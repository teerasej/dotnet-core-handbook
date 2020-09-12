
# สร้าง และเตรียมโปรเจค ASP.NET MVC 


## 1. สร้างโปรเจค ASP.NET MVC

ใน Terminal รันคำสั่ง สร้าง และรันโปรเจคขึ้นมาทดสอบ

```bash
dotnet new mvc -o GraphTutorial
cd GraphTutorial
dotnet run
```

สังเกตว่า ตอนนี้เว็บแอพเรารันอยู่ที่ https://localhost:5001

## 2. รันคำสั่งติดตั้ง package

```bash
dotnet add package Microsoft.Identity.Web --version 0.1.3-preview
dotnet add package Microsoft.Identity.Web.UI --version 0.1.3-preview
dotnet add package Microsoft.Graph --version 3.5.0
```