---
title: "Building a Roslyn Analyzer"
#excerpt_separator: "<!--more-->"
date: 2021-02-09T00:57:59Z
categories:
  - Blog
  - C#
tags:
  - How To
---

## Intro

Over the weekend, I built my first analyzer for the [.NET Roslyn compiler](https://en.wikipedia.org/wiki/Roslyn_(compiler)). I've never done this before, but in my quest to be a better open source contributor, I found [an issue](https://github.com/dotnet/orleans/issues/6194) in Microsoft's [Orleans](https://dotnet.github.io/orleans/docs/index.html) project asking for someone to write an analyzer. In short, from their own documentation, Orleans "is a cross-platform framework for building robust, scalable distributed applications". 

In Orleans, a `Grain` is an entity in the distributed system. Grains have an [abstract base class](https://github.com/dotnet/orleans/blob/master/src/Orleans.Core.Abstractions/Core/Grain.cs), as well as an interface, [IGrain](https://github.com/dotnet/orleans/blob/master/src/Orleans.Core.Abstractions/Core/IGrain.cs). When a class implements a form of `IGrain`, but doesn't inherit from `Grain`, the project compiles but has a runtime error.  Here's the ask:

> I had a project with IMyGrain which inherited from IGrainWithIntegerKey, but I forget to derive the implementation class from Grain.
<br><br>This all compiles with no warning (as IGrainWithIntegerKey has no methods to implement), but at runtime fails with "cannot find an implementation class" when I call GetGrain().
<br><br>I feel this could be more helpfully spotted.

After a small discussion in the issue, someone recommended an analyzer. Great! but.. what's an analyzer? And why do we want one?

## Hang on.. what's an analyzer, and how does it work?

[Source code analysis](https://docs.microsoft.com/en-us/visualstudio/code-quality/roslyn-analyzers-overview?view=vs-2019) is static check that can help inspect your C# or Visual Basic code for style, quality, maintainability, design, and other issues ([StyleCop](https://github.com/StyleCop/StyleCop) is a really popular one). A great example of this would be if you have an unused method, a Roslyn analyzer can tell you the method isn't used so that you can either add a reference, or remove it:

![Add isn't used, so we can just get rid of it!](/assets/images/roslyn-analyzer/basic-example.png)

or it can show you that some code isn't necessary, and help you clean up your source:

![A can't be null, since it's a primitive type int that defaults to 0](/assets/images/roslyn-analyzer/unnecessary-code-example.png)

and not only can it show you the issue, but it can also __show you fixes__ to the issue, and implement them for you!

![Keanu whoa moment.](/assets/images/roslyn-analyzer/basic-recommended-fix.png)

Awesome. I can't imagine working without these, they're the best. Praise modern IDEs. 

So that's what an analyzer is, but how does it work? For that, we'll have to go back to compilers.

### Compilers and Syntax Trees

Compiling code is, at a high level, making sure the code is runnable and that the computer can understand what you're asking it to do. If you type something random that the computer doesn't understand, when it builds your program, the computer will be like:

![](/assets/images/roslyn-analyzer/no.gif)

You have to make sure the computer understands what you want. 