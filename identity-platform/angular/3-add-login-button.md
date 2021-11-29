
# เพิ่มปุ่ม Sign In

ในที่นี้เราจะเพิ่มปุ่มในส่วน home component 

```html
<!-- src/app/home/home.component.html -->

<p>Hello</p>

<button (click)="login()">Sign In</button>

```

และเรียกใช้ MSAL Service เพื่อเริ่มกระบวนการ sign in 

```c#
// src/app/home/home.component.ts

import { MsalService } from '@azure/msal-angular';
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {

    // เรียกใช้ Masl service
  constructor(
    private authService: MsalService
  ) { }

  ngOnInit(): void {
  }

  login() {
    // แสดง pop up สำหรับ sign in
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