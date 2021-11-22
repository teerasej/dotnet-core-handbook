

# การขอรายการ Email

อย่างแรกเราจะขอรายการ Email ของผู้ใช้ที่เข้าใช้งานระบบของเรา 

- Email ใน outlook จะถูกเรียกว่า message 
- จะมีการจำกัดปริมาณของ message ที่ดึงมาอยู่ที่ 10 แต่ก็ขึ้นกับการ request ของเรา

## 1. สร้าง method สำหรับร้องขอ Messages

เปิดไฟล์ EmailHelper.cs

```cs
//..
namespace simple_graph_console
{
    public class EmailHelper
    {
        //..

        // สร้าง method สำหรับร้องขอข้อมูล Messages
        public static async Task<IUserMessagesCollectionPage> GetEmailsAsync()
        {
            try
            {
                return await graphClient.Me.Messages.Request().GetAsync();
            }
            catch (ServiceException ex)
            {
                Console.WriteLine($"Error getting user's message: {ex.Message}");
                return null;
            }
        }
    }
}
```



## ไฟล์เต็ม EmailHelper.cs

```cs
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

        public static async Task<IUserMessagesCollectionPage> GetEmailsAsync()
        {
            try
            {
                return await graphClient.Me.Messages.Request().GetAsync();
            }
            catch (ServiceException ex)
            {
                Console.WriteLine($"Error getting user's message: {ex.Message}");
                return null;
            }
        }
    }
}
```