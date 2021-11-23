
# Upload File


# 1. สร้าง method สำหรับเรียกใช้ Graph API เพื่ออัพโหลดไฟล์

> วิธีนี้ใช้ได้กับไฟล์ขนาดไม่เกิน 4 MB

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

## 3. ถ้าต้องการใช้กับไฟล์ขนาดใหญ่กว่า 4 MB จะเป็นไปตามโค้ดด้านล่างนี้ 

```cs
public static async Task UploadFileAsync(string fileName)
{
    // Use for file size not larger than 4 mb

    // try
    // {
    //     byte[] data = System.IO.File.ReadAllBytes(fileName);
    //     Stream stream = new MemoryStream(data);

    //     await graphClient.Me
    //     .Drive
    //     .Root
    //     .ItemWithPath(fileName)
    //     .Content
    //     .Request()
    //     .PutAsync<DriveItem>(stream);

    // }
    // catch (ServiceException ex)
    // {
    //     Console.WriteLine($"Error uploading file to onedrive: {ex.Message}");
    // }

    // เปิด Filestream ตามชื่อไฟล์ที่ได้มา
    using (var fileStream = System.IO.File.OpenRead(fileName))
    {
        // กำหนดเงื่อนไขว่าถ้าเจอไฟล์ชื่อซ้ำกัน ให้แทนที่ไฟล์เดิมไปเลย
        var uploadProps = new DriveItemUploadableProperties
        {
            ODataType = null,
            AdditionalData = new Dictionary<string, object>
            {
                { "@microsoft.graph.conflictBehavior", "replace" }
            }
        };

        // สร้าง upload session
        var uploadSession = await graphClient.Me.Drive.Root
            .ItemWithPath(fileName)
            .CreateUploadSession(uploadProps)
            .Request()
            .PostAsync();

        // แบ่งขนาดไฟล์
        int maxSliceSize = 320 * 1024;
        var fileUploadTask =
            new LargeFileUploadTask<DriveItem>(uploadSession, fileStream, maxSliceSize);

        // สร้าง function ที่จะทำงานขณะที่การอัพโหลดดำเดินไป
        IProgress<long> progress = new Progress<long>(prog =>
        {
            Console.WriteLine($"        Uploaded {prog} bytes of {fileStream.Length} bytes");
        });

        try
        {
            // อัพโหลดไฟล์
            var uploadResult = await fileUploadTask.UploadAsync(progress);

            // เช็คว่าการอัพโหลดเสร็จสมบูรณ์ไหม
            if (uploadResult.UploadSucceeded)
            {
                // ถ้าอัพโหลดสมบูรณ์ ก็สามารถดึง id ของ item มาแสดงได้
                Console.WriteLine($"        Upload complete, item ID: {uploadResult.ItemResponse.Id}");
            }
            else
            {
                Console.WriteLine("         Upload failed");
            }
        }
        catch (ServiceException ex)
        {
            Console.WriteLine($"        Error uploading: {ex.ToString()}");
        }
    }

}
```