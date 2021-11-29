
# ทำส่วนของการ sign in

## 1. สร้างกลไกการเปิด popup 

```js
// src/services/auth.js

import * as msal from '@azure/msal-browser'

// MSAL object used for signing in users with MS identity platform
let msalApp

export default {
  //..

  async login(scopes = ['User.Read', 'Mail.ReadBasic', 'Mail.Read']) {
    if (!msalApp) {
      return
    }

    await msalApp.loginPopup({
      scopes,
      prompt: 'select_account'
    })
  },

}
```

## 2. เรียกใช้ใน component 

```vue
<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Vue Graph</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <div id="container">
        <div v-if="!user.name">

            <!-- เรียกใช้คำสั่ง signin -->
          <ion-button @click="signIn()">Sign in</ion-button>
        </div>
        <div v-if="user.name">
          <p>Hello, {{ user.name }}</p>
          <ul>
            <li v-for="item in messages" :key="item.id">
              {{ item.subject }}
            </li>
          </ul>
          <ion-button>Sign out</ion-button>
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script>
import {
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonToolbar,
} from "@ionic/vue";
import { defineComponent } from "vue";
import auth from '../services/auth';

export default defineComponent({
  name: "Home",
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
  },

  data: function () {
    return {
      user: {},
      messages: [
        { subject: "Learn JavaScript" },
        { subject: "Learn Vue.js" },
        { subject: "Build Something Awesome" },
      ],
    };
  },

  async created() {
    let appClientID = "18942c9f-a155-4f47-8a2e-26a9bec3a45f";
    let tenantId = "2f85a92b-2b18-47a7-9ad3-32caebd8138a";
    auth.configure(appClientID, tenantId);
    // Restore any cached or saved local user
    //this.user = auth.user();
  },

  // เรียกใช้คำสั่ง login
  methods: {
    async signIn() {
      try {
        await auth.login()
      } catch (err) {
        console.log(err.toString())
      }
    }
  }


});
</script>

<style scoped>
#container {
  text-align: center;

  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;

  color: #8c8c8c;

  margin: 0;
}

#container a {
  text-decoration: none;
}
</style>
```