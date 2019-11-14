
# สร้าง Model เพื่อ Mapping กับข้อมูลใน Database

สร้างไฟล์ `Models/User.cs` 

```cs
using MongoDB.Bson;
using MongoDB.Bson.Serialization.Attributes;

namespace UserAPI.Models
{
    public class User
    {
        [BsonId]
        [BsonRepresentation(BsonType.ObjectId)]
        public string Id { get; set; }

        [BsonElement("username")]
        public string Username { get; set; }

        [BsonElement("password")]
        public string Password { get; set; }

        [BsonElement("__v")]
        public int Version { get; set; }
    }
} 
```