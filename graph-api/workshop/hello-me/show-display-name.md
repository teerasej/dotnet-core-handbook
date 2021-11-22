
# 

จากไฟล์ GraphHelper.cs เราจะเพิ่ม method เข้าไปเพื่อขอข้อมูลของ User 

```cs

using Azure.Core;
using Azure.Identity;
using Microsoft.Graph;

namespace simple_graph_console
{
    public class GraphHelper
    {
        //...

        public static async Task<User> GetMeAsync()
        {
            try
            {
                // Graph client จะมี property ตั้งชื่อล้อไปตามชื่อของ Resource ใน Microsoft Graph API 
                // ในที่นี้คือ .Me
                // เราสามารถเรียก .Request() เพื่อขอข้อมูลได้
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

## 2. เรียกใช้คำสั่งในแอพพลิเคชั่น 

เราจะทำการเพิ่มขั้นตอนการนำชื่อมาแสดงในแอพพลิเคชั่น โดยกำหนดเป็นคำสั่งที่ 2

```cs
while (choice != 0)
{
    Console.WriteLine("Please choose one of the following options:");
    Console.WriteLine("0. Exit");
    Console.WriteLine("1. Display access token");

    // เพิ่งตัวเลือกคำสั่งในเมนู
    Console.WriteLine("2. Hello, Me.");

    try
    {
        choice = int.Parse(Console.ReadLine());
    }
    catch (System.FormatException)
    {
        choice = -1;
    }

    switch (choice)
    {
        case 0:
            Console.WriteLine("Goodbye...");
            break;
        case 1:
            Console.WriteLine($"Access token: {accessToken}\n");
            break;
        case 2:
            // เรียกใช้ GetMeAsync() เพื่อนำ User object ที่ได้มาใช้งาน
            var user = await GraphHelper.GetMeAsync();

            // แสดง display name
            Console.WriteLine($"Hi, {user.DisplayName}\n");
            break;
        default:
            Console.WriteLine("Invalid choice! Please try again.");
            break;
    }
}
```

## 3. ทดสอบการทำงาน

ทดสอบรันแอพพลิเคชั่น, sign in, และเรียกคำสั่ง **Hello, Me**

## 4. ลองของ

ทดสอบการทำงานของแอพพลิเคชั่นใน debug mode แต่คราวนี้ให้ลองเลือก User คนอื่นเป็นคนล๊อคอิน 

โดยให้สังเกต

1. หน้าต่าง consent windows มีอะไรแตกต่างจากปกติไหม?
2. ถ้าวาง breakpoint ไว้ท้ายคำสั่ง break ของ case 2 เพื่อดู User object ว่ามีข้อมูลอะไรมากกว่าปกติหรือเปล่า?

## ไฟล์เต็ม Program.cs

```cs
// See https://aka.ms/new-console-template for more information
using Microsoft.Extensions.Configuration;
using simple_graph_console;



var appConfig = new ConfigurationBuilder()
        .AddUserSecrets<Program>()
        .Build();

if (string.IsNullOrEmpty(appConfig["appId"]) ||
    string.IsNullOrEmpty(appConfig["scopes"]))
{
    Console.WriteLine("Missing or invalid appsettings.json...exiting\n");
    return;
}

var appId = appConfig["appId"];
var scopesString = appConfig["scopes"];
var scopes = scopesString.Split(';');

GraphHelper.Initialize(appId, scopes, (code, cancellation) =>
{
    Console.WriteLine(code.Message);
    return Task.FromResult(0);
});

var accessToken = GraphHelper.GetAccessTokenAsync(scopes).Result;
Console.WriteLine("Signed In...\n");
Console.WriteLine($"Access token: {accessToken}\n");
int choice = -1;

while (choice != 0)
{
    Console.WriteLine("Please choose one of the following options:");
    Console.WriteLine("0. Exit");
    Console.WriteLine("1. Display access token");
    Console.WriteLine("2. Hello, Me.");

    try
    {
        choice = int.Parse(Console.ReadLine());
    }
    catch (System.FormatException)
    {
        choice = -1;
    }

    switch (choice)
    {
        case 0:
            Console.WriteLine("Goodbye...");
            break;
        case 1:
            Console.WriteLine($"Access token: {accessToken}\n");
            break;
        case 2:
            var user = await GraphHelper.GetMeAsync();
            Console.WriteLine($"Hi, {user.DisplayName}\n");
            break;
        default:
            Console.WriteLine("Invalid choice! Please try again.");
            break;
    }
}

```

## ไฟล์เต็ม GraphHelper.cs

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Azure.Core;
using Azure.Identity;
using Microsoft.Graph;
using TimeZoneConverter;

namespace simple_graph_console
{
    public class GraphHelper
    {
        private static DeviceCodeCredential tokenCredential;
        private static GraphServiceClient graphClient;

        public static void Initialize(string clientId,
                                      string[] scopes,
                                      Func<DeviceCodeInfo, CancellationToken, Task> callBack)
        {
            tokenCredential = new DeviceCodeCredential(callBack, clientId);
            graphClient = new GraphServiceClient(tokenCredential, scopes);
        }

        public static async Task<string> GetAccessTokenAsync(string[] scopes)
        {
            var context = new TokenRequestContext(scopes);
            var response = await tokenCredential.GetTokenAsync(context);
            return response.Token;
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