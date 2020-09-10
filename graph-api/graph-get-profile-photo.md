

# การบันทึกภาพโปรไฟล์ผ่าน Microsoft Graph Client


## 1. เพิ่ม method สำหรับเรียกข้อมูลรูปภาพ และ Stream

[อ้างอิง](https://docs.microsoft.com/en-us/graph/api/profilephoto-get?view=graph-rest-1.0)

```c#
// GraphHelper.cs

using System.IO;

public static async Task<Stream> GetUserPhotoContentAsync()
{
    try
    {
        return await graphClient.Me.Photo.Content.Request().GetAsync();
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

```c#
// Program.cs
using System.Drawing;
using System.IO;


// แสดงข้อมูลภาพกว้างยาวของภาพ
var userPhoto = GraphHelper.GetUserPhotoAsync().Result;
Console.WriteLine($"My Photo Infomation: {userPhoto.Width} x {userPhoto.Height}");

// บันทึกไฟล์ stream ไว้ในเครื่อง
var photoStream = GraphHelper.GetUserPhotoContentAsync().Result;
string path = Path.Combine("", "photo.jpg");
using (FileStream outputFileStream = new FileStream(path, FileMode.Create))
{
    photoStream.CopyTo(outputFileStream);
}
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

            // แสดงข้อมูลภาพกว้างยาวของภาพ
            var userPhoto = GraphHelper.GetUserPhotoAsync().Result;
            Console.WriteLine($"My Photo Infomation: {userPhoto}");

            // บันทึกไฟล์ stream ไว้ในเครื่อง
            var photoStream = GraphHelper.GetUserPhotoContentAsync().Result;
            string path = Path.Combine("", "photo.jpg");
            using (FileStream outputFileStream = new FileStream(path, FileMode.Create))
            {
                photoStream.CopyTo(outputFileStream);
            }
        }
    }
}

```