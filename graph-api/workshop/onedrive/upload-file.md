
# Upload File


# 1. สร้าง method สำหรับเรียกใช้ Graph API เพื่ออัพโหลดไฟล์

เปิดไฟล์ **OneDriveHelper.cs** 

```cs
public static async Task UploadFileAsync(string fileName)
{
    try
    {
        // อ่านไฟล์ และแปลงเป็น stream
        byte[] data = System.IO.File.ReadAllBytes(fileName);
        Stream stream = new MemoryStream(data);
        
        // อัพโหลดไปที่ Root โดยการระบุชื่อไฟล์ และ stream
        await graphClient.Me
        .Drive
        .Root
        .ItemWithPath(fileName)
        .Content
        .Request()
        .PutAsync<DriveItem>(stream);
    }
    catch (ServiceException ex)
    {
        Console.WriteLine($"Error uploading file to onedrive: {ex.Message}");
    }

}
```

## 2. สร้างตัวเลือกใหม่ เพื่อกรอกข้อมูล เพื่ออัพโหลดไฟล์

เปิดไฟล์ Program.cs และเพิ่มการทำงานลงไป

- จากนั้นให้ทดสอบการทำงาน

```cs
while (choice != 0)
{
    //..
    Console.WriteLine("---- OneDrive ----");
    Console.WriteLine("5. List All Files");
    Console.WriteLine("6. New Folder");
    Console.WriteLine("7. Download a file");

    // เพิ่มตัวเลือกที่ 8
    Console.WriteLine("8. Upload a file");

    //..

    switch (choice)
    {
        

        //..
        
        // กำหนดให้กรอกชื่อไฟล์และนามสกุล โดยในที่นี้ให้เอาไฟล์มาวางไว้ในโฟลเดอร์ของโปรเจค
        case 8: 
            Console.WriteLine("file name to upload (put file in project's root only):");
            var fileName = Console.ReadLine();

            Console.WriteLine("Uploading...");
            await OneDriveHelper.UploadFileAsync(fileName);
            Console.WriteLine("Done!\n");
            break;

        default:
            Console.WriteLine("Invalid choice! Please try again.");
            break;
    }
}
```