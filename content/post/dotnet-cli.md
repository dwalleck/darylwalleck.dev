+++
author = "Daryl Walleck"
title = "Bootstrapping a .NET project from the command line"
date = "2019-03-10"
description = "A brief description of Hugo Shortcodes"
tags = [
    "shortcodes",
    "privacy",
]
thumbnail = "images/dollar.png"

+++

If you're like me, most of your experience with creating new .NET projects
involves the "File -> New Project" menu in Visual Studio. After coming back to
.NET after a few years in the Python world, I was used to doing pretty much
everything from the command line. Coming back, I was delighted to discover
the new `dotnet` command and other global tools that made working from the
command line a first-class citizen. However, while `dotnet new` does get the
necessary basic code in place to get started, there's still some things missing
that Visual Studio took care of for you. Here's a few things I learned to fill
that gap.

## Solution Management

When creating a project with `dotnet new`, one of the things I noticed off the
bat missing was a solution file. The assumption likely was that
anyone creating a project from the command line would likely be working in
Visual Studio Code and wouldn't need one. Being the fickle person that I am,
I find myself switching between VS Code and Visual Studio depending on my mood.
It turns out that this is an easy problem to solve as `dotnet new` has an option
for solution files.

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

I'll be honest: I couldn't write a .NET Core Web API Controller from scratch.
My muscle memory wants me to right click the Controllers directory so I can
click the handy `Add -> Controller` link. I had considered writing a VS Code
snippet to create a skeleton of the class, but I happened upon a
[workshop](https://github.com/csharpfritz/aspnetcore-app-workshop) that
introduced me to a helpful tool called `dotnet-aspnet-codegenerator`, which
provides the same capabilities as the
`API Controller with actions, using Entity Framework` option in the new
controller wizard.

This tool has a dependency on another NuGet package to run properly, so the
first command should be run from the project you are working on.

```bash
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
```
