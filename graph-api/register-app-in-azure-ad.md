
# สร้างและตั้งค่า Application ใน Azure AD

1. ทำการลงชื่อเข้าใช้ [Azure Portal](https://portal.azure.com/#home)
2. เข้าไปที่ Azure Active Directory (Azure AD)

<img width="446" alt="2020-09-09_22-09-02" src="https://user-images.githubusercontent.com/85179/92617246-465ca580-f2e9-11ea-9de1-fc126c138542.png">


3. ในส่วน Manage ให้เลือก App Registration 


<img width="275" alt="2020-09-09_22-10-48" src="https://user-images.githubusercontent.com/85179/92617411-74da8080-f2e9-11ea-9d73-1f09f98bbaf1.png">

4. กรอกรายละเอียดของ Application ที่จะใช้งานให้ครบ

    - **Name** ชื่อแอพ
    - **Supported account type** ประเภทบัญชีที่สามารถใช้งาน App นี้ได้ 
    - **Redirect URI** ในการทำการ authenticate ผู้ใช้เข้าระบบบางเคส จำเป็นต้องเตรียม URI ที่ Azure AD จะทำการคืนค่ากลับมาให้ด้วย (สามารถแก้ไขทีหลังได้) สามารถใช้ `http://localhost` ก่อนได้

5. เสร็จแล้วให้ copy ส่วน **Application (client) ID**
 เก็บไว้


<img width="731" alt="2020-09-09_22-18-31" src="https://user-images.githubusercontent.com/85179/92618424-8b350c00-f2ea-11ea-8123-a6d2858ada46.png">

6. เลือกเข้ามาดูในส่วนชื่อ Authentication ซึ่งการตั้งค่าต่างๆ ในส่วนนี้จะมีผลต่อแอพที่ทำการ authen เข้ามาที่ Azure AD

<img width="1302" alt="2020-09-09_22-19-40" src="https://user-images.githubusercontent.com/85179/92618868-03033680-f2eb-11ea-84d7-bff0d18743df.png">
