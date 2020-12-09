---
title: "On \"Clean Code\""
date: 2020-12-04T08:56:02-08:00
categories:
  - Books
tags:
  - Technical Books
  - Coding
---

## Introduction
[<i>Clean Code</i> by Robert "Uncle Bob" Martin](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) is one of those books I've heard about, but never got a chance to check it out. I would always see it in people's "Top Programming Books to Read" lists, along with other intimidating books like "Design Patterns: Elements of Reusable Object-Oriented Software" or "The C Programming Language". I remember trying to read those books in college, and it was... rough.

After a couple years in the industry, I've seen some brilliantly elegant code and some... not so elegant code. I've struggled with naming variables and functions, and have also fell into traps of assuming functions or variables do or are what they're called or named, and it has been a lesson learned the hard way.

A couple of months ago, I went on a technical book buying spree, and have yet to finish 1... might as well be another side project. This time, I had a friend recently move into the software engineer role and brought up in conversation that someone mentioned this book to him. Knowing I had the book, I thought it would be fun to read and discuss together. This book was such a great read, I thought I'd write up my thoughts.

## What is 'clean code'?
The book starts off with chapter one going through scenarios that are all too familiar: 
> "We've all felt the relief of seeing our messy program work and deciding that a working mess is better than nothing. We've all said we'd go back and clean it up later. Of course, in those days, we didn't know LeBlanc's law: <b><u>Later equals never</u></b>."

We all know messy code when we see it. Variables poorly named, functions a mile long, classes all over the place... it's easy to spot. But what's next? How do we go from a mess to something that's readable and concise? 

Martin goes into a scenario where a scrappy team has their MVP or PoC running well enough to gain traction. Once the feature requests and requirements start setting in, it can be hard to maintain the code and keep it orderly. The mess keeps piling up, slowing the team down and reducing productivity until it asymptotically approaches zero. Under the pressure to stay productive, management green lights a rewrite. The tiger team is formed, and they now have to replicate all of the functionality of the older application, while it's still building out new features. Now the teams are racing forward until the newer team can catch up to the older team's work.



## "Clarity is Key"
If there's really one take-away from this book, it's that "clarity is key". There are chapters on meaningful names, functions, comments, and formatting all dedicated to making sure intent is clear. I feel like these chapters can be summarized from this one excerpt:

> "The name of a variable, function, or class should answer all the big questions. It should tell you why it exists, what it does, and how it is used. If a name requires a comment, then the name does not reveal its intent."

