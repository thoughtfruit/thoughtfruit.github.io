---
layout: post
title: "Silent Bugs from Lexical Scope [JS]"
categories:
- blog
tags:
- programming
---

Before C there was B, and before B there was BCPL. It was a language originally inteded for writing compilers in other languages. It was also the first programming language that implemented brackets/braces, {}, for block scope within functions.

B was created at Bell Labs by Ken Thompson and Dennis Ritchie around 1969. It was based mainly on the syntax and ideas that were taken from CPL to BCPL, and then later migrated to C and C++. Like BCPL, B has only one datatype the computer word. Most operators treated this as a memory address via classical reference.

Here’s an example, taken from Wikipedia, of what B looked like:

```c
printn(n, b) { 
  extern putchar;
  auto a;

  if (a = n / b) 
    printn(a, b);
    putchar(n%b + ‘0’);
}
```

Looks very similar to C, in a lot of ways. And C of course was a language developed by Dennis Ritchie, between 1969 and 1973 at Bell Labs. C allowed for lexical variable scope and recursion, with a static type system. C is a procedural language, and was designed to be compiled using a relatively straightforward compiler which allows it access to mapping low-level machine code. And what’s most important to us, is that JavaScript borrowed some of these design characteristics while it was being created in 1995.

JavaScript for instance took Lexical Scoping from C, and its history from HyperCard. It was developed at Netscape by Brendan Eich, who was at the time a part of the battle for the internet between Microsoft and Netscape. Which was really a battle between Java and Visual Basic. JavaScript was originally developed under the name Mocha, but then shipped under the name LiveScript. The final choice of name caused confusion given that it was to be marketed as a spin-off of Java, hence the name JavaScript.

It has a large history, one which would be hard to fit in a blog post. But the interesting part for reference here is that within Bell Labs there were often large debates about the language. And one of these debates was over the placement of the function brackets / braces in JavaScript. There was a healthy number of people who wanted to put it on the left of the function decleration, and some who wanted it on the right (including the creators). The creators could have defined this within the langauge itself, but they left it up to us to make the decision - thus the debate has continued on for years.

Though there is one caveat, and let’s take a look at the code below to see what that is.

```javascript
function foo (bar) {
  return 
    // New & Empty line, silent error gets thrown here
    {
      bar: 'baz';
    }
}
```

Here you can see that with a simple typo a hidden or silent error can get thrown that will often cause Bugs later on in programs. As a programmer we should always prefer non-destructive systems over destructive ones, so wherever we can head off a bug such as this (caused by human error) we should do so.

Let’s take a look at the common way to stop this error from being thrown:

```javascript
function foo (bar) { 
  return { 
    ok: true;
  }
}
```

And it’s as simple as that. This is why you should always put your brackets or braces in JavaScript to the right, not the left.

The language designers could have built this in, but they choose to leave it for us to find out. Now that you have, I will leave the debate humbly to you.

_*all information here was cited via Wikipedia, and Doug Crockford's own opinions on the Braces in JavaScript. &nbsp;I couldn't find him saying this (regarding braces) publically online, but it is available [here](https://frontendmasters.com/courses/javascript-the-good-parts/) for a conference he taught.&nbsp;_