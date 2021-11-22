
# สร้าง method ในการสร้าง Message (Email)


- เปิดไฟล์ GraphHelper.cs และเพิ่ม Method เข้าไป 
- ในที่นี้จะมีการเรียกใช้ Class ชื่อ **Message**, **Recipient** และ **BodyItem** ของ **Microsoft.Graph** ด้วย


```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.Graph;

namespace simple_graph_console
{
    public class EmailHelper
    {
        //..

        public static async Task SendSimpleEmailAsync(string recipientAddress, string subject, string content)
        {
            try
            {
                // Message class ตัวแทนของข้อความที่ต้องการส่ง
                var message = new Message
                {
                    // กำหนดหัว Email
                    Subject = subject,

                    // กำหนดตัวเนื้อหาของ Email (Body)
                    Body = new ItemBody
                    {
                        ContentType = BodyType.Text,
                        Content = content
                    },

                    // กำหนดผู้รับ สามารถกำหนดได้หลายคน
                    ToRecipients = new List<Recipient>()
                    {
                        // 1 Recpient
                        new Recipient
                        {
                            EmailAddress = new EmailAddress
                            {
                                Address = recipientAddress
                            }
                        }
                    }
                };
                
                    
                var saveToSentItems = true;

                await graphClient.Me
                    .SendMail(message, saveToSentItems)
                    .Request()
                    .PostAsync();
            }
            catch (ServiceException ex)
            {
                Console.WriteLine($"Error sending message: {ex.Message}");
            }
        }
    }
}
```

## ไฟล์เต็ม 

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.Graph;

namespace simple_graph_console
{
    public class EmailHelper
    {
        private static GraphServiceClient graphClient;

        public static void Initialize(GraphServiceClient client)
        {
            graphClient = client;
        }

        public static async Task<IUserMessagesCollectionPage> GetEmailsAsync(int amount = 10)
        {
            try
            {
                // return await graphClient.Me.Messages.Request().Select("id,sender").Top(amount).GetAsync();
                return await graphClient.Me.Messages.Request()
                .Top(amount)
                .Expand("attachments")
                .GetAsync();
            }
            catch (ServiceException ex)
            {
                Console.WriteLine($"Error getting user's message: {ex.Message}");
                return null;
            }
        }

        public static async Task<Attachment> GetAttachmentAsync(string messageId, string attachmentId)
        {
            try
            {
                // return await graphClient.Me.Messages.Request().Select("id,sender").Top(amount).GetAsync();
                return await graphClient.Me.Messages[messageId].Attachments[attachmentId]
                .Request()
                .GetAsync();
            }
            catch (ServiceException ex)
            {
                Console.WriteLine($"Error getting message's attachment: {ex.Message}");
                return null;
            }
        }

        public static async Task SendSimpleEmailAsync(string recipientAddress, string subject, string content)
        {
            try
            {
                var message = new Message
                {
                    Subject = subject,
                    Body = new ItemBody
                    {
                        ContentType = BodyType.Text,
                        Content = content
                    },
                    ToRecipients = new List<Recipient>()
                    {
                        new Recipient
                        {
                            EmailAddress = new EmailAddress
                            {
                                Address = recipientAddress
                            }
                        }
                    }
                };
                

                var saveToSentItems = true;

                await graphClient.Me
                    .SendMail(message, saveToSentItems)
                    .Request()
                    .PostAsync();
            }
            catch (ServiceException ex)
            {
                Console.WriteLine($"Error sending message: {ex.Message}");
            }
        }
    }
}
```