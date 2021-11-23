
# สร้าง Event

## 1. เขียน method การสร้าง Event ใน CalendarHelper.cs

```cs
public static async Task<Event> CreateEvent(string subject, string content, string location)
        {
            try
            {
                // ในที่นี้ถือว่า timezone ของเครื่องอยู่ใน South East Asia 

                // กำหนดเวลาเริ่มเป็น 1 ชั่วโมงถัดจากเวลาที่สร้าง Event
                var startTime = DateTime.Now.AddHours(1).ToString();
                // กำหนดเวลาจบเป็น 1 ชั่วโมงถัดจากเวลาที่เริ่ม Event
                var endTime = DateTime.Now.AddHours(2).ToString();

                var @event = new Event
                {
                    // กำหนดชื่อ Event
                    Subject = subject,
                    // กำหนดเนื้อหา Event
                    Body = new ItemBody
                    {
                        ContentType = BodyType.Html,
                        Content = content
                    },
                    // กำหนดเวลาเริ่ม
                    Start = new DateTimeTimeZone
                    {
                        DateTime = startTime,
                        TimeZone = "SE Asia Standard Time"
                    },
                    // กำหนดเวลาสิ้นสุด
                    End = new DateTimeTimeZone
                    {
                        // DateTime = "2017-04-15T14:00:00",
                        DateTime = endTime,
                        TimeZone = "SE Asia Standard Time"
                    },
                    // กำหนดชื่อสถานที่
                    Location = new Location
                    {
                        DisplayName = location
                    },
                    AllowNewTimeProposals = true,
                };

                // เพิ่มโดยกำหนดใช้ใช้ timezone ของ outlook เป็น South East Asia
                // ดูชื่อ timezone ต่างๆ ได้ที่นี่ https://www.ge.com/digital/documentation/meridium/V36160/Help/Master/Subsystems/AssetPortal/Content/Time_Zone_Mappings.htm
                return await graphClient.Me.Events
                    .Request()
                    .Header("Prefer", "outlook.timezone=\"SE Asia Standard Time\"")
                    .AddAsync(@event);

            }
            catch (ServiceException ex)
            {
                Console.WriteLine($"Error creating calendar: {ex.Message}");
                return null;
            }
        }
    }
```

## 2. เพิ่มตัวเลือกในการกรอกข้อมูลสร้าง Event ใน Program.cs

เสร็จแล้วทดสอบการทำงาน

```cs
while (choice != 0)
{
    Console.WriteLine("Please choose one of the following options:");
    
    //...

    // สร้างตัวเลือกเพิ่มใน menu

    Console.WriteLine("---- Calendar ----");
    Console.WriteLine("12. Create Event");

    //...

    switch (choice)
    {
        //...
            
        case 12:
            Console.WriteLine("Event Name:");
            var eventName = Console.ReadLine();

            Console.WriteLine("Event Description:");
            var eventDescription = Console.ReadLine();

            Console.WriteLine("Location name:");
            var locationName = Console.ReadLine();

            Console.WriteLine("     Creating Event...");
            var createdEvent = await CalendarHelper.CreateEvent(eventName, eventDescription, locationName);
            Console.WriteLine("     Done.");
            break;

        default:
            Console.WriteLine("Invalid choice! Please try again.");
            break;
    }
}
```