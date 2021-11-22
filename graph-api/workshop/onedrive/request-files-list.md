
# request-files-list.md

## 1. สร้าง Method ใน Helper

เปิด **OneDriveHelper.cs** และเพิ่ม method ด้านล่างลงไป 

```cs
public static async Task<IDriveItemChildrenCollectionPage> GetUserDriveItemsAsync()
{
    try
    {
        return await graphClient.Me.Drive.Root.Children.Request().GetAsync();
    }
    catch (ServiceException ex)
    {
        Console.WriteLine($"Error getting user's drive items: {ex.Message}");
        return null;
    }
}
```

## 2. เรียกใช้ในแอพพลิเคชั่น

ในที่นี้เราจะเพิ่มตัวเลือกที่ 5 ให้กับเมนูของเรา จากนั้นให้รันทดสอบแบบ Debug 

```cs
while (choice != 0)
{
    Console.WriteLine("Please choose one of the following options:");
    
    //..

    Console.WriteLine("---- OneDrive ----");

    // เพิ่มตัวเลือกที่ 5
    Console.WriteLine("5. List All Files");


    try
    {
        choice = int.Parse(Console.ReadLine());
    }
    catch (System.FormatException)
    {
        choice = -1;
    }

    switch (choice)
    {
        //..

        case 5:
            var onedriveItems = await OneDriveHelper.GetUserDriveItemsAsync();
            Console.WriteLine($"Items in Drive: {onedriveItems.Count}");
            
            for (int i = 0; i < onedriveItems.Count; i++)
            {
                DriveItem driveItem = onedriveItems[i];

                var itemType = "File";
                if(driveItem.Folder != null) {
                    itemType = "Folder";
                }

                Console.WriteLine($"{i + 1}: ({itemType}) {driveItem.Name}");
            }

            Console.WriteLine("\n");
            break;

        //..
    }
}
```

## ลองของ

1. ลองสร้าง Directory เปล่าลงไปใน OneDrive ของ Account ที่ใช้ทดสอบ
2. ทดสอบรันแอพพลิเคชั่น และสังเกตผลที่ได้

## ลองของ 2 

1. ทำการอัพโหลดไฟล์ลงไปใน Directory ที่สร้างขึ้นก่อนหน้านี้
2. ทดสอบรันแอพพลิเคชั่น แบบ debug session และสังเกตผลที่ได้
