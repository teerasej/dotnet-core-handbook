
# 

## เพิ่มส่วนเช็ค user ใน service

เปิดไฟล์ `Services/UserService.cs` 

และเพิ่ม method ด้านล่างลงไปใน service 

```cs
 public bool isExist(string username, string password){

    var user = this._Users.Find<User>(user => user.Username == username && user.Password == password).FirstOrDefault();

    if(user != null) return true;

    return false;
}
```

## 2. สร้าง route POST /login

เปิดไฟล์ `Controllers/UserController.cs` 

และเพิ่ม Route นี้ลงไปใน Class

```cs
[HttpPost("login")]
public ActionResult<int> Login(User user)
{
    if(_userService.isExist(user.Username, user.Password)){
        return 1;
    }

    return 0;
}
```