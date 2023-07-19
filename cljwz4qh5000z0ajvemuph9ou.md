---
title: "How to send an email with ASP.NET Core Minimal API and MailKit"
datePublished: Mon Jul 10 2023 14:43:55 GMT+0000 (Coordinated Universal Time)
cuid: cljwz4qh5000z0ajvemuph9ou
slug: how-to-send-an-email-with-aspnet-core-minimal-api-and-mailkit
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/h7F8eOLQSX4/upload/1b4e69751e047557d3ee8c3bf3d8aaba.jpeg
tags: net, aspnet-core, minimal-apis, send-email, mailkit

---

Hello everyone, I trust you're all doing well. Today, we're going to explore how to send an email from an ASP.NET Core Minimal API using MailKit from scratch.

Assuming you have all the necessary tools installed, including the .NET SDK and an Integrated Development Environment (IDE), we can proceed. For this post, I'll be using .[NET SDK](https://dotnet.microsoft.com/en-us/download/dotnet/7.0) 7 and [JetBrains Rider](https://www.jetbrains.com/rider/download/#section=mac) (This is a paid product but you can get a month of free trial), you could use [Visual Studio Code](https://code.visualstudio.com/) or [Visual Studio 2022 Community](https://visualstudio.microsoft.com/vs/) version which is totally free.

Let's dive in and start with our first step:

**Step 1. Create a new Project.**

Begin by creating a new ASP.NET Core Web API project using the terminal. Open the terminal, navigate to your preferred directory using the cd command, and create a new project as follows:

`dotnet new webapi -o SendingEmail`

With the command above, we have scaffolded a new web API template which we'll use as our starting point and since we are working with Minimal APIs, we don't require the `controller` folder and the `WeatherForecast` class, so we will get rid of them.

**Step 2. Define the Email Data Transfer Object (DTO)**

We need to define an Email Data Transfer Object (DTO) model for effectively passing data from our API to the mail server. In this context, we're using a DTO to carry email data from our API to the mail server, which will then send an email to the receiver.

Under the project directory, let's create a new folder called `Models`. Within this folder, let's add a new class named `EmailDto` with the following properties:

```csharp
namespace SendingEmail.API.Models;

public class EmailDto
{
    public string From { get;  set; }
    public string To { get;  set; } 
    public string  Subject { get;  set; } 
    public string Body { get;  set; }
}
```

**Step 3: Create the Email Service Interface**

Under the project root, let's create a folder named `Services.` Within this folder, let's create an interface called `IEmailService` as follows:

```csharp
namespace SendingEmail.API.Services;

public interface IEmailService
{
    Task SendEmailAsync(EmailDto request);
}
```

The interface serves as a contract for our Email Service and should be implemented by any class that acts as such service. But before implementing this interface, we need to add the MailKit package by navigating to the project root and running the following command in your terminal:

`dotnet add package MailKit`

Following the interface definition, we need now to add the implementation of our email service. Add a class in the `Services` folder and add the implementation as follows:

```csharp
using MailKit.Net.Smtp;
using MailKit.Security;
using MimeKit;
using MimeKit.Text;

namespace SendingEmail.API.Services;

public class EmailService : IEmailService
{
    private readonly IConfiguration _config;
    public EmailService(IConfiguration config)
    {
        _config = config;
    }
    public async Task SendEmailAsync(EmailDto request)
    {
        var email = new MimeMessage(); 
        email.From.Add(MailboxAddress.Parse(request.From));
        email.To.Add(MailboxAddress.Parse(request.To));
        email.Subject = request.Subject;
        email.Body = new TextPart(TextFormat.Html) {Text = request.Body};

        using var smtp = new SmtpClient();
        await smtp.ConnectAsync(_config["EmailHost"], 587, SecureSocketOptions.StartTls);
        await smtp.AuthenticateAsync(_config["EmailUsername"], _config["EmailPassword"]);
        await smtp.SendAsync(email);
        await smtp.DisconnectAsync(true);
    }
}
```

**Step 4: Configure the API Endpoint**

Now that we have our email service set up, we will create an endpoint that utilizes this service to handle email-sending requests.

We aim to MapPost route `/email` for sending emails:

```csharp
//...
app.MapPost("/email", (EmailDto request, IEmailService emailService) =>
{
    emailService.SendEmail(request);
    return Results.Ok();
});
// ...
```

You will notice that we have the body of our POST request and the email service injected. Our Program.cs file should look as follows including the injection of our IEmailService interface and its implementation(EmailService):

```csharp

global using SendingEmail.API.Services;
global using SendingEmail.API.Models;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();
builder.Services.AddScoped<IEmailService, EmailService>();

var app = builder.Build();

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

app.UseCors();

app.UseAuthorization();

app.MapControllers();

app.MapPost("/email", (EmailDto request, IEmailService emailService) =>
{
    emailService.SendEmailAsync(request);

    return Results.Ok();
});
app.Run();
```

Here, we are mapping an HTTP POST request to /email route. Whenever a POST request comes to /email, our app will call the SendEmail method from our email service using the data received in the request.

**Step 5: Set Up Application Configurations**

The final step is to ensure that our application has the correct configuration to send an email. We store these settings in our appsettings.json file:

```csharp
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "EmailHost": "smtp.ethereal.email",
  "EmailUsername": "rosamond.schoen50@ethereal.email", 
  "EmailPassword": "R8fARwUE7PHFHXuvrZ",
  "AllowedHosts": "*"
}
```

We use Ethereal email, an online fake SMTP service, for this purpose. It helps confirm that the application is sending emails correctly without them being delivered to the recipient.

You need to create a free account on [Ethereal](https://ethereal.email/) to start testing. Once the account is set up, they provide a username and password, which you should update in the appsettings.json file.

**Step 6: Test with Swagger**

Since our ASP.NET Core Minimal API project was scaffolded with Swagger preconfigured, it makes API testing a breeze. Swagger offers an interactive environment allowing the users to send HTTP requests to our endpoints and view the responses directly from the browser without needing any extra tooling such as Postman. To use Swagger, once we have our application running (which could be initiated by either clicking run in JetBrains Rider IDE or using the dotnet run command in terminal), we can navigate to [https://localhost:{yourPort}/swagger/index.html](https://localhost:5001/swagger/index.html) in our web browser. On the Swagger page, under `POST /email`, click `Try it out`, fill the request body with your preferred email Data Transfer Object (DTO) that includes the desired From, To, Subject, and Body attributes in JSON format, then click `Execute`. Swagger will send a POST request to our /email endpoint just as any client application would do. Thanks to the visibility provided by Swagger, you can immediately view the response to your request. A Response Code of 200 and a resultant message indicate that the email was submitted successfully. With tools like Swagger integrated in your development process, API testing becomes efficient and straightforward.

**Conclusion**

In this post, we have explored how to send an email from an ASP.NET Core Minimal API using MailKit. We started by setting up a new project and creating a Data Transfer Object (DTO) to pass email data to the mail server. We then defined an Email Service interface and implemented it using MailKit to handle the email sending functionality.

We configured the API endpoint to handle email-sending requests, allowing clients to send emails by making a POST request to the /email route. We also set up the application configurations, including the email host, username, and password, which can be customized based on your specific email provider.

Sending emails from your API can be a powerful feature that enables communication and enhances user experiences. I hope this pos has been helpful in guiding you through the process. Happy coding! 🙏🏾