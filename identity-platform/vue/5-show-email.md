
# ทำส่วนของการโชว์ Email

## 1. สร้างกลไกการขอ token  

```js
// src/services/auth.js

import * as msal from '@azure/msal-browser'

let msalApp

export default {
  //..

   async acquireToken(scopes = ['user.read']) {
    if (!msalApp) {
      return null
    }

    // กำหนด scope ของการขอ token
    const accessTokenRequest = {
      scopes,
      account: this.user()
    }

    let tokenResp
    try {
      // 1. ขอ token แบบเงียบกริบโดยไม่กวนผู้ใช้
      tokenResp = await msalApp.acquireTokenSilent(accessTokenRequest)
      console.log('### MSAL acquireTokenSilent was successful')
    } catch (err) {
      // 2. ถ้าขอเงียบไม่ได้ก็จะ pop up 
      tokenResp = await msalApp.acquireTokenPopup(accessTokenRequest)
      console.log('### MSAL acquireTokenPopup was successful')
    }

    // เช็ค response ว่ามี token ไหม
    if (!tokenResp.accessToken) {
      throw new Error("### accessToken not found in response, that's bad")
    }

    return tokenResp.accessToken
  },

}
```

## 2. สร้างกลไกการเรียกข้อมูลจาก Graph 

```js
// src/services/graph.js


import auth from './auth'

const GRAPH_BASE = 'https://graph.microsoft.com/v1.0'
const GRAPH_SCOPES =  ['User.Read', 'Mail.ReadBasic', 'Mail.Read']

let accessToken

export default {

    // สร้าง function สำหรับขอข้อมูล email
    async getEmails() {
        let response = await callGraph('/me/messages')
        if (response) {
            let data = await response.json()
            return data.value
        }
    },

}

async function callGraph(apiPath) {
    // ขอ token เพื่อเอามาใช้ request
    accessToken = await auth.acquireToken(GRAPH_SCOPES)

    let response = await fetch(`${GRAPH_BASE}${apiPath}`, {
        headers: { authorization: `bearer ${accessToken}` }
    })

    if (!response.ok) {
        throw new Error(`Call to ${GRAPH_BASE}${apiPath} failed: ${response.statusText}`)
    }

    return response
}
```

## 3. เรียกใช้ใน component 

```js

import graph from '../services/graph';

export default defineComponent({
  
  //...

  methods: {
    async signIn() {
      try {
        await auth.login()
        this.user = auth.user()

        // เรียกใช้และแสดง email
        this.messages = await graph.getEmails();
      } catch (err) {
        console.log(err.toString())
      }
    }
  }


});


```