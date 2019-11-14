
# สร้างโปรเจค Web API และทดสอบใช้งาน

## 1. สร้างโปรเจค

สร้างโปรเจคโดยใช้คำสั่ง 

```bash
dotnet new webapi -o UserAPI
```

ถ้ามีการถามว่า 

```bash
Required assets to build and debug are missing from 'BooksApi'. Add them?.
```
ให้ตอบว่า **Yes**

เสร็จแล้วให้เปิด directory ของโปรเจค โดยการคลิกขวาที่ directory และเลือก **Open in Terminal**

## 2. ทดสอบรันโปรเจค

กด **F5** และเลือก **.NET Core**

## 3. ทดสอบเปิด Web Browser

เปิดเว็บ https://localhost:5001/WeatherForecast จะเห็นข้อมูลแสดงขึ้นมาแบบนี้ 

```json

  {
    "date": "2019-11-15T20:15:35.952057+07:00",
    "temperatureC": 53,
    "temperatureF": 127,
    "summary": "Bracing"
  },
  {
    "date": "2019-11-16T20:15:35.952482+07:00",
    "temperatureC": -20,
    "temperatureF": -3,
    "summary": "Cool"
  },
  {
    "date": "2019-11-17T20:15:35.952488+07:00",
    "temperatureC": -18,
    "temperatureF": 0,
    "summary": "Balmy"
  },
  {
    "date": "2019-11-18T20:15:35.952489+07:00",
    "temperatureC": 52,
    "temperatureF": 125,
    "summary": "Scorching"
  },
  {
    "date": "2019-11-19T20:15:35.952489+07:00",
    "temperatureC": 7,
    "temperatureF": 44,
    "summary": "Hot"
  }
]
```
