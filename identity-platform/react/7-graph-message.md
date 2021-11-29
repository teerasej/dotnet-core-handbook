
# เรียกข้อมูล Email 10 อันล่าสุด และแสดงใน EmailView 

```jsx
// src/EmailView.js


import React, { useState } from 'react'
import { useMsal, useIsAuthenticated } from "@azure/msal-react";
import { graphConfig, loginRequest } from './authConfig';

export default function EmailView() {

    // เรียกใช้ instance และ inProgress เพิ่มเติม
    const { instance, accounts, inProgress } = useMsal();

    // เช็คสถานะการ login
    const isAuthenticated = useIsAuthenticated();

    // สร้าง state สำหรับเก็บ access token ในการส่งไปกับ request ไปหา Microsoft Graph
    const [accessToken, setAccessToken] = useState(null);

    // สร้าง state สำหรับเก็บข้อมูล email ที่ได้มาจาก Microsoft Graph 
    const [messages, setMessages] = useState([]);

    // ถ้ายังไม่มี access token แต่ login แล้ว
    if (!accessToken && isAuthenticated) {
        
        // สร้าง object สำหรับขอ token จาก Microsoft Identity Platform
        const request = {
            ...loginRequest,
            account: accounts[0]
        };

        // acquireTokenSilent() จะทำการขอ token เงียบๆ ด้วยข้อมูลของผู้ที่ sign in แล้ว ไม่กระโตกกระตาก 
        instance.acquireTokenSilent(request).then((response) => {
            // ถ้าได้ token มาอย่างไม่มีปัญหาก็เก็บไว้สำหรับส่ง request
            setAccessToken(response.accessToken);
        }).catch((e) => {
            // ถ้าขอไม่ได้ (อาจจะไม่มี login) ให้ popup แทน
            instance.acquireTokenPopup(request).then((response) => {
                setAccessToken(response.accessToken);
            });
        });
    } else {

        // สร้าง request สำหรับขอข้อมูล ตรงนี้จะเป็นเหมือนกับการส่ง request ทั่วไป

        const headers = new Headers();
        console.log('token', accessToken);
        const bearer = `Bearer ${accessToken}`;

        headers.append("Authorization", bearer);

        const options = {
            method: "GET",
            headers: headers
        };

        fetch(graphConfig.graphMeEndpoint + '/messages', options)
            .then(response => {
                // ถ้าได้ข้อมูลมาแล้ว ก็ทำการเก็บใส่ state
                response.json().then(data => {
                    setMessages(data.value);
                });
            })
            .catch(error => console.log(error));
    }

    // ถ้ามีข้อมูล email ก็แสดงออกมาเลยจ้า
    return (
        <div>
            {
                messages &&
                messages.map((message,index) => (
                    <p>{index} {message.subject}</p>
                ))
            }
        </div>
    )
}

```