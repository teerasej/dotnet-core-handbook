
# แสดงข้อมูลจาก Email List

หลังจากที่เราได้ข้อมูลจาก Graph Client แล้ว ก็จะนำมาแสดงใน console ตามตัวเลือกที่เราจะเพิ่มเข้ามาในแอพพลิเคชั่นครับ

- เสร็จแล้วให้รันทดสอบดู
- มีลองของ 2 รอบนะ สำหรับ practice นี้

```cs
using Microsoft.Extensions.Configuration;
using Microsoft.Graph;
using simple_graph_console;


//..

while (choice != 0)
{
    Console.WriteLine("Please choose one of the following options:");
    
    //..

    Console.WriteLine("---- Email ----");
    Console.WriteLine("3. List Emails");

    //..

    switch (choice)
    {
        //..

        case 3:
            // เรียกข้อมูล Email จาก Graph API
            var emails = await EmailHelper.GetEmailsAsync();
            for (int i = 0; i < emails.Count; i++)
            {
                Message message = emails[i];

                Console.WriteLine($"{i + 1}: message {message.Id}");
                Console.WriteLine($"   Sender: {message.Sender.EmailAddress.Name} ({message.Sender.EmailAddress.Address})");
                Console.WriteLine($"   Subject: {message.Subject}");
                Console.WriteLine("\n");
            }

            break;

        default:
           //..
    }
}

```

## 2. ลองของ

- เราสามารถเพิ่มปริมาณของ message ที่รีเควสมาในแต่ละรอบได้โดยการใช้ method `.Top()`
- เปิดไฟล์ **EmailHelper.cs** และลองแก้ไขคำสั่งเป็นแบบด้านล่าง

```cs
 public static async Task<IUserMessagesCollectionPage> GetEmailsAsync(int amount = 10)
        {
            try
            {
                return await graphClient.Me.Messages
                .Request()
                // กำหนดจำนวนตรงนี้
                .Top(amount)
                .GetAsync();
            }
            catch (ServiceException ex)
            {
                Console.WriteLine($"Error getting user's message: {ex.Message}");
                return null;
            }
        }
```

- จากนั้นก็ไปใส่จำนวนตอนเรียกใช้ในไฟล์ **Program.cs**

```cs
var emails = await EmailHelper.GetEmailsAsync(20);
```

## 3. ลองของอีกรอบ

- เราสามารถกำหนดให้ดึงเฉพาะข้อมูลที่ต้องการได้ด้วยนะ โดยการเรียกใช้ `.Select()` และกำหนดชื่อ field ของ resource นั้นๆ 
- อ้างอิงชื่อของ field property ได้จาก Graph API Documentation

```cs
graphClient.Me.Messages
    .Request()
    // ขอเฉพาะ field ที่ต้องการ
    .Select("id,sender")
    .Top(amount)
    .GetAsync();
```

ทดสอบรันแอพพลิเคชั่นดู จะเห็นว่า subject เป็นค่าว่างที่แสดงขึ้นมาใน console

## ไฟล์เต็ม Program.cs

```cs
// See https://aka.ms/new-console-template for more information
using Microsoft.Extensions.Configuration;
using Microsoft.Graph;
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

// Email Helper
EmailHelper.Initialize(GraphHelper.graphClient);


int choice = -1;

while (choice != 0)
{
    Console.WriteLine("Please choose one of the following options:");
    Console.WriteLine("0. Exit");
    Console.WriteLine("1. Display access token");
    Console.WriteLine("2. Hello, Me.");
    Console.WriteLine("---- Email ----");
    Console.WriteLine("3. List Emails");

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

        case 3:
            var emails = await EmailHelper.GetEmailsAsync();
            for (int i = 0; i < emails.Count; i++)
            {
                Message message = emails[i];

                Console.WriteLine($"{i + 1}: message {message.Id}");
                Console.WriteLine($"   Sender: {message.Sender.EmailAddress.Name} ({message.Sender.EmailAddress.Address})");
                Console.WriteLine($"   Subject: {message.Subject}");
                Console.WriteLine("\n");
            }

            break;

        default:
            Console.WriteLine("Invalid choice! Please try again.");
            break;
    }
}

```