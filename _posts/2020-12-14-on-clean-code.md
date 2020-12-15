---
title: "On \"Clean Code\""
date: 2020-12-14T19:02:31-08:00
categories:
  - Books
tags:
  - Technical Books
  - Coding
---

## Introduction
[<i>Clean Code</i> by Robert "Uncle Bob" Martin](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) is one of those books I've heard about, but never got a chance to sit down and read. I would always see it in people's "Top Programming Books to Read" lists, along with other intimidating books like "Design Patterns: Elements of Reusable Object-Oriented Software" or "The C Programming Language". I remember trying to read those books in college, and it was... rough, so the thought of going into another book that makes it on the same list was intimidating.

After a couple years in the industry, I've seen some brilliantly elegant code and some... not so much. I've struggled with naming variables and functions, and have also fell into traps of assuming functions do what they're called, and it has been a lesson learned the hard way.

A couple of months ago, I went on a technical book buying spree, and have yet to finish 1... might as well be another side project. This time, I had a friend recently move into the software engineer role and brought up in conversation that someone mentioned this book to him. Knowing I had the book, I thought it would be fun to read and discuss together. This book was such a great read, I thought I'd write up my thoughts.

## What is 'clean code'?
The book starts off with chapter one going through scenarios that are all too familiar: 
> "We've all felt the relief of seeing our messy program work and deciding that a working mess is better than nothing. We've all said we'd go back and clean it up later. Of course, in those days, we didn't know LeBlanc's Law: <b><u>Later equals never</u></b>."

We all know messy code when we see it. Variables poorly named, functions a mile long, classes all over the place... it's easy to spot. But what's next? How do we go from a mess to something that's readable and concise? 

Clean code is when you know exactly what functions do and what variables are. Clean code is when classes are organized such that you might not even need to look up documentation, you can just find it naturally. Clean code is Separation of Concerns, it's high cohesion low coupling, it's no fluff, it's all substance, but above all, it's clarity.

## "Clarity is Key"
Sometimes, in software, when we let these issues get out of hand, the cost to repair can almost be overwhelming. Martin goes into a scenario where a scrappy team has their MVP or PoC running well enough to gain traction. Once the feature requests and requirements start rolling in, it can be hard to maintain the code and keep it orderly, ESPECIALLY if we didn't start with clean code (which we rarely do). The mess keeps piling up, slowing the team down and reducing productivity until it asymptotically approaches zero. Under the pressure to stay productive, management green lights a rewrite. The tiger team is formed, and they now have to replicate all of the functionality of the older application, while it's still building out new features. Now the teams are racing forward until the newer team can catch up to the older team's work.

How can we avoid this? How can we start with clean code, and maintain order throughout the entire process?

If there's really one take-away from this book, it's that "clarity is key". There are chapters on meaningful names, functions, and comments that can be summarized from this one excerpt:

> "The name of a variable, function, or class should answer all the big questions. It should tell you why it exists, what it does, and how it is used. If a name requires a comment, then the name does not reveal its intent."

Let's go over an example from the book. When you're reading this, think about what it's doing, but also how many times you had to read it to understand what's happening:

```csharp
public List<int[]> getItem() 
{
  List<int[]> list1 = new List<int[]>();
  for (var x in theList)
  {
    if (x[0] == 4)
      list1.Add(x);
  }
  return list1;
}
```

Spacing is fine, there's no complex logic, indentation is easy to read.. so why is it so hard to tell what the code is doing here? 

"Okay, so the function is returning a list of int arrays...? What's "theList"? X in theList? What is that? First element in X? Equivalent to 4?? What is 4!?"

Martin says "the problem isn't the simplicity of the code but the <i>implicity</i> of the code." It implicitly requires we know:
1. What kinds of things are in ``theList``?
2. What is the significance of the zeroth subscript of an item in ``theList``?
3. What is the significance of the value 4?
4. How would I use the list being returned?

Let's rewrite this to be more clear. Let's say we're working on a mine sweeper game, and we find that ``theList`` is actually a gameboard. Each cell on the board is represented by an array, and the first element in the array is the status value of the cell, with 4 being 'flagged'.

```csharp
public List<int[]> getFlaggedCells()
{
  List<int[]> flaggedCells = new List<int[]>();
  for (var cell in gameboard)
  {
    if (cell[STATUS_VALUE] == FLAGGED)
      flaggedCells.Add(cell);
  }
  return flaggedCels;
}
```

Better, but let's go one step further and make a simple class ``Cell`` out of the int arrays:

```csharp
public List<Cell> getFlaggedCells() {
  List<Cell> flaggedCells = new List<Cell>();
  for(var cell in gameboard)
  {
    if(cell.isFlagged())
      flaggedCells.Add(cell);
  }
  return flaggedCells;
}
```

This takes SIGNIFICANTLY less brainpower, and you can understand it in 1 read through. Easy. Amazing what clarity can do to the code.

## Functions
Martin says that functions should be small.. really small.. "hardly ever longer than 20 lines", and that they should do "one thing, they should do it well, and they should do it only." Single Responsibility Principle, right? It's a common theme throughout this whole book: <i>don't do too much, do just enough</i>.

### Method Bodies
I was recently making a change in one of our code bases for a label's value based on some conditions. Trying to walk through the controller initialization, I found the logic buried in the ``.then(...)`` of a Promise. Without knowing exactly where to look, it took me significantly longer to understand the flow than I'd like to admit. Trying to use the Boy Scouts rule that was used in the book and "leave the campground cleaner than [I] found it"... I decided to break that out into another function that ONLY set the label. So instead of 

```ts
this.method().then( (result) =>
{
  // like 19 lines of logic
  //
  //
  //
  // ...
  //
  //
  //
})
```
it became:
```ts
this.method().then(this.setLabel);
//...
private setLabel(result: MethodResult) {
  // 19 lines of logic here.
}
```

Simple. One responsibility, easy to read. It felt so much more organized, and the constructor was much easier to read. It was definitely one of those "hey I applied the thing I learned" moments.

But was my change really 'cleaner'? In the intro to the book, [Ward Cunningham](https://en.wikipedia.org/wiki/Ward_Cunningham) has the quote:
> You know you are working on clean code when each routine turns out to be pretty much what you expected.

The refactor of creating a function called ``setLabel`` where it.. sets the label, <i>and only sets the label</i>, felt like I was making what Ward would call clean code.

### Arguments
This is another section where Martin's passion (or previous frustration) can be felt. He says that 1 is great, 2 is okay, and 3 "should be avoided where possible. More than 3 requires very special justification - <u><i>and then shouldn't be used anyway</i></u>".

His example for this is the difference between these two:

```csharp
Circle makeCircle(double x, double y, double radius);
Circle makeCircle(Point center, double radius);
```

> Reducing the number of arguments by creating objects out of them may seem like cheating, but it's not. When groups of variables are passed together, the way x and y are in the example above, they are likely part of a concept <b>that deserves a name of its own</b>.

The ``x`` and ``y`` represent a point of a circle, so we can make an object ``Point``, but even better, this gives us an opportunity to relabel the argument. We can call it ``center``, now clarifying exactly the purpose of that point.

The other 2 great pieces of advice from Martin that I think are worth bringing up:
1. Don't use booleans as inputs. 
>"It immediately complicates the signature of the method, loudly proclaiming that this function does more than one thing. It does one thing when the flag is true and another if the flag is false!"

2. Command Query separation
>"Either your function should change the state of an object, or it should return some information about that object."

## Comments
Comments, according to Martin, are
> "[...] at best, a necessary evil. If our programming languages were expressive enough, or we had the talent to subtly wield those languages to express our intent, we would not need comments very much - perhaps at all."

My reaction when I read this was basically...
![Can't reall argue with that](/assets/images/nothing-to-say.jpg)

I've been sort of wrestling with this for a while. One of the Principle engineers on my team, when I started, casually said "code is the only source of truth. Comments lie, code doesn't" and it's really stuck with me for a while. If I have to write comments to explain my code, I might need to rewrite my code.

In this section, Martin places comments into one of two buckets, Good Comments and Bad Comments.

### Good Comments
- <b><u>Legal comments</u></b>: sometimes, corporate coding standards force us to write certain comments for legal reasons, like copyright.
- <b><u>Informative comments</u></b>: a good example of this is regex. I personally have trouble reading regex and knowing exactly what it's looking for, so a good comment would be explaining the pattern:

```
// format matched kk::mm::ss EEE, MMM dd, YYYY
```
- <b><u>Explination of intent</u></b>: a comment providing intent can be helpful. Code can't always provide business justification, but a comment might be helpful.
- <b><u>Clarification</u></b>: if you're using a library, parts of the library might be confusing, which gives us a good opportunity for clarification comments.
- <b><u>Warning of consequences</u></b>: we've all seen these memes:

![Total hours wasted: 254](/assets/images/waste-time-meme.jpg)

Don't be a hero, heed the warning.
- <b><u>TODO comments</u></b>: I like to keep my PRs relatively small, and if I know there's a work item that will follow, I'll write a comment and tag our Azure Dev Ops work item number so other members can look it up.
- <b><u>Amplification</u></b>: if a section is critical, it's a good idea to amplify.

### Bad Comments
- <b><u>Mumbling</u></b>: if you decide to write a comment, spend the time necessary to make sure it is the best comment you can write.
- <b><u>Redundant</u></b>: if the names are self explanitory, then you shouldn't need comments like this:

```csharp
// The lifecycle event support for this component.
protected LifecycleSupport lifecycle = new LifecucleSupport(this);

// The logger implementation
protected Log logger = null;

// Associated logger name.
protected string logName = null;
```

... yeah, based off the name, we can see that. Are these necessary if the names are good enough?
- <b><u>Misleading</u></b>: let's look at this example from the book:
```csharp
// Utility method that returns when this.closed is true. Throws an exception
// if the timeout is reached.
public async void waitForClose(long timeoutMillis) 
{
  if(!this.closed)
  {
    wait(timeoutMillis);
    if(!this.closed)
    {
      throw new Exception("could not be closed");
    }
  }
}
```

The method doesn't return WHEN ``this.closed`` becomes true, it returns IF ``this.closed`` is true, otherwise it waits for a blind timeout and then throws an exception if ``this.closed`` is still not true.

Subtle difference, but misleading.
- <b><u>Redundant</u></b>: wait, didn't we.. oh I get it.
- <b><u>Mandated comments</u></b>: the teams I've worked for have always had mandated comments. To me they make it easier to read, but my eyes always just skip over all the comments. Most of them are like "the logger" or "the id", so I don't really feel like there's much to miss.
- <b><u>Position markers</u></b>: stuff like regions and other marker comments, are they really needed with modern IDEs? Doubtful.
- <b><u>Closing braces</u></b>: same thing, do we really need this with modern IDEs? Or, your function is too long. Either way, definitely not necessary.
- <b><u>Attributions and bylines</u></b>: source control makes this irrelevant.
- <b><u>Commented out code</u></b>: people who see commented out code won't know what it's for, or have the courage to delete it. They'll think there's a reason for the commented out code, and it'll live forever.

## Classes
With classes, there were few lessons to learn, most mainly focused around the Single Responsibility Principle (big surprise).
> "With functions we count lines. With classes, we count responsibilities."

SRP is pervasive throughout the book. The best analogy I've seen for this came from Martin himself:
>"Do you want your tools organized into toolboxes with many small drawers each containing well-defined and well-labeled components? Or do you want a few drawers that you just toss everything into?"

Martin also talks about cohesion and coupling (which, [here's a good StackOverflow](https://stackoverflow.com/questions/14000762/what-does-low-in-coupling-and-high-in-cohesion-mean) about what it means to have high cohesion and low coupling). Cohesion is described as "the more variables a method manipulates, the more cohesive that method is to its class".  So according to Martin, "a class in which each variable is used by each method is maximally cohesive." When I was reading this section, I thought that was a super interesting point: to be maximally cohesive, each function uses each variable. 

Finally, on coupling, Martin says:
>"Lack of coupling means that the elements of our system are better isolated from each other and from change."

Low coupling and keeping elements isolated from change will help reduce the chance of regressions.

## Unit Tests
Code needs to be clean, but Martin makes great points about why unit tests should also be clean.
>"The problem is that tests must change as the production code evolves. The dirtier the tests, the harder they are to change. The more tangled the test code, the more likely it is that you will spend more time cramming new tests into the suite than it takes to write the new production code."

Refactoring code is one struggle, but when you have to also refactor the unit tests surrounding the code, the difficulty of a change might be a multiplier if both aren't kept clean. In this section, he has one of my favorite quotes in the book:
>"What makes a clean test? 3 things: readability, readability, and readability."

Just like everything else we've talked about so far, if it's hard to read, it's hard to understand. If it's hard to understand, it's going to take much longer to complete the task than if someone had taken the time to write clear, concise tests that anyone can immediately understand. Velocity depends on understanding what's written. Martin mentions that clean tests follow FIRST:
- <u>Fast</u> - when a test runs slow, you won't want to run tests frequently.
- <u>Independent</u> - tests should not depend on each other. If one fails, then it kicks off a cascading chain of failures and the root cause is hard to diagnose.
- <u>Repeatable</u> - tests should be able to run in any environment: prod, QA, local, even without a network. If your tests aren't repeatable, you'll have an excuse for why they fail (see: flaky tests).
- <u>Self-validating</u> - tests should have a boolean output; either they pass or they fail. If the tests aren't self-validating, then failure can become subjective and running the tests can require a long manual evaluation.
- <u>Timely</u> - written in a timely fashion. If you write tests after the production code, you may find the production code hard to test.

Closing off this chapter, Martin talks about how test rot == code rot:
> "If you let tests rot, your code will rot too. Keep your tests clean."

## Smells
This final section is dedicated to "code smells". This is another industry term I've recently learned, and I'm definitely going to be using it from now on.

Code smell, for those who don't know, was popularized by Martin Fowler in <i>Refactoring: Improving the Design of Existing Code</i>, saying "it is a surface indication that usually corresponds to a deeper problem in the software system".

Sure, the code looks fine... but how does it <i>smell?????</i>

Let's look over some smells:

Comments
- <b><u>Inappropriate information</u></b>: if it isn't relevant, leave it out. Put it in a wiki or in a work item or somewhere where it IS relevant.
- <b><u>Obsolete comment</u></b>: anything that's old, irrelevant, or incorrect is obsolete. 
- <b><u>Redundant comment</u></b>: if something adequately describes itself, you don't need a comment.
- <b><u>Poorly written comment</u></b>: if it's worth writing, it's worth writing correctly.
- <b><u>Redundant comment</u></b>: if something adequately descr.. wait, didn't we already do this?

Functions
- <b><u>Too many arguments</u></b>: if there's too many arguments, the function is probably doing too many things.
- <b><u>Flag arguments</u></b>: same thing, if the the function takes a boolean flag, it more than likely doesn't follow SRP.
- <b><u>Dead functions</u></b>: methods that aren't called should be cleaned out.

General
- <b><u>Obvious behavior is unimplemented</u></b>: following the "Principle of Least Surprise", any function or class should implement the behaviors that another programmer could reasonably expect.
- <b><u>Duplication</u></b>: Dave Thomas and Andy Hunt called it the DRY principle (Don't Repeat Yourself). Ron Jeffries ranks this rule second, just below getting all tests to pass. Once, and only once.
- <b><u>Duplication</u></b>: ... T__T 
- <b><u>Code at wrong level of abstraction</u></b>: we want to make sure <i>all</i> lower level concepts to be in derivatives and <i>all</i> higher level concepts to be in base classes.

## Conclusion
This, to me is definitely a book programmers will want to read. Does it have a lot of in depth, thought provoking chapters that will make you put the book down and think for an hour? Not really. Does it have concepts that everyone should follow? Most likely. For most of us, it's not just my code or your code, it's <i>our</i> code, and we're all responsible for how it looks, how it runs, and how easy we make it to maintain for people in the future. Next time someone makes a pull request, or you're about to create one, double check on code cleanliness to leave the campgrounds cleaner than you found it.

## References
- Clean Code by Robert C. Martin - https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882