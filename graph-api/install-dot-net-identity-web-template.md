

# ดาวน์โหลดและติดตั้ง Web Template ของ Microsoft Identity Web

1. [เข้าไปใน Link และดาวน์โหลดตัว template จาก Nuget](https://github.com/AzureAD/microsoft-identity-web/wiki/web-app-template)

```
You can download the Microsoft.Identity.Web.ProjectTemplates-0.3.1-preview NuGet package from NuGet.org
```

2. รันคำสั่งใน Terminal เพื่อใช้งานไฟล์ และเพิ่ม template ใหม่ลงไปใน dotnet CLI

> คำสั่งด้านล่างอาจจะเปลี่ยนไปตามเวอร์ชั่นของไฟล์

```bash
dotnet new -i Microsoft.Identity.Web.ProjectTemplates.0.3.1-preview.nupkg
```

[อ้างอิง](https://github.com/AzureAD/microsoft-identity-web/wiki/web-app-template)

