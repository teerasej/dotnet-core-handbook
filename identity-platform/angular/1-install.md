
# ติดตั้ง และสร้างโปรเจค 

## 1. ติดตั้ง NodeJS

ดู[วิธีการติดตั้ง Node.js ได้จากที่นี่](https://github.com/teerasej/node-handbook/blob/master/install-nodejs.md) 

## 2. ติดตั้ง Angular CLI 

```bash
npm install -g @angular/cli
```

## 3. สร้างโปรเจคใหม่

รันคำสั่งเพื่อสร้างโปรเจคใหม่ 

```bash
ng new angular-graph
```

## 4. ติดตั้ง Package

รันคำสั่งด้านล่างเพื่อติดตั้ง package สำหรับทำงานกับ Microsoft Authentication Library

```bash
npm install @azure/msal-angular @azure/msal-browser
```

## 5. สร้างหน้า Home

รันคำสั่งด้านล่างเพื่อสร้างส่วนที่แยกเป็น User Interface หน้า home

```bash
ng g component home
```

## 5. เพิ่ม Home ใน Router

```c#
// src/app/app-routing.module.ts

import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  // เพิ่ม route สำหรับ home component 
  {
    path: '',
    component: HomeComponent
  },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }

```

## 6. เคลียน์ place holder UI ที่ติดมากับโปรเจค 

ให้เหลือแค่ด้านล่างพอ

```html
<!-- src/app/app.component.html -->

<router-outlet></router-outlet>

```