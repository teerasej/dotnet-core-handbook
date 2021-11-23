

# Download Attachment and Upload to OneDrive

ส่วนนี้จะเป็นการลองประยุกต์ Message เข้ากับ OneDrive

> ขณะนี้ตัว Graph API ยังไม่มีส่วนที่โอนไฟล์จาก Attachment ของ Email เข้า OneDrive โดยตรง ถ้ามีแล้วจะมาอัพเดตใน https://www.facebook.com/nextflow นะ

## 1. การกำหนด scopes

ทำการรันคำสั่งใน Terminal เพื่อกำหนด scope สำหรับ Workshop นี้

```
dotnet user-secrets set scopes "User.Read;Mail.Read;Mail.ReadBasic;Files.Read;Files.Read.All;Files.ReadWrite;Files.ReadWrite.All"
```

## 2. สร้าง method สำหรับขอข้อมูล Email และ Attachment จาก Id 

เปิดไฟล์ EmailHelper.cs 

```cs
public static async Task<Message> GetMessageWithAttachmentAsync(string messageId)
{
    try
    {
        // Request ข้อมูลของ Message แบบระบุ Id
        return await graphClient.Me.Messages[messageId]
        .Request()
        // กำหนดขอข้อมูล Attachments ด้วย
        .Expand("attachments")
        .GetAsync();
    }
    catch (ServiceException ex)
    {
        Console.WriteLine($"Error getting message's attachment: {ex.Message}");
        return null;
    }
}
```

## 3. แก้ไขให้การอัพโหลดไฟล์ใน OneDrive รองรับไฟล์ขนาดใหญ่กว่า 4MB

เปิดไฟล์ **OneDriveHelper.cs** และแก้ไขด้านใน method `UploadFileAsync(string fileName)`

```cs
// นี่เป็น method เดิม แต่ตอนนี้เราจะทำเป็นการอัพโหลดไฟล์ที่รองรับขนาดไฟล์ที่ใหญ่กว่า 4 MB
public static async Task UploadFileAsync(string fileName)
{
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

## 4. เพิ่มตัวเลือก สำหรับการระบุ Email เพื่อดาวน์โหลดไฟล์ attachments และอัพโหลดไฟล์ไปที่ OneDrive

เปิดไฟล์ Program.cs และเพิ่มตัวเลือกลงไปในเมนู

- ก่อนการทดสอบการทำงาน ให้ส่ง Email พร้อมไฟล์แนบ 1 ไฟล์ไปที่ Account ที่ทดสอบ 

```cs
while (choice != 0)
{
    Console.WriteLine("Please choose one of the following options:");
    
    //..

    // เพิ่มตัวเลือกที่ 9
    Console.WriteLine("---- Mail to Drive ----");
    Console.WriteLine("9. Download attachment to OneDrive");

    //..

    switch (choice)
    {
        //...

        case 9:
            // อ่่าน message Id จาก console
            Console.WriteLine("Target Message Id:");
            var targetMessageId = Console.ReadLine();

            // ขอข้อมูล Message พร้อม Attachment
            var targetMessage = await EmailHelper.GetMessageWithAttachmentAsync(targetMessageId);
            

            if ((bool)targetMessage.HasAttachments)
            {
                Console.WriteLine($"   Attachments: {targetMessage.Attachments.Count}");

                foreach (var attachment in targetMessage.Attachments.CurrentPage)
                {
                    if(attachment is FileAttachment) 
                    {
                        // ดาวน์โหลด attachments มาเก็บไว้ในเครื่อง
                        var fileAttachment = attachment as FileAttachment;
                        System.IO.File.WriteAllBytes(fileAttachment.Name, fileAttachment.ContentBytes);
                        Console.WriteLine($"   Downloaded: {fileAttachment.Name}");

                        // อัพโหลดไปที่ OneDrive
                        Console.WriteLine($"        Uploading to OneDrive: {fileAttachment.Name}");
                        await OneDriveHelper.UploadFileAsync(fileAttachment.Name);
                        Console.WriteLine($"        Done.");
                    }
                }
            }
            else 
            {
                Console.WriteLine("Sorry, this email has no attachment.\n");
            }

            break;
            

        default:
            Console.WriteLine("Invalid choice! Please try again.");
            break;
    }
}
```