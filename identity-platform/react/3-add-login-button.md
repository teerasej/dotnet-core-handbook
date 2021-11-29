
# เพิ่มปุ่ม Sign In

ในที่นี้เราจะสร้าง component สำหรับแสดงปุ่ม Sign in

## 1. สร้าง LoginView.js

```jsx
// src/LoginView.js
import React from 'react'

// เรียกใช้ hook useMsal
import { useMsal } from "@azure/msal-react";
import { loginRequest } from "./authConfig";

export default function LoginView() {

    // เรียกใช้ hook เพื่อเข้าถึง msal
    const { instance } = useMsal();

    // เรียกใช้ pop up ในการ sign in
    const handleLogin = instance => {
        instance.loginPopup(loginRequest).catch(e => {
            console.error(e);
        });
    }

    // แสดงปุ่ม sign in
    return (
        <div>
            <button onClick={() => { handleLogin(instance) }}>Sign in</button>
        </div>
    )
}
```

## 2. เพิ่มปุ่ม Sign in/Sign out ใน App Component

```jsx
// src/App.js

import logo from './logo.svg';
import './App.css';

import LoginView from './LoginView';

function App() {
  return (
    <div className="App">
      <LoginView/>
    </div>
  );
}

export default App;

```