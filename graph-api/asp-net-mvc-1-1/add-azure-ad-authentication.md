
# เพิ่ม Azure AD Authentication

## 1. เพิ่มการตั้งค่า Azure AD ในไฟล์ **appsettings.json**

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "TenantId": "common",
    "CallbackPath": "/signin-oidc"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

2. รันคำสั่งสร้าง ClientId และ ClientSecret

```c#
dotnet user-secrets init
dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
```

## 2. เพิ่มระบบ Sign in 

1. สร้างไฟล์ **GraphConstants.cs** ในโฟลเดอร์ **/Graph**

```c#
namespace GraphTutorial
{
    public static class GraphConstants
    {
        // Defines the permission scopes used by the app
        public readonly static string[] Scopes =
        {
            "User.Read",
            "MailboxSettings.Read",
            "Calendars.ReadWrite"
        };
    }
}
```

2. เรียกใช้ namespace เพิ่มเติมในไฟล์ **Startup.cs**

```c#
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc.Authorization;
using Microsoft.Identity.Web;
using Microsoft.Identity.Web.TokenCacheProviders.InMemory;
using Microsoft.Identity.Web.UI;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;
using Microsoft.Graph;
using System.Net;
```

3. แก้ไขโค้ดใน **ConfigureService** function ด้วยโค้ดด้านล่าง 

```c#
public void ConfigureServices(IServiceCollection services)
{
    // Add Microsoft Identity Platform sign-in
    services.AddSignIn(Configuration);

    // Add ability to call web API (Graph)
    // and get access tokens
    services.AddWebAppCallsProtectedWebApi(Configuration,
        GraphConstants.Scopes)
        // Use in-memory token cache
        // See https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization
        .AddInMemoryTokenCaches();

    // Require authentication
    services.AddControllersWithViews(options =>
    {
        var policy = new AuthorizationPolicyBuilder()
            .RequireAuthenticatedUser()
            .Build();
        options.Filters.Add(new AuthorizeFilter(policy));
    })
    // Add the Microsoft Identity UI pages for signin/out
    .AddMicrosoftIdentityUI();
}
```


