
# การ Setup ตัว Microsof Graph Client SDK


## 1. สร้าง AuthProvider สำหรับการทำ Authentication

สร้างไฟล์ `AuthProvider.cs` ที่ implement `IAuthenticationProvider`

```c#
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.Identity.Client;

namespace GraphAPI_1
{
    public class AuthProvider : IAuthenticationProvider
    {

        public async Task AuthenticateRequestAsync(HttpRequestMessage requestMessage)
        {
            
        }
        
    }
}
```

# 2. ทำการเขียนกลไกการ Authentication

ในที่นี้เราจะใช้ `Microsoft.Identity.Client` มาช่วย

```c#

using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.Identity.Client;

namespace GraphAPI_1
{
    public class AuthProvider : IAuthenticationProvider
    {
        // authentication client
        private IPublicClientApplication _msalClient;

        // permission scopes
        private string[] _scopes;
        private IAccount _userAccount;

        public AuthProvider(string clientId, string[] scopes)
        {
            _scopes = scopes;

            // ทำการสร้าง authentication client 
            // ด้วย clientId
            _msalClient = PublicClientApplicationBuilder
                .Create(clientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount, true)
                .Build();
        }

        public async Task AuthenticateRequestAsync(HttpRequestMessage requestMessage)
        {
            // ทำการใส่ token ไปกับ Request
            // ระบบที่ใช้งาน IAuthenticationProvider อย่างเช่น Microsoft.Graph จะใช้งานส่วนนี้ในการทำงาน
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("bearer", await GetAccessToken());
        }
        
         public async Task<string> GetAccessToken()
        {
            // ถ้าไม่มี user acccount ในระบบให้ทำการ login ใหม่
            if (_userAccount == null)
            {
                try
                {
                    // ทำงาน authen ผ่าน popup
                    var result = await _msalClient.AcquireTokenInteractive(scopes).ExecuteAsync();

                    _userAccount = result.Account;
                    return result.AccessToken;
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"Error getting access token: {exception.Message}");
                    return null;
                }
            }
            else
            {
                // กลไกขอ token ใหม่ด้วย account ที่มีอยู่แล้ว

                    var result = await _msalClient
                        .AcquireTokenSilent(_scopes, _userAccount)
                        .ExecuteAsync();

                    return result.AccessToken;
            }
        }
    }
}
```

## 3. สร้าง Graph Helper ในการจัดการเข้าถึงข้อมูลต่างๆ 

```c#
using System;
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
    }
}

```

## 4. นำ AuthProvider และ GraphHelper มาใช้ในโปรแกรม 

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
           
        }
    }
}

```