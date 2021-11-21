
# สร้างโปรเจค .NET console และติดตั้ง package ที่จำเป็น

## 1. สร้างโปรเจคใหม่ด้วยคำสั่ง

```bash
dotnet new console -n graph-console
```

## 2. ติดตั้ง package ที่จำเป็นต้องใช้

ผ่านการรันคำสั่งด้านล่าง ภายใน Terminal

```bash
dotnet add package Microsoft.Extensions.Configuration.UserSecrets
dotnet add package Azure.Identity
dotnet add package Microsoft.Graph
dotnet add package TimeZoneConverter
```

## 3. ทดสอบการทำงานของโปรเจค

ผ่านการรันคำสั่งด้านล่าง ภายใน Terminal

```
dotnet run
```


### ถ้าต้องการ debug แบบใส่ข้อมูลผ่าน console ได้ 

ให้ไปปรับค่าชื่อ `console:` ใน **.vscode/launch.json** ที่อยู่ในโปรเจค เป็น **integratedTerminal**

```json
{
    ...
    "configurations": [
        {
           ...
            "console": "integratedTerminal",
           ...
        },
        ...
    ]
}
```