
# สร้าง Component สำหรับแสดงข้อมูลที่เกี่ยวข้องกับ User 

## 1. สร้าง ProfileView.js

```jsx
// src/ProfileView.js
import React from 'react'
import { useMsal, useIsAuthenticated } from "@azure/msal-react";

export default function ProfileView() {

    // เรียกใช้ useMsal เพื่อนำข้อมูล account ที่ sign in อยู่มาพิจารณา
    const { accounts } = useMsal();

    // เช็ค account และชื่อของ account
    const name = accounts[0] && accounts[0].name;

    // แสดงชื่อของ Account
    return (
        <div>
            { name && 
                <h5>Welcome {name}</h5>
            }
        </div>
    )
}

```

## 2. เพิ่มปุ่ม ProfileView ใน App Component

```jsx
// src/App.js

import logo from './logo.svg';
import './App.css';

import LoginView from './LoginView';
import ProfileView from './ProfileView';

function App() {
  return (
    <div className="App">
      <LoginView/>
      <ProfileView/>
    </div>
  );
}

export default App;

```