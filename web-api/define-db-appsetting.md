
# กำหนดข้อมูลสำหรับต่อ Database ใน appsetting.json

## 1. กำหนดค่าในไฟล์ `appsettings.json`

เปิดไฟล์ `appsettings.json`

```json
{
  "UserDatabaseSettings": {
    "UsersCollectionName": "users",
    "ConnectionString": "mongodb://localhost:27017",
    "DatabaseName": "test"
  },
  "Logging": {
    ...

```

## 2. สร้าง class mapping 

สร้างไฟล์ `Models/UserStoreDatabaseSetting.cs`

```cs
namespace UserAPI.Models
{
    public class UserDatabaseSettings : IUserDatabaseSettings
    {
        public string UserCollectionName { get; set; }
        public string ConnectionString { get; set; }
        public string DatabaseName { get; set; }
    }

    public interface IUserDatabaseSettings
    {
        string UserCollectionName { get; set; }
        string ConnectionString { get; set; }
        string DatabaseName { get; set; }
    }
} 
```

## 3. โหลด Setting มาใช้ในโปรเจค

เปิดไฟล์ `Startup.cs`

เริ่มจาก using namespaces ที่ต้องใช้

```cs
using UserAPI.Models;
using Microsoft.Extensions.Options;
```

และเขียนโหลดตัว setting ใน method `ConfigureServices`

```cs
public void ConfigureServices(IServiceCollection services)
{

    services.Configure<UserDatabaseSettings>(Configuration.GetSection(nameof(UserDatabaseSettings)));
    services.AddSingleton<IUserDatabaseSettings>(sp => {
        return sp.GetRequiredService<IOptions<UserDatabaseSettings>>().Value;
    });

    services.AddControllers();
}
```