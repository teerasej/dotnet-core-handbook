
# เพิ่มปุ่ม Sign out 

เราจะเพิ่มปุ่มสำหรับ Sign out โดยใช้แนวทางเดียวกับปุ่ม Sign in


```jsx
// src/LoginView.js

import React from 'react'

// เรียกใช้งาน useIsAuthenticated
import { useMsal, useIsAuthenticated } from "@azure/msal-react";
import { loginRequest } from "./authConfig";

export default function LoginView() {

    const { instance } = useMsal();

    // เช็คสถานะการ sign in
    const isAuthenticated = useIsAuthenticated();

    const handleLogin = instance => {
        instance.loginPopup(loginRequest).catch(e => {
            console.error(e);
        });
    }

    // เรียกใช้ logoutPopup ส่วนนี้ต้องมีการกำหนด redirect Uri ด้วย แตกต่างจากแบบ log in
    const handleLogout = instance => {
        instance.logoutPopup(
            {
                postLogoutRedirectUri: "http://localhost:3000",
                mainWindowRedirectUri: "http://localhost:3000",
            }
        ).catch(e => {
            console.error(e);
        });
    }

    // แสดงปุ่ม sign in/sign out อ้างอิงจากสถานะการเข้าใช้งาน
    return (
        <div>
            {isAuthenticated
                ? <button onClick={() => { handleLogout(instance) }}>Sign out</button>
                : <button onClick={() => { handleLogin(instance) }}>Sign in</button>
            }

        </div>
    )
}


```