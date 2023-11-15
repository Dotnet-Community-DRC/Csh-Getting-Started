---
title: "Getting started with ASP.NET Core 7 Minimal APIs"
datePublished: Mon Jun 12 2023 10:50:15 GMT+0000 (Coordinated Universal Time)
cuid: clisqgdm2000609lc619pdrfg
slug: getting-started-with-aspnet-core-7-minimal-apis
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/xG8IQMqMITM/upload/1e2882f6942a1fff70d47469b37fdb6e.jpeg
tags: csharp, net, aspnet-core, getting-started, minimal-apis

---

Imagine you are a new chef, eager to make your mark in the culinary world. You have a passion for cooking, but you're just starting and don't have much experience yet. You want to create a new recipe that showcases your skills, but you're not quite sure where to start.

This is where Minimal APIs come in. Minimal APIs provide a simplified way to create web APIs that are lightweight and easy to use. In the same way that a new chef might start with a simple recipe that has few ingredients and minimal steps, Minimal APIs allow developers to create APIs with minimal code and configuration.

To get started with Minimal APIs, you don't need to be an experienced developer or have a lot of technical knowledge. With just a few lines of code, you can create an endpoint that responds to HTTP requests, whether it's a simple "Hello World" message or a more complex API that interacts with a database.

Minimal APIs are also flexible, so you can customize them to suit your specific needs. Whether building a small application for personal use or a large-scale enterprise system, Minimal APIs can provide the foundation you need to create a powerful and effective API.

So, whether you're a new or experienced developer who wants to get started with Minimal APIs, all you need is a basic understanding of programming concepts and a willingness to learn. With Minimal APIs, you can start small and build your way up to more complex applications, all while enjoying the simplicity and flexibility that this technology provides.

With that in mind, here are some of the requirements to get started:

1. IDE or Code Editor. In our case, we will use the famous visual studio code which is free and can be found [here.](https://code.visualstudio.com/)
    
2. The .NET 7 SDK can be found [here.](https://dotnet.microsoft.com/en-us/download/dotnet/7.0)
    
3. We will use C# as the programming language, so I will assume you have some knowledge. However, if you're not familiar with C#, don't worry, you can still benefit from this guide.
    

If you are all set, you should be able to do `dotnet --version` in your VS Code terminal and get the version of the dotnet SDK installed as such:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1687083194703/6e7fd4af-f218-4ba3-aa30-efff9b47cd78.png align="center")

Now, let's create our first API using the Minimal API architecture. All you need to do is to open your terminal and follow the steps below:

**Step 1**: Create a new project In your terminal, navigate to the directory where you want to create your project. Then, run the following command to create a new minimal API project:

`dotnet new web -n CookingAPI`

The command above creates an empty ASP.NET Core Minimal API called **CookingAPI** with a single endpoint that returns a Hello World from the route path endpoint.

**Step 2:** Open the project in your favorite code editor, such as Visual Studio Code or Rider in my case, and navigate to the `Program.cs` file. In this file, we'll define the routes and actions for our API.

Let's say you want to create an endpoint that returns a list of recipes. Add the following code inside the `Program.cs` file:

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/recipes", () =>
{
    // list of recipes
    var recipes = new List<string>
    {
        "Patate Douce",
        "Bugali na Sombe",
        "Sambaza an chips"
    };

    return recipes;
});

app.Run();
```

Above, we have used the **MapGet** method to define a route for the `/recipes` endpoint. Inside the endpoint action, you can add the logic to fetch the list of recipes from a data source. For simplicity, we have hardcoded a list of recipes as an example. In the next blog post, we will connect a data source and get the data from a database such as Sqlite or Postgres.

**Step 3:** Run the API

Save the changes and go back to your terminal in your VS Code. Navigate to the root directory of your project and run the following command:

`dotnet run`

dotnet run will build and run the API. Once it's up and running, let's open Postman or any favorite tool for testing your API. You should have something like this if you are using Postman:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1687082771744/960d10d4-b7d4-494e-ae87-58b804d0087b.png align="center")

In Postman, navigate to [http://localhost:5185/recipes](http://localhost:5185/recipes) using the GET method. On a side note, the port number of the URL might be different from mine. Mind and change according to your environment. Send the request, and you should receive a response containing a list of recipes as follows:

```csharp
[
    "Patate Douce",
    "Bugali na Sombe",
    "Sambaza an chips"
]
```

Congratulations! You have successfully created your first API using the Minimal API architecture. From here, you can expand and enhance your API by adding more routes, and actions, and integrating it with a database or other external services.

Remember, just like in cooking, practice makes perfect. Experiment with different endpoints, data sources, and functionalities to sharpen your API development skills. In our next blog post, we'll see how we can connect to a data source.

Keep cooking up some amazing APIs with ASP.NET Core 7 Minimal APIs! Remember, just like in cooking, practice makes perfect. To cook some amazing APIs, you can start with this introduction and then move to some of the most [advanced concepts](https://gist.github.com/davidfowl/ff1addd02d239d2d26f4648a06158727).

The source code for this introduction can be found [here](https://github.com/Dotnet-Community-DRC/CookingAPI).