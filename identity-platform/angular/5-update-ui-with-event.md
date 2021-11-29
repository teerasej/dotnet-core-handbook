
# ใช้เงื่อนไขการแสดง UI โดยอาศัยการทำงานของ MSAL

โดย `MsalBroadcastService` จะเป็นตัวที่คอย broadcast การทำงานของ Msal Service เราจึงใช้ข้อมูลจาก event พวกนี้ในการปรับ UI ให้สอดคล้องกับสถานะการ Login ได้

```c#
// src/app/home/home.component.ts

import { MsalService, MsalBroadcastService } from '@azure/msal-angular';
import { Component, OnInit } from '@angular/core';
import { filter } from 'rxjs/operators';
import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {

  loginDisplay = false;

  // เรียกใช้ MsalBroadcastService
  constructor(
    private authService: MsalService,
    private msalBroadcastService: MsalBroadcastService
  ) { }

  ngOnInit(): void {

    // ตรวจจับ Event Login Success จาก msal broadcast service
    this.msalBroadcastService.msalSubject$
      .pipe(
        filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
      )
      .subscribe((result: EventMessage) => {
        console.log(result);
      });

    // ตรวจจับ Interaction Status
    this.msalBroadcastService.inProgress$
      .pipe(
        filter((status: InteractionStatus) => status === InteractionStatus.None)
      )
      .subscribe(() => {
        // ตั้งค่า Login display หลังจากที่การ login หรือ log out สิ้นสุดลง (InteractionStatus จะมีค่าเป็น None )
        this.setLoginDisplay();
      })
  
  }

  setLoginDisplay() {

    // เราสามารถเรียกใช้งาน auth service เพื่อขอดู account ที่ sign in อยู่ได้ 
    let accounts = this.authService.instance.getAllAccounts();
    // ถ้าพบว่ามี account ที่ sign in อยู่มากกว่า 1 ให้แสดงหน้าจอสำหรับผู้ใช้ 
    this.loginDisplay = accounts.length > 0;
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