
# ทดสอบใช้งาน Microsoft Graph

ในส่วนนี้เราก็จะทดสอบเรียกข้อมูลจาก Microsoft Graph โดยใช้ HttpClient 

```html
<!-- src/app/home/home.component.html -->

<p>Hello</p>

<div *ngIf="!loginDisplay">
    <button (click)="login()">Sign In</button>
</div>


<div *ngIf="loginDisplay">
    <p>Login successful!</p>

    <!-- แสดงชื่อผู้ใช้ที่ sign in ด้วย Microsoft account -->
    <p>Hello, {{username}}</p>
    <button (click)="logout()">Wanna log out?</button>
</div>
```


```cs
// src/app/home/home.component.ts

import { MsalService, MsalBroadcastService } from '@azure/msal-angular';
import { Component, OnInit } from '@angular/core';
import { filter } from 'rxjs/operators';
import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';

// import class
import { HttpClient } from '@angular/common/http';


@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {

  loginDisplay = false;
  
  // แสดงส่วนของ username ในตอนเริ่มต้น 
  username = "...";


  // เรียกใช้งาน HttpClient 
  constructor(
    private authService: MsalService,
    private msalBroadcastService: MsalBroadcastService,
    private http: HttpClient
  ) { }

  ngOnInit(): void {

    this.msalBroadcastService.msalSubject$
      .pipe(
        filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
      )
      .subscribe((result: EventMessage) => {
        console.log(result);
      });

    this.msalBroadcastService.inProgress$
      .pipe(
        filter((status: InteractionStatus) => status === InteractionStatus.None)
      )
      .subscribe(() => {
        this.setLoginDisplay();
      })
  
  }

    // สร้าง method สำหรับส่ง request ไปที่ microsoft graph
   showUsername() {
    this.http.get('https://graph.microsoft.com/v1.0/me')
    .subscribe( (profile:any) => {
      // ดูข้อมูล
      console.log('profile', profile);

      // แสดง display name
      this.username = profile.displayName;
    });
      
  }

  login() {
    this.authService.loginPopup()
      .subscribe({
        next: (result) => {
          console.log(result);

          // เรียกแสดง username หลัง login เสร็จ
          this.showUsername();
        },
        error: (error) => console.log(error)
      });
  }

  setLoginDisplay() {
    let accounts = this.authService.instance.getAllAccounts();
    this.loginDisplay = accounts.length > 0;
  }

  logout() {
    this.authService.logoutPopup()
    .subscribe({
      next: (result) => {
        console.log(result);
      },
      error: (error) => console.log(error)
    });
  }


}

```