---
layout: default
title: lunr.js Documentation
---

# lunr.js Documentation

**lunr.js** is a small JavaScript library that adds full-text search to a web page or a Node.js
application, entirely on the client side — no database or search server required. You give it a
list of documents, it builds a searchable index out of them, and it lets you query that index and
get back a ranked list of matches.

This is an unofficial, simplified companion to the guides at
[lunrjs.com](https://lunrjs.com/guides/getting_started.html), rewritten to be approachable even if
you've never written JavaScript before.

## Where to start

Pick the page that matches your situation:

| If you...                                                        | Read this |
|-------------------------------------------------------------------|-----------|
| have never written JavaScript before                              | [1. JavaScript Basics](01-javascript-basics.html) |
| just want search working as quickly as possible                   | [2. Getting Started](02-getting-started.html) |
| already have it working and want to know the search syntax        | [3. How to Search](03-how-to-search.html) |
| have a lot of documents, or a static site, and care about speed   | [4. How to Customize & Pre-build](04-how-to-customize.html) |
| want a quick lookup of methods and options                        | [5. Reference](05-reference.html) |

If you're already comfortable with JavaScript, you can skip straight to page 2.

## What lunr.js is *not*

- It is not a server or a database — everything happens in memory, in the browser or in Node.js.
- It is not meant for millions of documents — it's built for the kind of dataset that fits
  comfortably in memory (a blog, a docs site, a product catalog, that sort of scale).
- It does not automatically stay in sync with changing data — once built, an index is fixed. You
  rebuild it when your documents change (page 4 covers this).

## Other resources

- Official guides and generated API reference: [lunrjs.com](https://lunrjs.com/)
- Source code: [github.com/olivernn/lunr.js](https://github.com/olivernn/lunr.js)
- Non-English language support: [lunr-languages](https://github.com/MihaiValentin/lunr-languages)
