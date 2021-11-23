
# สร้าง Channel 

## 1. เพิ่มรายละเอียดในการสร้าง Channel ใน TeamHelper.cs

```cs

// ปรับให้สามารถรับชื่อ Channel ได้
public static async Task<Team> CreateTeamAsync(string teamName, string description, string channelName) 
{
    var newTeam = new Team
    {
        DisplayName = teamName,
        Description = description,
        AdditionalData = new Dictionary<string, object>()
        {
            {"template@odata.bind", "https://graph.microsoft.com/v1.0/teamsTemplates('standard')"}
        },
        // กำหนด Channel ที่จะถูกสร้างขึ้นมาพร้อมกับ Team
        Channels = new TeamChannelsCollectionPage
        {   
            // กำหนดคุณสมบัติของ Channel
            new Channel
            {
                DisplayName = channelName
            }
        }
        
    };

    try
    {
        return await graphClient.Teams.Request().AddAsync(newTeam);
    }
    catch (ServiceException ex)
    {
        Console.WriteLine($"Error creating team: {ex.Message}");
        return null;
    }
}
```

## 2. เพิ่มในส่วนรับชื่อ channel ใน Program.cs 

เสร็จแล้วทดสอบการทำงาน

```cs
case 10:
    Console.WriteLine("Team Name:");
    var teamName = Console.ReadLine();

    Console.WriteLine("Team Description:");
    var teamDescription = Console.ReadLine();

    // เพิ่มส่วนรับชื่อ Channel
    Console.WriteLine("Addition Channel name:");
    var channelName = Console.ReadLine();

    Console.WriteLine("     Creating team...");
    // กำหนดชื่อ Channel ลงไปในขั้นตอนสร้าง Team
    var createdTeam = await TeamHelper.CreateTeamAsync(teamName, teamDescription, channelName);
    Console.WriteLine("     Creating channel...");
    Console.WriteLine("     Done.");
    break;
```