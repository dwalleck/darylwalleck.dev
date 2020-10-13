+++
author = "Daryl Walleck"
title = "Bootstrapping a .NET project from the command line"
date = "2020-10-12"
description = "A brief description of Hugo Shortcodes"
tags = [
    ".NET",
    "Command Line",
]
thumbnail = "images/dotnet-bot.png"

+++

From the day Visual Studio Code was released, I've been a huge fan. At that
time, I mostly worked with Python projects, which played well with the concept
of a lightweight editor. Running tests or code all happened from the command
line, which I became used to over the next several years as my projects adopted
more other command-line based tools like Terraform.

As .NET Core started to take off, I felt the tug to get back up to speed on all
things C#. I had grown attached to VS Code over the years, so I challenged
myself to continue using it rather than switching back to Visual Studio.
However, all of my previous experience with creating new .NET projects involved
the "File -> New Project" menu in Visual Studio, so I decided to compromise. I
used Visual Studio to create solutions, projects, and templated classes like API
controllers and used VS Code for coding. However, this felt _very wrong_.

The better answer was the `dotnet` command-line tool, which was added with .NET
Core. When I started several new personal projects earlier this year, I decided
to take the time to better understand the `dotnet` command line experience and
find alternative ways to handle tasks that I depended on Visual Studio for.

## Solution Management

When using `dotnet new` to create a new project, one of the first things I
noticed missing from the project was a solution file. Given that `new` usually
creates a single project, a solution file isn't always necessary. However,
if you end up creating several projects or are working with someone using Visual
Studio, having a solution file becomes more useful. I was pleased to learn that
`dotnet new` can generate solution files as well.

```powershell
dotnet new webapi -n CliDemo
cd CliDemo
dotnet new sln
```

Perfect! This creates an empty solution file, so there is a bit more work to be
done. While you could manually edit the solution to add a reference to your
projects, the `dotnet sln` command is a more reliable way of adding, listing,
and deleting projects from the solution.

```powershell
❯ dotnet sln add .\CliDemo.csproj
Project `CliDemo.csproj` added to the solution.
```

Now we can run `dotnet sln list` to verify the project was added successfully.

```powershell
❯ dotnet sln list
Project(s)
----------
CliDemo.csproj
```

## Source Control

While adding source control to a project is a quick `git init` away, there's
still one thing missing: a .gitignore file. While you could track
down the official GitHub repo that has .gitignore files for most language types
or just copy one from an old project, the .NET team has thankfully added a
`dotnet new gitignore` command to generate the file for you. It's one of those
nice additions that save time and doesn't leave you wondering if you found the
"right" .gitignore file.

## Global Tools

Where the scope `dotnet` command ends, the ecosystem of .NET Core tools step in
to fill the gaps. If this is the first time you've heard of .NET Core tools,
they are extensions to the .NET CLI that add new command line programs that
solve problems related to developing .NET Core applications (an unofficial
list of tools can be found [here](https://github.com/natemcmaster/dotnet-tools)).

## Package Management

Replicating Visual Studio's NuGet package manager experience turned out to be
less challenging than I thought. The NuGet website provides an easy way to
search for packages that has the additional benefit of providing the command
that needs to be run to add a reference to the package. The nagging issue I
had was how to find out out if versions of my packages were out of date. A bit
of research lead me to the NuKeeper global tool which provided exactly what I
needed.

```powershell
nukeeper inspect
Found 5 packages
Found 5 packages in use, 5 distinct, in 1 projects.
Json.Net, Microsoft.EntityFrameworkCore.Design, Microsoft.EntityFrameworkCore.SqlServer, Microsoft.VisualStudio.Web.CodeGeneration.Design, Swashbuckle.AspNetCore
Found 2 possible updates
Microsoft.EntityFrameworkCore.SqlServer from 3.1.4 to 3.1.8 in CliDemo.csproj
Swashbuckle.AspNetCore from 5.5.1 to 5.6.3 in CliDemo.csproj

Found 2 package updates
Microsoft.EntityFrameworkCore.SqlServer to 3.1.8 from 3.1.4 in 1 place since 1 month ago.
Swashbuckle.AspNetCore to 5.6.3 from 5.5.1 in 1 place since 19 days ago.
```

Running `nukeeper inspect` provides a report on which packages are behind their
latest versions without performing any updates. If everything looks in order,
executing `nukeeper update` updates the version numbers in the csproj file and
downloads the new package versions.

## Database Migrations

This was the easiest t

There are two tools that I found to be especially useful to replicate
functionality that I was used to in Visual Studio. If you've worked with Entity
Framework before, you're likely used to switching to the package manager console
to run add or run migrations. For the command line world, there is thankfully a
`dotnet ef` tool that provides the same functionality.

```powershell
dotnet ef migrations add FirstMigration
dotnet ef database update
```

### Scaffolding

I'll be honest: I can't write a .NET Core Web API Controller from scratch.
My muscle memory expects right clicking the Controllers directory to pop up the
handy `Add -> Controller` link that Visual Studio provides.

{{< figure src="/images/AddControllerEntityFramework.png"
    alt="Picture of the Add Controller dialog from Visual Studio" >}}

By coincidence, I happened upon a [workshop](https://github.com/csharpfritz/aspnetcore-app-workshop)
that introduced me to the `dotnet-aspnet-codegenerator` global tool which
provides the same capabilitiy. This tool has a dependency on another NuGet
package to run properly, so the first command should be run from the directory
of the project you are working on.

dotnet aspnet-codegenerator controller -api -name ToolsController -m Tool -dc Data.AppDbContext -outDir Controllers


```powershell
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
```

```powershell
❯ dotnet aspnet-codegenerator controller -api -name ToolsController -m Tool -dc Data.AppDbContext -outDir Controllers
Building project ...
Finding the generator 'controller'...
Running the generator 'controller'...
Generating a new DbContext class 'Data.AppDbContext'
Attempting to compile the application in memory with the added DbContext.
Attempting to figure out the EntityFramework metadata for the model and DbContext: 'Tool'
Added DbContext : '\Data\AppDbContext.cs'
Added Controller : '\Controllers\ToolsController.cs'.
RunTime 00:00:07.58
```

There's a lot to unpack here. First, it notes that this project does not have
a `DbContext` class yet, so it uses the provided name to create it. Next, it
inspects the `Tool` class to
