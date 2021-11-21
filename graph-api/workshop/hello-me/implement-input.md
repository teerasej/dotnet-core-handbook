
# สร้างส่วนรับคำสั่งจาก console

ในไฟล์ **Program.cs** ให้สร้างส่วนรับค่าตัวเลขจาก console

```cs
int choice = -1;

while (choice != 0)
{
    Console.WriteLine("Please choose one of the following options:");
    Console.WriteLine("0. Exit");
    Console.WriteLine("1. Display access token");

    try
    {
        choice = int.Parse(Console.ReadLine());
    }
    catch (System.FormatException)
    {
        // Set to invalid value
        choice = -1;
    }

    switch (choice)
    {
        case 0:
            // Exit the program
            Console.WriteLine("Goodbye...");
            break;
        case 1:
            // Display access token
            break;
        default:
            Console.WriteLine("Invalid choice! Please try again.");
            break;
    }
}
```