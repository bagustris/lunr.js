---
layout: default
title: JavaScript Basics
order: 2
---

# 1. JavaScript Basics (for complete beginners)

[← Back to docs home](index.html)

You don't need to be a JavaScript expert to use lunr.js — but the examples in this guide will make
a lot more sense if you understand a handful of building blocks first. This page covers *only*
what you need for the rest of the guide, not JavaScript in general.

If you've written JavaScript before, skip ahead to [2. Getting Started](02-getting-started.html).

## Two places JavaScript can run

- **In a browser**, inside a `<script>` tag on an HTML page.
- **In Node.js**, a program that runs JavaScript outside the browser, from your terminal.

lunr.js works in both. Later pages show both options.

## Variables

A variable is a named box that holds a value. Modern JavaScript mostly uses `const` (a box whose
contents won't be reassigned) and `let` (a box that can be reassigned):

```javascript
const name = "lunr"   // a piece of text ("string")
let count = 3          // a number
```

## Objects and arrays

An **object** groups related values together under names (called *keys*). It's written with curly
braces `{ }`:

```javascript
const book = {
  title: "Moby Dick",
  author: "Herman Melville"
}

console.log(book.title)   // "Moby Dick"
```

An **array** is an ordered list of values, written with square brackets `[ ]`:

```javascript
const books = [
  { title: "Moby Dick", author: "Herman Melville" },
  { title: "Dracula", author: "Bram Stoker" }
]
```

This shape — an array of objects — is exactly how you'll hand your documents to lunr.js: one
object per document, with fields like `title` or `body` as keys.

## Functions

A function is a named, reusable block of code:

```javascript
function greet(name) {
  return "Hello, " + name
}

greet("world")   // "Hello, world"
```

You'll also see functions passed *as values* into other functions — this is called a **callback**.
It looks unusual at first, but it just means "here's some code to run later, or in a specific
context":

```javascript
books.forEach(function (book) {
  console.log(book.title)
})
```

lunr.js uses this pattern heavily: you hand it a function, and it runs that function to let you
configure things.

## What `this` means inside a callback

lunr.js's setup step (shown on the next page) looks like this:

```javascript
const idx = lunr(function () {
  this.ref('id')
  this.field('title')
})
```

Here, `this` inside the function refers to the search index being built. lunr.js runs your
function with `this` pointing at its own internal builder object, so that calling `this.ref(...)`
and `this.field(...)` configures *that* index. You don't need to understand how this works
internally — just know that inside this particular callback, `this` means "the index I'm
building."

## Loading the library: `require` and `<script>`

- In **Node.js**, you load a library with `require`:
  ```javascript
  const lunr = require('lunr')
  ```
- In a **browser**, you load it with a `<script>` tag, and it becomes available as a global
  variable called `lunr`:
  ```html
  <script src="https://unpkg.com/lunr/lunr.js"></script>
  ```

That's genuinely all the JavaScript you need. Next: [2. Getting Started](02-getting-started.html).
