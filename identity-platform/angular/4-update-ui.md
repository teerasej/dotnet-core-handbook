
# กำหนดตัวแปรเป็นเงื่อนไขการแสดง UI

เราจะสร้างตัวแปร เพื่อใช้ในการสลับ UI ของ component ตามสถานะการ sign in

```html
<!-- src/app/home/home.component.html -->

<!-- ใช้ property loginDisplay ในการแสดงปุ่ม sign in -->
<div *ngIf="!loginDisplay">
    <button (click)="login()">Sign In</button>
</div>


<div *ngIf="loginDisplay">
    <p>Login successful!</p>
</div>
```

ในส่วน TypeScript ก็ประกาศตัวแปรไว้

```ts
// src/app/home/home.component.ts

import { MsalService } from '@azure/msal-angular';
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {
  
  // กำหนดค่า Login Display
  loginDisplay = false;

  constructor(
    private authService: MsalService
  ) { }

  ngOnInit(): void {
  }

  login() {
    this.authService.loginPopup()
      .subscribe({
        next: (result) => {
          console.log(result);
        },
        error: (error) => console.log(error)
      });
  }


}

```