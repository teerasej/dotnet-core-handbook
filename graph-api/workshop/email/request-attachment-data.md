

# การเข้าถึง Attachment Files

- ส่วนใหญ่ ข้อมูลที่เราต้องการใช้งานจาก Email ก็คือไฟล์แนบ หรือที่เรียกว่า File Attachment 


## 1. มีไฟล์แนบอยู่นะ 

1. ให้ทดสอบส่ง Email แบบมีไฟล์รูปภาพแนบ มาที่ Account ที่ใช้ทดสอบกับ Microsoft Graph (แนะนำเป็น user ธรรมดาที่ไม่ใช่ admin เพราะจะมี email น้อยดูง่าย)
2. Sign in เข้าใช้งาน ด้วย Account ทดสอบที่ http://outlook.com/ 
3. เช็คให้แน่ใจว่า Email เข้ามาแล้ว และมีไฟล์แนบติดมาด้วย
4. ให้ทดสอบรันแบบ debug 
   1. วาง breakpoint เพื่อดดูภายใน Message ว่าสามารถดูข้อมูล file attachment ได้ไหม
   2. ดู `message.hasAttachments`
   3. ดู `message.Attachments`


## 2. ขอ file attachment มาด้วยสิ 

เราสามารถร้องขอข้อมูล file attachment มาพร้อมๆ กับการขอ message เลย 
หรือจะมาไล่ขอทีหลังก็ได้ 
ในที่นี้เราจะขอแบบแรก คือขอมาพร้อมกับ message

- เปิดไฟล์ **EmailHelper.cs** เราจะลุยแก้ตรงนี้กัน 

```cs
return await graphClient.Me.Messages.Request()
                .Top(amount)
                // ตรงนี้ล่ะที่เป็นการกำหนดว่าให้ดึงข้อมูลของ Attachments ติดมาด้วยเลย
                .Expand("attachments")
                .GetAsync();
```

## 3. ทดสอบอีกที

1. ให้ทดสอบรันแบบ debug 
   1. วาง breakpoint เพื่อดดูภายใน Message ว่าสามารถดูข้อมูล file attachment ได้ไหม
   2. ดู `message.hasAttachments`
   3. ดู `message.Attachments`


## 4. แสดงข้อมูลเบื้องต้นของไฟล์แนบใน Email 

เปิดไฟล์ Program.cs และเพิ่มส่วนของการแสดงข้อมูลไฟล์แนบเข้าไป

- เสร็จแล้วลองรันทดสอบดู

```cs
case 3:
    var emails = await EmailHelper.GetEmailsAsync(20);
    for (int i = 0; i < emails.Count; i++)
    {
        Message message = emails[i];

        Console.WriteLine($"{i + 1}: message {message.Id}");
        Console.WriteLine($"   Sender: {message.Sender.EmailAddress.Name} ({message.Sender.EmailAddress.Address})");
        Console.WriteLine($"   Subject: {message.Subject}");

        // เช็คว่ามีไฟล์แนบไหม
        if ((bool)message.HasAttachments)
        {
            // แสดงจำนวนไฟล์แนบ
            Console.WriteLine($"   Attachments: {message.Attachments.Count}");
        }

        Console.WriteLine("\n");
    }

    break;
```