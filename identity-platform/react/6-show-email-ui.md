
# สร้าง Component สำหรับแสดงข้อมูล Email

## 1. สร้าง EmailView.js

```jsx
// src/EmailView.js
import React from 'react'
import { useMsal, useIsAuthenticated } from "@azure/msal-react";

export default function EmailView() {


    return (
        <div>
        
        </div>
    )
}

```

## 2. เพิ่มปุ่ม EmailView ใน App Component

```jsx
// src/App.js

import logo from './logo.svg';
import './App.css';

import LoginView from './LoginView';
import ProfileView from './ProfileView';
import EmailView from './EmailView';

function App() {
  return (
    <div className="App">
      <LoginView/>
      <ProfileView/>
      <EmailView/>
    </div>
  );
}

export default App;

```