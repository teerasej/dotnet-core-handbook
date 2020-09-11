
# ทดสอบใช้งาน Web Identity

workshop นี้ใช้ได้บน Windows 

1. [ดาวน์โหลดและติดตั้ง Azure CosmosDB Emulator](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator-release-notes) 
2. [clone โปรเจค จาก repo ที่นี่](https://github.com/teerasej/BlazorServer-MSIdentityWeb-pre4-graphclient) 

3. กำหนดค่าที่จำเป็นจาก Application บน Azure AD App ในไฟล์​ `appsettings.json`
4. ทำการนำ comment ในไฟล์​ **Startup.cs** ออก

```c#
services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.ContainerName = Configuration["CosmosCache:ContainerName"];
    cacheOptions.DatabaseName = Configuration["CosmosCache:DatabaseName"];
    cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosCache:ConnectionString"]);
    cacheOptions.CreateIfNotExists = true;
});

services.AddControllersWithViews(options =>
{
    var policy = new AuthorizationPolicyBuilder()
        .RequireAuthenticatedUser()
        .Build();
    options.Filters.Add(new AuthorizeFilter(policy));
}).AddMicrosoftIdentityUI();

```