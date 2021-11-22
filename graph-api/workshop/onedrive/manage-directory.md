

# สร้าง Directory

## 1. สร้าง method สำหรับเรียกใช้ Graph API เพื่อสร้าง Folder

เปิดไฟล์ **OneDriveHelper.cs** และกำหนดกลไกในการสร้างโฟลเดอร์ใหม่

```cs
public static async Task<DriveItem> CreateNewFolderAsync(string name = "temp")
{
    // DriveItem เป็นตัวแทนของข้อมูลใน Onedrive
    var driveItem = new DriveItem
    {
        // กำหนดชื่อ
        Name = name,

        // การกำหนด Folder property จะถือว่า DriveItem นี้ เป็น folder
        Folder = new Folder
        {
        },

        // ส่วน conflictBehavior จะถูกใช้เมื่อมีกรณีชื่อของ Item ชนกับที่มีอยู่เดิม
        AdditionalData = new Dictionary<string, object>()
        {
            {"@microsoft.graph.conflictBehavior", "rename"}
        }
    };

    try
    {
        
        return await graphClient.Me.Drive.Root.Children
        .Request()
        // AddAsync() ในการสร้าง Item ใหม่
        .AddAsync(driveItem);
    }
    catch (ServiceException ex)
    {
        Console.WriteLine($"Error creating new folder in onedrive: {ex.Message}");
        return null;
    }
}
```

## 2. สร้างตัวเลือกใหม่ เพื่อกรอกข้อมูล และเอาไปใช้สร้าง Folder

เปิดไฟล์ Program.cs เพื่อสร้างตัวเลือกใหม่ในการสร้าง folder

- จากนั้นให้ทดสอบการทำงาน

```cs
while (choice != 0)
{
    Console.WriteLine("Please choose one of the following options:");
    
    //..

    Console.WriteLine("---- OneDrive ----");
    Console.WriteLine("5. List All Files");

    // เพิ่มตัวเลือกที่ 6
    Console.WriteLine("6. New Folder");

    try
    {
        choice = int.Parse(Console.ReadLine());
    }
    catch (System.FormatException)
    {
        choice = -1;
    }

    switch (choice)
    {
        //..

        case 6: 
            Console.WriteLine("Folder Name:");
            var folderName = Console.ReadLine();

            Console.WriteLine("Creating...");
            await OneDriveHelper.CreateNewFolderAsync(folderName);
            Console.WriteLine("Done!\n");
            break;

        //..
    }
}
```

## ลองของ (ระดับท้าทาย)

แน่นอนว่าเราสามารถสร้างได้แค่ 1 folder จากโฟลเดอร์ต้นทางของ OneDrive แต่จะทำยังไงถ้าลูกค้าต้องการสร้างแบบซ้อนล่ะ?!

เช่น 

- profiles/person/001 

เปลี่ยนโค้ดของเราให้สามารถสร้างแบบ nested folder ทีเดียวแบบนี้ได้มั้ย?
