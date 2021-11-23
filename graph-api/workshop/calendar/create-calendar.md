
# สร้าง Calendar

## 1. เขียน method การสร้าง Calendar ใน CalendarHelper.cs

```cs

public static async Task<Calendar> CreateCalendar(string calendarName)
{
    try
    {
        // กำหนดค่าให้ Calendar object
        var calendar = new Calendar
        {
            Name = calendarName
        };

        // request เพื่อเพิ่ม Calendar 
        return await graphClient.Me.Calendars
            .Request()
            .AddAsync(calendar);

    }
    catch (ServiceException ex)
    {
        Console.WriteLine($"Error creating calendar: {ex.Message}");
        return null;
    }
}
```

## 2. เพิ่มตัวเลือกในการกรอกข้อมูลสร้าง Calendar ใน Program.cs

เสร็จแล้วทดสอบการทำงาน

```cs
while (choice != 0)
{
    Console.WriteLine("Please choose one of the following options:");
    
    //...

    // สร้างตัวเลือกเพิ่มใน menu

    Console.WriteLine("---- Calendar ----");
    Console.WriteLine("11. Create Calendar");

    //...

    switch (choice)
    {
        //...
            
        case 11:

            Console.WriteLine("Calendar name:");
            var calendarName = Console.ReadLine();

            Console.WriteLine("     Creating Calendar...");
            var createdCalendar = await CalendarHelper.CreateCalendar(calendarName);
            Console.WriteLine("     Done.");

            break;

        default:
            Console.WriteLine("Invalid choice! Please try again.");
            break;
    }
}
```