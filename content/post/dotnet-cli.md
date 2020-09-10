+++
author = "Daryl Walleck"
title = "Bootstrapping a .NET project from the command line"
date = "2019-03-10"
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
myself to keep using it rather than switching back to Visual Studio.
However, all of my previous experience with creating new .NET projects involved
the "File -> New Project" menu in Visual Studio, so I decided to compromise. I
used Visual Studio to create solutions, projects, and templated classes like web
controllers and used VS Code for coding. However, this felt _really wrong_.

The better answer was the `dotnet` command-line tool, which was added with .NET
Core. When I started several new personal projects earlier this year, I decided
to take the time to better understand the `dotnet` tool and see if I could
overcome the roadblocks that stood between me and command-line bliss.

However, while `dotnet new` does get the
necessary basic code in place to get started, there's still some things missing
that Visual Studio took care of for you.


With a bit of time,
useful documentation, and a helpful workshop, I managed to find my way back to
command line bliss.

## Solution Management

When using `dotnet new` to create a new project, one of the first things I
noticed missing from the project was a solution file. Given that `new` usually
creates a single project, a solution file isn't necessary. However, if you end
up creating several projects or are working with someone using Visual Studio,
having a solution file becomes more useful. It turns out that this is an easy
problem to solve as `dotnet new` has a command to create solution files.

```bash
dotnet new console -n SolutionDemo
cd SolutionDemo
dotnet new sln
```

Perfect! However, this creates an empty solution file, so there is a bit more
work to be done. While you could add an entry for your project by hand to the
solution file, there is also a `dotnet sln` command to allow you to add, list,
and delete items from the solution.

```bash
dotnet sln add .\App1.csproj
```

## Source Control

While adding source control is a quick `git init` away, there's one thing thats
still missing: a .gitignore file. While you could track down the official GitHub
repo that has .gitignore files for most language types or just copy one from an
old project, the .NET team has thankfully added a `dotnet new gitignore` command
generate the file for you. It's one of those small things that save you a bit
of time and gives confidence that you have the "right" .gitignore file.

## Global Tools

Where the scope `dotnet` command ends, the ecosystem of .NET Core tools step in
to fill the gaps. If this is the first time you've heard of .NET Core tools,
they are extensions to the .NET CLI that add new command line programs that
solve problems related to developing .NET Core applications (an unofficial
list of tools can be found [here](https://github.com/natemcmaster/dotnet-tools)).

There are two tools that I found to be especially useful to replicate
functionality that I was used to in Visual Studio. If you've worked with Entity
Framework before, you're likely used to switching to the package manager console
to run add or run migrations. For the command line world, there is thankfully a
`dotnet ef` tool that provides the same functionality.

```bash
Add-Migration FirstMigration
Update-Database
```

```bash
dotnet ef migrations add FirstMigration
dotnet ef database update
```

### Scaffolding

I'll be honest: I can't write a .NET Core Web API Controller from scratch.
My muscle memory wants me to right click the Controllers directory so I can
click the handy `Add -> Controller` link. I had considered writing a VS Code
snippet to create a skeleton of the class, but I happened upon a
[workshop](https://github.com/csharpfritz/aspnetcore-app-workshop) that
introduced me to the `dotnet-aspnet-codegenerator` global tool which
provides the same capabilities as the
`API Controller with actions, using Entity Framework` option in the new
controller wizard.

{{< figure src="/images/AddControllerEntityFramework.png"
    alt="Picture of the Add Controller dialog from Visual Studio" >}}

This tool has a dependency on another NuGet package to run properly, so the
first command should be run from the project you are working on.

```bash
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
```
