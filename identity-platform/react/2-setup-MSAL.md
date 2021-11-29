
# ตั้งค่า Microsoft Authication Library

## 1. กำหนดค่าต่างๆ พร้อมให้ใช้งาน

หลังจากที่เราทำการลงทะเบียน (App Registration) แบบ SPA ใน Active Directory แล้ว เราจะเอาข้อมูลมาเตรียมใช้งานใน React 

ข้อมูลสำคัญได้แก่

- client Id
- authority สามารถหาได้จากส่วน tenant ID 
- redirectUri ส่วนนี้ถ้าไม่กำหนด ตัว MSAL จะถือว่า URL ของเว็บที่เรียกใช้คือ redirectUri

```js
// src/authConfig.js

export const msalConfig = {
    auth: {
      clientId: "ae37ce31-43f2-4d37-a36b-824f50a21796",
      authority: "https://login.microsoftonline.com/2f85a92b-2b18-47a7-9ad3-32caebd8138a", // This is a URL (e.g. https://login.microsoftonline.com/{your tenant ID})
    },
    cache: {
      cacheLocation: "sessionStorage", // รูปแบบการเก็บ cache
      storeAuthStateInCookie: false, // กำหนดเป็น true มีปัญหาการทำงานกับ IE11 หรือ Edge
    }
  };
  
  // กำหนด scope ของ microsoft identity platform
  export const loginRequest = {
   scopes: [
     "User.Read",
     "Mail.Read",
     "Mail.ReadBasic"
    ]
  };
  
  // กำหนด URL ของ resource ต่างๆ เพื่อใช้ในการส่ง request
  export const graphConfig = {
      graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
  };

```

## 2. ทำการโหลดใส่ component 

```js
// src/index.js

import { PublicClientApplication } from "@azure/msal-browser";
import { MsalProvider } from "@azure/msal-react";
import { msalConfig } from "./authConfig";

import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';

// โหลด config ใส่ PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// กำหนด instance ให้ MsalProvider component เพื่อให้สามารถใช้งานได้ใน component อื่นๆ 
ReactDOM.render(
  <React.StrictMode>
    <MsalProvider instance={msalInstance}>
      <App />
    </MsalProvider>
  </React.StrictMode>,
  document.getElementById('root')
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals();

```