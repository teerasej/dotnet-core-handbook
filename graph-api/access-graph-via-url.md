
# การใช้ Microsoft Graph ผ่าน URL โดยตรง

- เราจะใช้ `Flure.Http` ในการส่ง Request ไปที่ Microsoft Graph โดยตรง
- โดยมีการแนบ Token ที่ได้ไปด้วย 
- [สามารถดู graph ในการเข้าถึง resource ต่างๆ เพิ่มเติมได้ที่นี่](https://docs.microsoft.com/en-US/graph/api/overview?view=graph-rest-1.0)

```C#
var me = await "https://graph.microsoft.com/v1.0/me/"
                .WithOAuthBearerToken(token)
                .GetStringAsync();
Console.WriteLine(me);
```



```C#
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
            var app = PublicClientApplicationBuilder.Create(_clientId)
                .WithRedirectUri("http://localhost")
                .Build();

            string[] scopes = new string[] { "User.Read" };

            var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

            var token = result.AccessToken;

            var me = await "https://graph.microsoft.com/v1.0/me/"
                .WithOAuthBearerToken(token)
                .GetStringAsync();
            Console.WriteLine(me);

        }
    }
}

```
 
