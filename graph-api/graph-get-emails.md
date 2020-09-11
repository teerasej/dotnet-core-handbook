
# เรียกดูรายการอีเมลล์

# 1. Register App ใน Azure AD 

- สร้างแอพใหม่ในส่วนของ App Registration in Azure Active Directory
- กำหนด support account type ตามต้องการ
- กำหนด Redirect URI เป็น **Public Client/Native (mobile & desktop)**


## สิ่งที่จะได้จากขั้นตอนนี้

- Application (client) ID



# 2. สร้างโปรเจคใหม่ และลงแพคเกจ

```bash
dotnet new console -o ListEmail
```

```bash
dotnet add package Flurl.Http
dotnet add package Microsoft.Graph --version 3.13.0
dotnet add package Microsoft.Graph.Auth --version 1.0.0-preview.5
```


## 3. เขียนโปรแกรมส่วน Authenticate 

- กำหนด client ID ที่ได้จาก Application ใน Azure AD
- กำหนด scopes ให้มี `"User.Read"` และ `"Mail.Read"`

```c#
using System;
using System.Drawing;
using System.Threading.Tasks;
using Microsoft.Identity.Client;
using System.IO;
using Flurl.Http;
using Microsoft.Graph.Auth;
using Microsoft.Graph;

namespace ListEmail
{
    class Program
    {

        private static readonly string _clientId = "clientID";
        static async Task Main(string[] args)
        {

            string[] scopes = new string[] {
                "User.Read",
                "Mail.Read"
            };

            var app = PublicClientApplicationBuilder
                .Create(_clientId)
                .WithRedirectUri("http://localhost")
                .Build();

            var provider = new InteractiveAuthenticationProvider(app, scopes);
            var client = new GraphServiceClient(provider);

        }   
    }
}
```

## 4. ดึงข้อมูล Email มาแสดง

- ใช้ client ในการเรียก `Me.Messages` ในที่นี้หมายถึง Email ของผู้ใช้
- นำมาวนลูปแสดง id และ subject 

```c#
var emails = await client.Me.Messages.Request().GetAsync();

foreach (var email in emails)
{
    Console.WriteLine($"Identity: {email.Id}");
    Console.WriteLine($"Subject: {email.Subject}");
}
```

## 5. แสดงเนื้อหาใน Email

- เลือก id ของ Email จากรายการที่แสดงใน Terminal ของหัวข้อที่แล้ว มากำหนดให้กับ `targetEmailId`
- ใช้ id ในการขอข้อมูลผ่าน `Me.Messages[targetEmailId]`
- `targetEmail` จะมีข้อมูลต่างๆ ของอีเมลล์นั้น เช่น preview และ body

```c#
var targetEmailId = "messageId";
var targetEmail = await client.Me.Messages[targetEmailId].Request().GetAsync();

Console.WriteLine($"Preview Message: {targetEmail.BodyPreview}");
Console.WriteLine($"Message: {targetEmail.Body.Content}");
```

# ไฟล์สมบูรณ์

```c#
using System;
using System.Drawing;
using System.Threading.Tasks;
using Microsoft.Identity.Client;
using System.IO;
using Flurl.Http;
using Microsoft.Graph.Auth;
using Microsoft.Graph;

namespace ListEmail
{
    class Program
    {

        private static readonly string _clientId = "clientID";
        static async Task Main(string[] args)
        {

            string[] scopes = new string[] {
                "User.Read",
                "Mail.Read"
            };

            var app = PublicClientApplicationBuilder
                .Create(_clientId)
                .WithRedirectUri("http://localhost")
                .Build();

            var provider = new InteractiveAuthenticationProvider(app, scopes);
            var client = new GraphServiceClient(provider);

            var me = await client.Me.Request().GetAsync();

            Console.WriteLine($"[Job title] {me.JobTitle}");
            Console.WriteLine($"[Display Name] {me.DisplayName}");

            var emails = await client.Me.Messages.Request().GetAsync();

            foreach (var email in emails)
            {
                Console.WriteLine($"Identity: {email.Id}");
                Console.WriteLine($"Subject: {email.Subject}");
            }
            
            var targetEmailId = "messageId";
            var targetEmail = await client.Me.Messages[targetEmailId].Request().GetAsync();

            Console.WriteLine($"Preview Message: {targetEmail.BodyPreview}");
            Console.WriteLine($"Message: {targetEmail.Body.Content}");
        }   
    }
}
```