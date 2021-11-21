
# สร้างและตั้งค่า Application ใน Azure AD สำหรับ Web Application

ตลอดทั้งขั้นตอนนี้ เราจะใช้ Admin Account ของ Office 365 [ที่ไปสร้างไว้ก่อนหน้านี้](365-dev-account/readme.md)นะ

## 1. เข้าใช้งาน Azure Active Directory (Azure AD)

ทำการลงชื่อเข้าใช้ [Azure AD Admin Center](https://portal.azure.com/#home)

### ถ้าเกิดเราเข้าจาก Azure Portal 

ให้เลือกเข้าไปที่ Azure Active Directory (Azure AD)

<img width="446" alt="2020-09-09_22-09-02" src="https://user-images.githubusercontent.com/85179/92617246-465ca580-f2e9-11ea-9de1-fc126c138542.png">

## 2. ลงทะเบียนแอพเพื่อใช้งานกับ Azure AD

- ในส่วน Manage ให้เลือก App registrations ซึ่งจะมีโปรไฟล์ของ App ที่มีการลงทะเบียนกับ domain นี้ไว้ก่อนอยู่แล้ว 
- คลิกปุ่ม New Registration เพื่อเริ่มการลงทะเบียน

## 3. กรอกรายละเอียดของ Application 

1. **Name** ชื่อแอพ
2. **Supported account type** ประเภทบัญชีที่สามารถใช้งาน App นี้ได้ 
3. **Redirect URI** ในการทำการ authenticate ผู้ใช้เข้าระบบบางเคส จำเป็นต้องเตรียม URI ที่ Azure AD จะทำการ redirect หลังจากการ authentication สมบูรณ์ (สามารถแก้ไขทีหลังได้) 

เช่น
- ถ้าเป็น mobile หรือ desktop app ก็สามารถใช้ **https://login.microsoftonline.com/common/oauth2/nativeclient**
- ถ้าเป็น web ก็สามารถใช้ **http://localhost** ก่อนได้ (แต่พอ production จริงต้องมาแก้ให้ตรงกับที่กำหนดในแอพนะ)

4. กด register ได้เลย 

## 4. เก็บ Application (client) ID

พอลงทะเบียนแอพเรียบร้อยแล้ว เราจะมาอยู่หน้า Dashboard ให้ กดดูในส่วน Overview และ คัดลอก **Appliation ID** เอาไว้ เราต้องเอามันมาใช้ใน Client (ในที่นี้เป็น Desktop Client)

## 5. กำหนด Branding

เราสามารถกำหนดข้อมูลที่จะแสดงขึ้นมาในขั้นตอนการ Authentication หรือส่วนการทำงานอื่นๆ ได้ เช่น ชื่อ และโลโก้ของแอพ 

ดาวน์โหลดโลโก้มาลองได้เลย

## 6. ตั้งค่าส่วนของ Authentication

ส่วนนี้จะเป็นการตั้งค่าต่างๆ ที่จำเป็นต้องใช้ในการ Authenticate ผู้ใช้กับ Azure Active Directory ของเรา โดยแยกตาม Platform 

1. เช่นตอนแรกเราจะมี Mobile and Desktop เป็น Platform เริ่มต้น และมี Redirect URI ให้เปิดใช้งานเช่น NativeClient, Live SDK, หรือ MASL (Microsoft Authentication Library)
2. ส่วนของ Advanced Setting > Allow Public Client flow **ให้เปิดใช้งาน**