---
# try also 'default' to start simple
theme: seriph
background: https://i.kym-cdn.com/entries/icons/original/000/035/125/bill.jpg
class: 'text-center'
highlighter: shiki
---

# .NET, a soft introduction

soft because .net is confusing

---
layout: image-right
image: https://external-preview.redd.it/8bC44APhvVYTnZ_6nXdCzsOj-HduRZDeWjf6CMLNF34.jpg?auto=webp&v=enabled&s=f1ffbebbd5078125c3b266fa220090d052e22c47
---

# what the heck is .net?

<br>

.NET is a platform for building pretty much anything.

There used to be a bunch of different ways to run .NET:
- .NET Framework (Windows)
- .NET Core (new & open source)
- Mono / Xamarin (cross platform / used for mobile)

Now, it has been unified into .NET Core, which is a single SDK.

<br>
<br>

<!--
- Languages - C#, F#, VB
- Runtimes - Core CLR
- Libraries - Lots of BCL that are built-in and you bring them in by bringing in namespaces, 3rd party, etc
-->

<style>
h1 {
background-color: #2B90B6;
background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
background-size: 100%;
-webkit-background-clip: text;
-moz-background-clip: text;
-webkit-text-fill-color: transparent;
-moz-text-fill-color: transparent;
}
</style>

---

# Toolchain 

.NET offers a few tools to help with developing .NET applications

`dotnet` CLI 
- `dotnet new <TEMPLATE> --name <PROJECT_NAME>`
- `dotnet build`
- `dotnet run`
- `dotnet watch`
    - This is my favorite command because it will perform a build and run and then also do some filesystem stuff to recompile your code

`nuget` package manager
- `dotnet add package Htmx --version 1.1.3`

<small>Or... you can use Visual Studio...which is OK...</small>

<style>
small {
    font-size: 8px;
}
</style>

<!--
After a few weeks of using the `dotnet` CLI
- It's pretty good
- Has helpful Documentation
- Kinda like your standard entrypoint tool ./manage.py <cmd> / rails <cmd> 

A side note about using Visual Studio:
-->

---

# Web Development 

more relevant stuff?

ASP.NET extends the .NET ecosystem with tools & libraries specifically for bulding web applications.

you can do most things you'd expect:
- web apps
- apis
- real-time
- microservices

.NET has solutions for a lot of server and client side projects
- Blazor (javascript-less UI framework, build apps with C#, HTML & Razor templates)
- Razor Pages (page-based apps, no need for views/controllers)
- Razor Views (HTML templates, using .cshtml extension)
- Built-in support for JS UI frameworks (:O)

<!--
There is this benchmark, don't know but it looks like aspcore stuff ranks pretty high which is cool to see
https://www.techempower.com/benchmarks/#hw=ph&test=plaintext&section=data-r21

side bonus(?): 
besides all the marketing material of it being fast as heckz they give you
10 ASP.NET websites for free with Microsoft Azure 
-->

---

# Documentation
is nice

Pretty extensive documentation with several examples, API docs, terminology, etc

- [Examples](https://learn.microsoft.com/en-us/aspnet/core/?view=aspnetcore-7.0&WT.mc_id=dotnet-35129-website)
- [API Docs](https://learn.microsoft.com/en-us/dotnet/api/?view=aspnetcore-5.0)

<!--
Just a small, quick slide about their documentation.

I've found myself in the documentation a lot over the past several weeks. They do well to explain
typical concepts but then also delve into the .NET-way of doing things.

show docs in neovim
-->

---

# Fundamentals
Typical stuff in .NET projects

With most web templates you use in the `dotnet new` command, .NET 7 projects will generate a pretty consistent filesystem structure.

- Program.cs
- appsettings.json
- .csproj

*show the directories*

<!--
The nuances come in once you decide how you want to build/serve your frontend.

The `dotnet` tool gives us access to a lot of project templates that we can spin up. 

- `mvc`
- `web`
- `webapp`
- `react`
-->

---

# Program.cs
Contains the application startup code

The main purpose of our Program.cs file is to:

1. Register [services](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/?view=aspnetcore-7.0&tabs=macos#dependency-injection-services) required by our application
    - Leans on dependency injection to register services throughout the application
    - Services are added to our `WebApplicationBuilder.Services` instance
    - Services could be framework-provided (like mapping Razor pages, etc), or stuff like adding our DB context

Once services are added, we build our `Microsoft.AspNetCore.Builder.WebApplication` instance.

2. Configure the request handling pipeline through a series of [middleware components](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/?view=aspnetcore-7.0&tabs=macos#middleware)
    - Each component performs stuff on an `HttpContext` object

<!--
Previously, this was split across two startup files which were Startup and Program (or something like that).

This main startup file is used across various different .NET project types (razor, MVC, API, etc).

With our builder object, we add services via the `Services`
https://learn.microsoft.com/en-us/aspnet/core/fundamentals/?view=aspnetcore-7.0
-->

---

# Program.cs (cont.)
```csharp
// Program.cs - More typical...
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddRazorPages();
builder.Services.AddControllersWithViews();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Error");
    app.UseHsts();
}

app.UseHttpsRedirection();
app.UseStaticFiles();

app.UseAuthorization();
...
```

---

```csharp
// Program.cs - Basic Example
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/", () => "Hello World!");

app.Run();
```

---

# Project Configuration

.NET projects are configured using configuration sources. The two main ones are:

1. appsettings.json
    - Is one of the [providers](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-7.0#default-application-configuration-sources) that stores configuration values.

```json
{
  "Logging": {
      "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
      }
    },
    "AllowedHosts": "*"
}
```

---

# Project Configuration (cont)

2. .csproj [project file](https://learn.microsoft.com/en-us/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/understanding-the-project-file)
    - XML-based document
    - Describes project dependencies
    - Informs the Microsoft Build Engine (MSBuild) how to build the project

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>net7.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>
</Project>

<ItemGroup>
    <PackageReference Include="Microsoft.Data.Sqlite.Core" Version="7.0.8" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="7.0.8">
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
      <PrivateAssets>all</PrivateAssets>
    </PackageReference>
    <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="7.0.8" />
</ItemGroup>
```

<!--
These can get pretty verbose and big. Take a look at the React template version
-->

---

# [Routing](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/routing?view=aspnetcore-7.0)
Matches incoming HTTP requests and dispatches them to the app's executable endpoints

Apps can configure routing using various options:
- Controllers
- Razor Pages
- Delegates or Lambdas

```csharp
app.MapGet("/", () => "Hello World!");

# Register Controllers
builder.Services.AddControllersWithViews();

# Or register Razor pages
builder.Services.AddRazorPages();
```

<!--
Routing seems pretty intuitive. I havent done anything complicated with the system yet but their docs seem to cover the subject well
-->

---

# .NET MVC Template
The initial directory structure is pretty well laid out.

```shell
├── Controllers
├── Data
├── Migrations
├── Models
├── Program.cs
├── Properties
├── Todo.csproj
├── Views
├── appsettings.Development.json
├── appsettings.json
└── wwwroot
```

- `Controllers` will house our applications controllers which handle our web requests
- `Data` is an EF Core convention for storing our database context, but can also house service objects here
- `Models` will contain any models as defined by EF Core
- `Views` link back to a controller and will contain the `.cshtml` template files
- `wwwroot` houses [static files](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/static-files?view=aspnetcore-7.0)

<!--
I mentioned there were various templates earlier. This is the template for the mvc project.
-->

---

# Models
Entity Framework Core

EF Core is the ORM.
- Maps data to .NET objects
- Enables DB access through model files, migrations, DB connections

<!--
My experience thus far has been models backed with Entity Framework Core.

EF Core works similarly to the frameworks we know and love. Django Models / Rails ActiveRecord / Elixir Ecto.

If I had to compare the data layer with another framework, it definitely so far compares similarly to Django. But I have yet
to really dive into EF Core so initial impressions are OK.
-->

---

# [Controllers](https://learn.microsoft.com/en-us/aspnet/core/mvc/controllers/actions?view=aspnetcore-7.0)
Registered in Program.cs

Handles the web request
- Every `public` method in a controller is callable as an HTTP endpoint.
- Access to the HTTP lifecycle object via `HttpContext`

The convention is to
- Place controllers in the `Controllers` directory
- Inherit from `Microsoft.AspNetCore.Mvc.Controller`

<!--
Typical stuff you'd expect from a controller implementation
-->

--- 

# Views
Data presentation

HTML template embedded with Razor markup.

Razor
- Markup syntax for embedding .NET code into webpages

---

# fin
thanks y'all

<!--

-->
