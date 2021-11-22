
# ทำการ Sign in และทดสอบขอ Token ตอนเริ่มการทำงานของแอพพลิเคชั่น

## 1. อ่านข้อมูลจาก Secret store

ด้านบนสุดของไฟล์ **Program.cs** เราจะทำการเขียนคำสั่ง เพื่อดึงข้อมูลจาก Secret file ขึ้นมาใช้ในแอพพลิเคชั่น

- เสร็จแล้วเราสามารถลอง debug เพื่อดูค่าที่อ่านขึ้นมาจาก Secret ได้

> อย่าลืมว่าการจะทดสอบแบบ debug session ที่สามารถรับค่าจาก terminal ได้ [ต้องมีการแก้ไขค่าบางอย่างในไฟล์ launch.json ก่อน](init-project.md) 

```cs
using Microsoft.Extensions.Configuration;

var appConfig = new ConfigurationBuilder()
        // ทำการโหลด user secret ที่สร้างไว้ก่อนหน้านี้มา 
        // method นี้ทำงานกับ package UserSecrets โดยตรง
        .AddUserSecrets<Program>()
        .Build();

// ถ้าไม่มี appId หรือ scope ให้หยุดการทำงาน
if (string.IsNullOrEmpty(appConfig["appId"]) ||
    string.IsNullOrEmpty(appConfig["scopes"]))
{
    Console.WriteLine("Missing or invalid appsettings.json...exiting");
    return;
}

// เตรียมข้อมูลสำหรับการเชื่อมต่อกับ Microsoft Graph API
var appId = appConfig["appId"];
var scopesString = appConfig["scopes"];
var scopes = scopesString.Split(';');

// ...
```



## 2. เริ่มการทำงานของ Graph Client 

ถัดลงมา เราจะเรียกใช้ method **GraphHelper.Initialize** 

```cs
//...

var appId = appConfig["appId"];
var scopesString = appConfig["scopes"];
var scopes = scopesString.Split(';');

// กำหนด appId, scopes, และ callback function เพื่อ setup การทำงานแบบที่เตรียมไว้
GraphHelper.Initialize(appId, scopes, (code, cancellation) =>
{
    Console.WriteLine(code.Message);
    return Task.FromResult(0);
});

// ขอ token มาใช้งาน ตรงนี้จะเกิดการ Authenticate ตามกระบวนการที่ setup เอาไว้ใน Azure AD
var accessToken = GraphHelper.GetAccessTokenAsync(scopes).Result;
```

## 3. แสดง token หากมีการเรียกจาก terminal 

ทีนี้เรามีคำสั่งที่สามารถให้ user แสดง token ขึ้นมาใน console ได้ ให้ทำการเขียนคำสั่งแสดง token ลงไปใน case 1 

```cs
while (choice != 0)
{
    //...

    switch (choice)
    {
        case 0:
            Console.WriteLine("Goodbye...");
            break;
        case 1:
            // แสดง token ที่ได้มาจากการร้องขอตอนเริ่มโปรแกรม 
            Console.WriteLine($"Access token: {accessToken}\n");
            break;
        default:
            Console.WriteLine("Invalid choice! Please try again.");
            break;
    }
}
```

## ไฟล์สมบูรณ์

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
    Console.WriteLine("Missing or invalid appsettings.json...exiting");
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


int choice = -1;

while (choice != 0)
{
    Console.WriteLine("Please choose one of the following options:");
    Console.WriteLine("0. Exit");
    Console.WriteLine("1. Display access token");

    try
    {
        choice = int.Parse(Console.ReadLine());
    }
    catch (System.FormatException)
    {
        // Set to invalid value
        choice = -1;
    }

    switch (choice)
    {
        case 0:
            // Exit the program
            Console.WriteLine("Goodbye...");
            break;
        case 1:
            // Display access token
            Console.WriteLine($"Access token: {accessToken}\n");
            break;
        default:
            Console.WriteLine("Invalid choice! Please try again.");
            break;
    }
}

```

## ลองของ

ในเมื่อการเรียกใช้ 

```cs
GraphHelper.Initialize(appId, scopes, (code, cancellation) =>
{
    Console.WriteLine(code.Message);
    return Task.FromResult(0);
});

var accessToken = GraphHelper.GetAccessTokenAsync(scopes).Result;
Console.WriteLine("Signed In...");
// แสดง access token หลังจากการร้องขอครั้งแรก ตอนเริ่มการทำงาน
Console.WriteLine($"Access token: {accessToken}\n");


while (choice != 0)
{
    //...

    switch (choice)
    {
        case 0:
            // Exit the program
            Console.WriteLine("Goodbye...");
            break;
        case 1:
            // ไหนลองขอ token อีกทีสิ ได้ token ตัวเดิมไหม ต้อง sign in ใหม่อีกครั้งไหม?
            accessToken = GraphHelper.GetAccessTokenAsync(scopes).Result;
            Console.WriteLine($"Access token: {accessToken}\n");
            break;
        default:
            Console.WriteLine("Invalid choice! Please try again.");
            break;
    }
}
```