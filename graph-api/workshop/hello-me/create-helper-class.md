
# สร้าง Class สำหรับจัดการ Sign in และขอ token จาก Azure AD

สร้างไฟล์ใหม่ชื่อ **GraphHelper.cs** และเขียนโค้ดด้านล่างลงไป

```cs
using Azure.Core;
using Azure.Identity;
using Microsoft.Graph;

namespace simple_graph_console
{
    public class GraphHelper
    {
        // ประเภทของ Credential ที่ใช้ ในที่นี้ใช้เป็น device code ตามที่กำหนดไว้ใน Authentication ของ App Registration
        private static DeviceCodeCredential tokenCredential;

        // ตัวนี้แหละสำคัญ ใช้ในการเข้าถึงข้อมูล และการ Authenticate
        private static GraphServiceClient graphClient;

        // สร้าง method ที่ทำการกำหนดค่าต่างๆ ให้พร้อมสำหรับ graph client
        public static void Initialize(string clientId,
                                      string[] scopes,
                                      Func<DeviceCodeInfo, CancellationToken, Task> callBack)
        {
            tokenCredential = new DeviceCodeCredential(callBack, clientId);
            graphClient = new GraphServiceClient(tokenCredential, scopes);
        }

        // การขอ token ส่วนนี้จะเป็นการเริ่ม authentication
        public static async Task<string> GetAccessTokenAsync(string[] scopes)
        {
            var context = new TokenRequestContext(scopes);
            var response = await tokenCredential.GetTokenAsync(context);
            return response.Token;
        }
    }
}
```

- ในที่นี้เราใช้ `DeviceCodeCredential` ให้สอดคล้องกับการตั้งค่าใน Azure AD ถ้าเป็น platform อื่น เราสามารถใช้ Credential ในรูปแบบอื่นๆ ได้ 