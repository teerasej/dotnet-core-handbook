
# สร้าง และใช้งานโปรเจค Blazor Server ด้วย .NET Identity Web Template

## สร้างโปรเจคแบบ multiple organization


ในที่นี้ เพื่อความเรียบง่าย เราจะเลือกสร้างแบบ Authenticate แบบ Multiple organization 

- ดูตารางเทียบการ authenticate ที่เหมาะกับเทมเพลตที่เหมาะกับเทมเพลตแต่ละแบบ [ได้ที่นี่](https://github.com/AzureAD/microsoft-identity-web/wiki#asp-net-core-web-app-and-web-api-project-templates) 
- และคำสั่งใช้ template Blazor แบบอื่นๆ [ได้ที่นี่](https://github.com/AzureAD/microsoft-identity-web/wiki/web-app-template#use-the-web-app-blazor-server-template)

```bash
dotnet new blazorserver2 --auth MultiOrg -o DotNet_BlazorServer_GraphAPI_with_IdentityWeb
```

## รันโปรเจคด้วย `dotnet run`

ตัว template นี้ มีการตั้ง target 2 framework นั่นคือ

- netcoreapp3.1
- net5.0

ดังนั้นตอนรัน จะต้องมีการระบุ framework เป้าหมายด้วย ([อ้างอิง](https://github.com/AvaloniaUI/avalonia-dotnet-templates/issues/25))

```bash
dotnet run --framework net5.0
```

### ถ้าไม่ต้องการเลือก framework

สามารถไปปรับในไฟล์ `.csproj` จาก 

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
     <TargetFrameworks>netcoreapp3.1; net5.0</TargetFrameworks>
  </PropertyGroup>
  ...
</Project>
```

เป็นแบบด้านล่าง (สังเกตว่าเราตัด `s` ออกไปจาก `<TargetFrameworks>`)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
     <TargetFramework>net5.0</TargetFramework>
  </PropertyGroup>
  ...
</Project>
```