
# สร้าง Team 

## 1. เขียน method การสร้าง team ใน TeamHelper.cs

```cs

// รับชื่อ Team และ Description 
public static async Task<Team> CreateTeamAsync(string teamName, string description) 
{   
    // กำหนดข้อมูลของ Team ที่จะสร้างขึ้นมาใหม่
    var newTeam = new Team
    {
        DisplayName = teamName,
        Description = description,

        // กำหนดใช้ template แบบมาตรฐาน
        AdditionalData = new Dictionary<string, object>()
        {
            {"template@odata.bind", "https://graph.microsoft.com/v1.0/teamsTemplates('standard')"}
        },
    };

    try
    {
        // สร้างเพิ่มข้อมูล Team ใหม่เข้าไป
        return await graphClient.Teams.Request().AddAsync(newTeam);
    }
    catch (ServiceException ex)
    {
        Console.WriteLine($"Error creating team: {ex.Message}");
        return null;
    }
}
```

## 2. เพิ่มตัวเลือกในการกรอกข้อมูลสร้าง Team ใน Program.cs

เสร็จแล้วทดสอบการทำงาน

```cs
while (choice != 0)
{
    Console.WriteLine("Please choose one of the following options:");
    
    //...

    // สร้างตัวเลือกเพิ่มใน menu

    Console.WriteLine("---- Team ----");
    Console.WriteLine("10. Create Team");

    //...

    switch (choice)
    {
        //...
            
        case 10:
            // รับชื่อ Team
            Console.WriteLine("Team Name:");
            var teamName = Console.ReadLine();

            // รับรายละเอียดของ Team
            Console.WriteLine("Team Description:");
            var teamDescription = Console.ReadLine();


            // สั่งสร้าง Team
            Console.WriteLine("     Creating team...");
            var createdTeam = await TeamHelper.CreateTeamAsync(teamName, teamDescription);
            Console.WriteLine("     Done.");
            break;

        default:
            Console.WriteLine("Invalid choice! Please try again.");
            break;
    }
}
```