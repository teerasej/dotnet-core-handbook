
# สร้าง secret store และกำหนด Application ID ลง Secret File

## 1. สร้าง Secret file สำหรับเก็บข้อมูลสำคัญ

จาก terminal ให้ใช้คำสั่งสร้าง secret store สำหรับเก็บข้อมูลสำคัญแยกออกจากแอพ และเรียกใช้ภายหลัง ผ่าน package ชื่อ [UserSecret](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets)

```bash
dotnet user-secrets init
```

## 2. เก็บ Application ID และ scopes permission

รันคำสั่งด้านล่างใน terminal โดยแทนที่ **YOUR_APP_ID_HERE** ด้วย Application ID ที่ได้จาก Azure AD 

```bash
dotnet user-secrets set appId "YOUR_APP_ID_HERE"
dotnet user-secrets set scopes "User.Read;"
```

* เราสามารถมาแก้ไข scopes ได้ที่หลัง ผ่านการกำหนดด้วยคำสั่งเดียวกัน
