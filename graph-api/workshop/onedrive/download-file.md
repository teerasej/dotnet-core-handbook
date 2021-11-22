# Download File

# 1. สร้าง method สำหรับเรียกใช้ Graph API เพื่อดาวน์โหลดไฟล์

เปิดไฟล์ **OneDriveHelper.cs** 

```cs
public static async Task DownloadFileAsync(string driveItemId)
{
    try
    {
        // เข้าถึงตัว DriveItem ที่ต้องการ
        var driveItem = await graphClient.Me.Drive.Items[driveItemId].Request().GetAsync();

        // เข้าถึง Content ของ DriveItem เพื่อใช้ stream
        var stream = await graphClient.Me.Drive.Items[driveItemId].Content.Request().GetAsync();

        // บันทึก Stream เป็นไฟล์
        using (var fileStream = System.IO.File.Create(driveItem.Name))
        {
            stream.Seek(0, SeekOrigin.Begin);
            stream.CopyTo(fileStream);
        }
    }
    catch (ServiceException ex)
    {
        Console.WriteLine($"Error downloading a file: {ex.Message}");
    }

}
```

## 2. สร้างตัวเลือกใหม่ เพื่อกรอกข้อมูล เพื่อดาวน์โหลด DriveItem

เปิดไฟล์ Program.cs และเพิ่มการทำงานลงไป

- จากนั้นให้ทดสอบการทำงาน

```cs
while (choice != 0)
{
    //..
    Console.WriteLine("---- OneDrive ----");
    Console.WriteLine("5. List All Files");
    Console.WriteLine("6. New Folder");

    // เพิ่มตัวเลือกที่ 7
    Console.WriteLine("7. Download a file");

    //..

    switch (choice)
    {
        

        case 5:
            
            //..

            for (int i = 0; i < onedriveItems.Count; i++)
            {
                
                //..

                Console.WriteLine($"{i + 1}: ({itemType}) {driveItem.Name}");

                // ปรับให้แสดง drive Item ID เพื่อคัดลอกเอาไปใช้ในการดาวน์โหลด
                Console.WriteLine($"    id: {driveItem.Id}");
            }

            Console.WriteLine("\n");
            break;

        //..
        
        // กำหนดให้กรอก drive item ID เพื่อใช้ในการดาวน์โหลด
        case 7: 
            Console.WriteLine("Item Id:");
            var itemId = Console.ReadLine();

            Console.WriteLine("Downloading...");
            await OneDriveHelper.DownloadFileAsync(itemId);
            Console.WriteLine("Done!\n");
            break;

        default:
            Console.WriteLine("Invalid choice! Please try again.");
            break;
    }
}
```