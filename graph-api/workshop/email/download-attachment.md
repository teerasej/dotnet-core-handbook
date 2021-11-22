

# การดาวน์โหลด Attachment Files

- เปิดไฟล์ Program.cs เราจะเพิ่มส่วนของ**การบันทึกไฟล์จาก email ทุกฉบับ ลงเครื่อง** (ว้าว)
- จากนั้นให้ทดสอบดู 
- จะเห็นว่า ถ้าเป็นพวก contact ที่ไม่ใช่ไฟล์ จะข้ามไปเลย

```cs
using System.IO;

case 3:
    var emails = await EmailHelper.GetEmailsAsync(20);
    for (int i = 0; i < emails.Count; i++)
    {
        Message message = emails[i];

        Console.WriteLine($"{i + 1}: message {message.Id}");
        Console.WriteLine($"   Sender: {message.Sender.EmailAddress.Name} ({message.Sender.EmailAddress.Address})");
        Console.WriteLine($"   Subject: {message.Subject}");


        if ((bool)message.HasAttachments)
        {
            Console.WriteLine($"   Attachments: {message.Attachments.Count}");

            // เอา Attachments มาวนดู
            foreach (var attachment in message.Attachments.CurrentPage)
            {
                // ถ้าเป็นประเภทไฟล์ ให้ดาวน์โหลดลงเครื่อง
                if(attachment is FileAttachment) {

                    // attachment ใน graph มีหลายแบบ จะมีเฉพาะ FileAttachment เท่านั้นถึงจะมี .ContentBytes สำหรับเขียนลงเป็นไฟล์
                    var fileAttachment = attachment as FileAttachment;
                    System.IO.File.WriteAllBytes(fileAttachment.Name, fileAttachment.ContentBytes);
                }
            }
        }

        Console.WriteLine("\n");
    }

    break;
```