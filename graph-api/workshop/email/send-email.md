
# การส่ง Email

## เรียกใช้คำสั่งในการส่ง Email

เปิดไฟล์ Program.cs และเพิ่มตัวเลือกที่ 4 เข้าไป

```cs

using Microsoft.Extensions.Configuration;
using Microsoft.Graph;
using simple_graph_console;
using System.IO;


//..

while (choice != 0)
{
    Console.WriteLine("Please choose one of the following options:");
     
    //..

    Console.WriteLine("---- Email ----");
    Console.WriteLine("3. List Emails");
    Console.WriteLine("4. Send an Email");


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

        // ตัวเลือกที่ 4 
        case 4:
            Console.WriteLine("Recipient Email:");
            var recipientEmailAddress = Console.ReadLine();
            
            Console.WriteLine("Subject:");
            var subject = Console.ReadLine();

            Console.WriteLine("Content:");
            var content = Console.ReadLine();

            Console.WriteLine("Sending...");
            await EmailHelper.SendSimpleEmailAsync(recipientEmailAddress, subject, content);
            Console.WriteLine("Sent!\n");
            break;

        //..
    }
}

```

## ไฟล์เต็ม 

```cs
using Microsoft.Extensions.Configuration;
using Microsoft.Graph;
using simple_graph_console;
using System.IO;


var appConfig = new ConfigurationBuilder()
        .AddUserSecrets<Program>()
        .Build();

if (string.IsNullOrEmpty(appConfig["appId"]) ||
    string.IsNullOrEmpty(appConfig["scopes"]))
{
    Console.WriteLine("Missing or invalid appsettings.json...exiting\n");
    return;
}

var appId = appConfig["appId"];
var scopesString = appConfig["scopes"];
var scopes = scopesString.Split(';');

GraphHelper.Initialize(appId, scopes, (code, cancellation) =>
{
    Console.WriteLine(code.Message);
    return Task.FromResult(0);
});

var accessToken = GraphHelper.GetAccessTokenAsync(scopes).Result;
Console.WriteLine("Signed In...\n");


// Email Helper
EmailHelper.Initialize(GraphHelper.graphClient);


int choice = -1;

while (choice != 0)
{
    Console.WriteLine("Please choose one of the following options:");
    Console.WriteLine("0. Exit");
    Console.WriteLine("1. Display access token");
    Console.WriteLine("2. Hello, Me.");
    Console.WriteLine("---- Email ----");
    Console.WriteLine("3. List Emails");
    Console.WriteLine("4. Send an Email");


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
        case 0:
            Console.WriteLine("Goodbye...");
            break;
        case 1:
            Console.WriteLine($"Access token: {accessToken}\n");
            break;
        case 2:
            var user = await GraphHelper.GetMeAsync();
            Console.WriteLine($"Hi, {user.DisplayName}\n");
            break;

        case 3:
            var emails = await EmailHelper.GetEmailsAsync(20);
            for (int i = 0; i < emails.Count; i++)
            {
                Message message = emails[i];

                Console.WriteLine($"{i + 1}: message {message.Id}");
                Console.WriteLine($"   Sender: {message.Sender.EmailAddress.Name} ({message.Sender.EmailAddress.Address})");
                Console.WriteLine($"   Subject: {message.Subject}");


                if ((bool)message.HasAttachments)
                {
                    Console.WriteLine($"   Attachments: {message.Attachments.Count}");

                    foreach (var attachment in message.Attachments.CurrentPage)
                    {
                        if(attachment is FileAttachment) {
                            var fileAttachment = attachment as FileAttachment;
                            System.IO.File.WriteAllBytes(fileAttachment.Name, fileAttachment.ContentBytes);
                        }
                    }
                }

                Console.WriteLine("\n");
            }

            break;

        case 4:
            Console.WriteLine("Recipient Email:");
            var recipientEmailAddress = Console.ReadLine();
            
            Console.WriteLine("Subject:");
            var subject = Console.ReadLine();

            Console.WriteLine("Content:");
            var content = Console.ReadLine();

            Console.WriteLine("Sending...");
            await EmailHelper.SendSimpleEmailAsync(recipientEmailAddress, subject, content);
            Console.WriteLine("Sent!\n");
            break;

        default:
            Console.WriteLine("Invalid choice! Please try again.");
            break;
    }
}

```