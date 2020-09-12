
# การเพิ่ม App ใน Azure AD Portal

1. log in เข้าไปที่ https://aad.portal.azure.com/
2. เลือก **Azure Active Directory** จากเมนูด้านซ้าย, และเลือก **App registrations** ในส่วนของ **Manage**

![](https://docs.microsoft.com/en-us/graph/tutorials/aspnet-core/tutorial/images/aad-portal-app-registrations.png)

3. เลือก New Registration และกรอกข้อมูลตามด้านล่าง 

- **Name:** ASP.NET Core Graph Tutorial.
- **Supported account types:** **Accounts in any organizational directory and personal Microsoft accounts**
- **Redirect URI:** ตั้งค่าเป็น **Web** - **https://localhost:5001/**

![](https://docs.microsoft.com/en-us/graph/tutorials/aspnet-core/tutorial/images/aad-register-an-app.png)

4. กดปุ่มยืนยันการสร้างด้านล่าง เราจะได้ส่วน Application ID มาในส่วนนี้

![](https://docs.microsoft.com/en-us/graph/tutorials/aspnet-core/tutorial/images/aad-register-an-app.png)

5. เลือกเมนู **Authentication** ในส่วนของ **Manage** แล้วกำหนดค่าในส่วน **Redirect URIs** ให้เพิ่มเป็น https://localhost:5001/signin-oidc
   
6. ในส่วน **Logout URL** กำหนดค่าเป็น https://localhost:5001/signout-oidc.

7. มองหาส่วนที่ชื่อ **Implicit grant** และเปิดการใช้งาน **ID tokens** อย่าลืมกด **Save** ด้วยล่ะ

![](https://docs.microsoft.com/en-us/graph/tutorials/aspnet-core/tutorial/images/aad-web-platform.png)

8. เลือก **Certificates & secrets** ในส่วนของเมนู **Manage** 
   กดปุ่ม **New client secret** กรอกรายละเอียดให้เรียบร้อยและกดปุ่ม **Add**

![](https://docs.microsoft.com/en-us/graph/tutorials/aspnet-core/tutorial/images/aad-new-client-secret.png)

9. คัดลอก Client Secret เก็บไว้ เพราะ**มันจะไม่แสดงขึ้นมาอีกเลย**

![](https://docs.microsoft.com/en-us/graph/tutorials/aspnet-core/tutorial/images/aad-copy-client-secret.png)