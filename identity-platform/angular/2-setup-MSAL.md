
# ตั้งค่า Microsoft Authication Library

หลังจากที่เราทำการลงทะเบียน (App Registration) แบบ SPA ใน Active Directory แล้ว เราจะเอาข้อมูลมาใส่ไว้ใน `MsalModule`

ข้อมูลสำคัญได้แก่

- client Id
- authority สามารถหาได้จากส่วน tenant ID 
- redirectUri 

```ts
// src/app/app.module.ts

import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { MsalBroadcastService, MsalGuard, MsalInterceptor, MsalModule, MsalService } from '@azure/msal-angular';
import { BrowserCacheLocation, InteractionType, PublicClientApplication } from '@azure/msal-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { HomeComponent } from './home/home.component';

@NgModule({
  declarations: [
    AppComponent,
    HomeComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    // กำหนดค่าเพื่อใช้งานใน MsalModule
    MsalModule.forRoot(new PublicClientApplication({
      auth: {
        clientId: 'da05109d-e284-479d-ac09-45130ab06e1b', // This is your client ID
        authority: 'https://login.microsoftonline.com/2f85a92b-2b18-47a7-9ad3-32caebd8138a', // This is your tenant ID
        redirectUri: 'http://localhost:4200/'// This is your redirect URI
      },
      cache: {
        cacheLocation: BrowserCacheLocation.LocalStorage,
        storeAuthStateInCookie: true, // Set to true for Internet Explorer 11
      }
    }),
      {
        interactionType: InteractionType.Redirect
      },
      {
        interactionType: InteractionType.Redirect,
        protectedResourceMap: new Map([
          ['https://graph.microsoft.com/v1.0/me', ['user.read']],
        ])
      }
    )
  ],
  // กำหนด provider เพื่อใช้งานใน Component 
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    },
    MsalService,
    MsalGuard,
    MsalBroadcastService
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }


```