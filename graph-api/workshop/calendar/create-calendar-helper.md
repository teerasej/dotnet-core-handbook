
# สร้าง Calendar Helper

ในที่นี้เราจะสร้าง Class ชื่อ **CalendarHelper** เพื่อแยกจัดการโค้ดเฉพาะส่วนของการเข้าถึง Calendar นะครับ

## 1. ทำให้ GraphHelper สามารถแชร์ client ให้ helper ตัวอื่นได้

เนื่องจาก `GraphServiceClient` ของ **GraphHelper** เป็นตัวที่ใช้ติดต่อกับ Microsoft Graph API โดยตรง เราจึงจะนำมันมาใช้ใน CalendarHelper ด้วย เพื่อลดความซ้ำซ้อน

ดังนั้นเราจะปรับ ให้ GraphServiceClient ในไฟล์ GraphHelper.cs เป็น public เพื่อให้ง่ายต่อการฝึกทำ

> ในกรณีอื่นๆ เราสามารถออกแบบให้ GraphServiceClient ไปอยู่ใน Single Pattern หรือเข้าถึงในรูปแบบอื่นๆ ได้นะ ไม่ต้องตามแบบฝึกหัดนี้ก็ได้ 

```cs
namespace simple_graph_console
{
    public class GraphHelper
    {
        //...
        public static GraphServiceClient graphClient;
        //...
    }
}
```

## 2. สร้าง Calendar 

สร้างไฟล์ชื่อ **CalendarHelper.cs** และกำหนดให้พร้อมสำหรับการรับ **GraphServiceClient** มาใช้งาน

```cs

using Microsoft.Graph;

namespace simple_graph_console
{
    public class CalendarHelper
    {
        private static GraphServiceClient graphClient;

        public static void Initialize(GraphServiceClient client)
        {
            graphClient = client;
        }

    }
}
```

## 3. ส่งผ่าน ให้ CalendarHelper

เปิดไฟล์ **Program.cs**

ดังนั้นตอนที่เราทำการ sign in เรียบร้อยแล้ว เราจะก็ส่งผ่าน **GraphServiceClient** ให้กับ EmailHelper เพื่อเอาไปใช้

```cs
//...
// ส่วนที่เราร้องขอ token 
var accessToken = GraphHelper.GetAccessTokenAsync(scopes).Result;
Console.WriteLine("Signed In...\n");


// ส่งผ่านให้กับ Calendar Helper ตรงนี้แล้วกัน กำลังดี 
CalendarHelper.Initialize(GraphHelper.graphClient);


int choice = -1;
//...
```