
# สร้าง Service class ไว้ทำงานกับ Database

## 1. สร้าง User Service 

สร้างไฟล์ `Services/UserService.cs`

```cs
using System.Collections.Generic;
using MongoDB.Driver;
using UserAPI.Models;

namespace UserAPI.Services
{
    public class UserService
    {
        private readonly IMongoCollection<User> _Users;

        public UserService(IUserDatabaseSettings settings)
        {
            // ดึงเอาค่าจาก Setting มาใช้เชื่อมต่อ Database
            var client = new MongoClient(settings.ConnectionString);
            var database = client.GetDatabase(settings.DatabaseName);

            _Users = database.GetCollection<User>(settings.UserCollectionName);
        }

        public List<User> Get() =>
            _Users.Find(user => true).ToList();

        public User Get(string id) =>
            _Users.Find<User>(user => user.Id == id).FirstOrDefault();

        public User Create(User user)
        {
            _Users.InsertOne(user);
            return user;
        }

        public void Update(string id, User userIn) =>
            _Users.ReplaceOne(user => user.Id == id, userIn);

        public void Remove(User userIn) =>
            _Users.DeleteOne(user => user.Id == userIn.Id);

        public void Remove(string id) => 
            _Users.DeleteOne(user => user.Id == id);
    }
} 
```

## 2. นำมากำหนดเป็น Singleton ใน Startup

เปิดไฟล์ `Startup.cs` 

```cs
using UserAPI.Services;

//...

public void ConfigureServices(IServiceCollection services)
{

    services.Configure<UserDatabaseSettings>(Configuration.GetSection(nameof(UserDatabaseSettings)));
    services.AddSingleton<IUserDatabaseSettings>(sp => {
        return sp.GetRequiredService<IOptions<UserDatabaseSettings>>().Value;
    });

    // กำหนดตรงนี้
    services.AddSingleton<UserService>();

    services.AddControllers();
}
```

