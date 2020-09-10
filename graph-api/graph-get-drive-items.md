

# การแสดงข้อมูลของ Drive ผู้ใช้ ผ่าน Microsoft Graph Client

## 1. เพิ่ม method สำหรับเรียกข้อมูลเกี่ยวกับผู้ใช้

[อ้างอิง](https://docs.microsoft.com/en-us/graph/api/resources/drive?view=graph-rest-1.0)

```c#
// GraphHelper.cs

public static async Task<IDriveItemChildrenCollectionPage> GetUserDriveItems()
        {
            try
            {
                // return await graphClient.Me.Drive.Items.Request().GetAsync();
                return await graphClient.Me.Drive.Root.Children.Request().GetAsync();
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
using System.IO;
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

        public static async Task<ProfilePhoto> GetUserPhotoAsync()
        {
            try
            {
                return await graphClient.Me.Photo.Request().GetAsync();
            }
            catch (ServiceException ex)
            {
                Console.WriteLine($"Error getting signed-in user: {ex.Message}");
                return null;
            }
        }

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

        public static async Task<IDriveItemChildrenCollectionPage> GetUserDriveItems()
        {
            try
            {
                // return await graphClient.Me.Drive.Items.Request().GetAsync();
                return await graphClient.Me.Drive.Root.Children.Request().GetAsync();
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

var driveItems = GraphHelper.GetUserDriveItems().Result;
Console.WriteLine($"Items in Drive: {driveItems.Count}");
```

```c#
// Program.cs

using System;
using System.Drawing;
using System.Threading.Tasks;
using Microsoft.Identity.Client;
using Flurl.Http;
using System.IO;

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

            var userPhoto = GraphHelper.GetUserPhotoAsync().Result;
            Console.WriteLine($"My Photo Infomation: {userPhoto.Width} x {userPhoto.Height}");

            var photoStream = GraphHelper.GetUserPhotoContentAsync().Result;
            string path = Path.Combine("", "photo.jpg");
            using (FileStream outputFileStream = new FileStream(path, FileMode.Create))
            {
                photoStream.CopyTo(outputFileStream);
            }

            var driveItems = GraphHelper.GetUserDriveItems().Result;
            Console.WriteLine($"Items in Drive: {driveItems.Count}");
        }
    }
}

```