
# Authentication #1

ใน Workshop นี้เราจะทำการ Authenticate ผู้ใช้ ผ่านระบบ Azure AD บนแอพ Console 

## 1. การตั้งค่าใน Azure AD App

- ในส่วนของ Authentication ให้แน่ใจว่าได้เลือกแบบ **Mobile and desktop applications**
- กำหนด Redirect URI ให้เรียบร้อย เช่น 

```
http://localhost
```

> อย่าลืมกด Save การตั้งค่าด้วย


## 2. โคลนโปรเจคมาใช้งาน

ใช้คำสั่ง git clone ตัวโปรเจคมาไว้ในเครื่อง

```bash
git clone --single-branch --branch for-start https://github.com/teerasej/dotnet-graphapi-console
```

## 2. ติดตั้ง .NET Package 

- [Microsoft Identity Client](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Flure.Http](https://flurl.dev/)

```bash 
dotnet add package Microsoft.Identity.Client --version 4.18.0

dotnet add package Flurl.Http
```

## 3. เตรียมค่าที่จำเป็น

- กำหนดค่า clientId ที่ได้จาก Azure AD
- กำหนด scope ของ permission ในรูปแบบ String

```C#
using System;
using System.Threading.Tasks;

namespace GraphAPI_1
{
    class Program
    {
        
        private static readonly string _clientId = "";
        static async Task Main(string[] args)
        {

            string[] scopes = new string[] { 
                "User.Read",
            };

        }
    }
}

```

## กำการ Authenticate ด้วย Microsoft Identity 

เราสามารถสร้าง Client สำหรับทำการ Authentication ได้ด้วย 

> สังเกตว่าค่า Redirect URI ต้องเหมือนกับที่ตั้งไว้ใน Azure AD 

```C#
var app = PublicClientApplicationBuilder.Create(_clientId)
                .WithRedirectUri("http://localhost")
                .Build();
```

และเราสามารถขอ Token มาใช้งานได้ผ่านคำสั่ง `AcquireTokenInteractive(scopes)` ซึ่งส่วนนี้จะเป็นการ pop up เว็บขึ้นมาให้ทำการ sign in 

ถ้าเป็นการเข้าใช้ครั้งแรก จะมีการแจ้งขอ Permission จากที่กำหนดลงไปในตัวแปะ `scopes`

```C#
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

var token = result.AccessToken;
```

เสร็จแล้วลองรัน

```C#
using System;
using System.Threading.Tasks;
using Microsoft.Identity.Client;

namespace GraphAPI_1
{
    class Program
    {
        
        private static readonly string _clientId = "";
        static async Task Main(string[] args)
        {
            var app = PublicClientApplicationBuilder.Create(_clientId)
                .WithRedirectUri("http://localhost")
                .Build();

            string[] scopes = new string[] { "User.Read" };

            var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

            var token = result.AccessToken;
        }
    }
}

```