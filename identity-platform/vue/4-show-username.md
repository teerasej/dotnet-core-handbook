
# ทำส่วนของการโชว์ชื่อ username

## 1. สร้างกลไกการเรียกดู account ที่ sign in  

```js
// src/services/auth.js

import * as msal from '@azure/msal-browser'

let msalApp

export default {
  //..

  user() {
    if (!msalApp) {
      return null
    }

    const currentAccounts = msalApp.getAllAccounts()
    if (!currentAccounts || currentAccounts.length === 0) {
      // No user signed in
      return null
    } else if (currentAccounts.length > 1) {
      return currentAccounts[0]
    } else {
      return currentAccounts[0]
    }
  },

}
```

## 2. เรียกใช้ใน component 

```js

methods: {
    async signIn() {
    try {
        await auth.login()
        // เรียกใช้คำสั่ง login แสดงชื่อ user
        this.user = auth.user()
    } catch (err) {
        console.log(err.toString())
    }
    }
}


```