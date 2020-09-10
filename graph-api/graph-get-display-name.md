
# การเรียกชื่อผู้ใช้ผ่าน Microsof Graph Client


## 1. เพิ่ม method สำหรับเรียกข้อมูลเกี่ยวกับผู้ใช้

`GraphServiceClient` สามารถเข้าถึง resource ต่างๆ ได้ในรูปแบบของ Property และ Method ทำให้ไม่ต้องนั่งกำหนด URL เอง

```c#
// GraphHelper.cs

public static async Task<User> GetMeAsync()
{
    try
    {
        return await graphClient.Me.Request().GetAsync();
    }
    catch (ServiceException ex)
    {
        Console.WriteLine($"Error getting signed-in user: {ex.Message}");
        return null;
    }
}
```


```c#
// GraphHelper.cs
using System;
using System.Threading.Tasks;
using Microsoft.Graph;

namespace GraphAPI_1
{
    public class GraphHelper
    {
        private static GraphServiceClient graphClient;

        public static void Initialize(IAuthenticationProvider authProvider)
        {
            graphClient = new GraphServiceClient(authProvider);
        }

        public static async Task<User> GetMeAsync()
        {
            try
            {
                return await graphClient.Me.Request().GetAsync();
            }
            catch (ServiceException ex)
            {
                Console.WriteLine($"Error getting signed-in user: {ex.Message}");
                return null;
            }
        }
    }
}

```


## 2. เรียกใช้ในโปรแกรม

เราจึงสามารถใช้ Class และ Interface ต่างๆ ได้ในโค้ดของเราเลย

```c#
// Program.cs

var user = GraphHelper.GetMeAsync().Result;
Console.WriteLine($"Welcome {user.DisplayName}\n");
```

หลังจากนี้ลองทดสอบการใช้งานดู

```c#
// Program.cs
using System;
using System.Threading.Tasks;
using Microsoft.Identity.Client;
using Flurl.Http;

namespace GraphAPI_1
{
    class Program
    {
        
        private static readonly string _clientId = "";
        static async Task Main(string[] args)
        {

            string[] scopes = new string[] { 
                "User.Read"
            };

            var authProvider = new AuthProvider(_clientId, scopes);

            GraphHelper.Initialize(authProvider);
            var user = GraphHelper.GetMeAsync().Result;
            Console.WriteLine($"Welcome {user.DisplayName}\n");

        }
    }
}

```