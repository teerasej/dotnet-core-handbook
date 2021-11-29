
# เพิ่มปุ่ม Sign out 

เราจะเพิ่มปุ่มสำหรับ Sign out โดยใช้แนวทางเดียวกับปุ่ม Sign in

```html
<!-- src/app/home/home.component.html -->

<p>Hello</p>

<div *ngIf="!loginDisplay">
    <button (click)="login()">Sign In</button>
</div>


<div *ngIf="loginDisplay">
    <p>Login successful!</p>
    <button (click)="logout()">Wanna log out?</button>
</div>
```

เพียงแต่เราจะเรียกใช้ `logoutPopup()` ของ `MsalService` แทน

```cs
//  src/app/home/home.component.ts

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

  constructor(
    private authService: MsalService,
    private msalBroadcastService: MsalBroadcastService
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

  login() {
    this.authService.loginPopup()
      .subscribe({
        next: (result) => {
          console.log(result);
        },
        error: (error) => console.log(error)
      });
  }

  setLoginDisplay() {
    let accounts = this.authService.instance.getAllAccounts();
    this.loginDisplay = accounts.length > 0;
  }

  // เพิ่มส่วนของการ Log out 
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