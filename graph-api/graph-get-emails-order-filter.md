
# การใช้งาน Order และ Filter

ไฟล์เริ่มต้น 

```c#
using System;
using System.Drawing;
using System.Threading.Tasks;
using Microsoft.Identity.Client;
using System.IO;
using Flurl.Http;
using Microsoft.Graph.Auth;
using Microsoft.Graph;

namespace GraphAPI_2
{
    class Program
    {

        private static readonly string _clientId = "38478b47-0715-4ce9-8d56-90ff0d503ce4";
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

            var emails = await client.Me.Messages.Request().GetAsync();


            foreach (var email in emails)
            {
                Console.WriteLine($"Recieved: {email.ReceivedDateTime:G}");
                Console.WriteLine($"Subject: {email.Subject}");
            }
            
        }   
    }
}

```

## 1. ทำการเรียงลำดับด้วย Recieved Date

- ทำการใช้ `.OrderBy()` ในการเรียงลำดับด้วย Recieved Date ของ Message
- `nameof()` ใช้ในการแปลงชื่อ Property เป็น string

```c#
var emails = await client.Me.Messages.Request()
            .OrderBy(nameof(Message.ReceivedDateTime))
            .GetAsync();
```

### เรียงจากล่าสุดขึ้นก่อน 

- อ้างอิง [OrderBy Parameter](https://docs.microsoft.com/en-us/graph/query-parameters#orderby-parameter)

```c#
var emails = await client.Me.Messages.Request()
            .OrderBy($"{nameof(Message.ReceivedDateTime)} DESC")
            .GetAsync();
```

## 2. การทำ Filter

[ดูเพิ่มเติมได้ใน Filter Parameter](https://docs.microsoft.com/en-us/graph/query-parameters#filter-parameter)

```c#
var emails = await client.Me.Messages.Request()
            .OrderBy($"{nameof(Message.ReceivedDateTime)}")
            .Filter($"{nameof(Message.ReceivedDateTime)} lt 2020-09-09")
            .GetAsync();
```


# ไฟล์เต็ม

```c#
using System;
using System.Drawing;
using System.Threading.Tasks;
using Microsoft.Identity.Client;
using System.IO;
using Flurl.Http;
using Microsoft.Graph.Auth;
using Microsoft.Graph;

namespace GraphAPI_2
{
    class Program
    {

        private static readonly string _clientId = "38478b47-0715-4ce9-8d56-90ff0d503ce4";
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


            var laterThanThisDay = DateTime.Today.AddDays(-10);
            Console.WriteLine($"Later than {laterThanThisDay:o}");

            var emails = await client.Me.Messages.Request()
            .OrderBy($"{nameof(Message.ReceivedDateTime)} DESC")
            .Filter($"{nameof(Message.ReceivedDateTime)} lt 2020-09-09")
            .GetAsync();


            foreach (var email in emails)
            {
                Console.WriteLine($"Recieved: {email.ReceivedDateTime:G}");
                Console.WriteLine($"Subject: {email.Subject}");
            }
            
        }   
    }
}

```

# โจทย์

1. หาอีเมลล์ที่ยังไม่ได้เปิดอ่าน ([เฉลย](https://docs.microsoft.com/en-us/graph/query-parameters#filter-parameter))
2. หาอีเมลล์ที่มีไฟล์แนบ attachment ([เฉลย](https://stackoverflow.com/a/45153444/95974))