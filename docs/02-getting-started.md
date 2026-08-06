---
layout: default
title: Getting Started
order: 3
---

# 2. Getting Started

[← Back to docs home](index.html) · [← 1. JavaScript Basics](01-javascript-basics.html)

By the end of this page you'll have a working search index and be able to search it. There are two
ways to get lunr.js into a project — pick whichever matches what you're building.

## Step 1: Install lunr.js

**Option A — Node.js project (npm):**

```
npm install lunr
```

**Option B — Plain HTML page (no build step, no install):**

```html
<script src="https://unpkg.com/lunr/lunr.js"></script>
```

Both give you the same thing: a function called `lunr` that you use to build a search index.

## Step 2: Describe your documents

lunr.js searches a list of plain JavaScript objects. Each one needs a unique identifier (the
`ref`) and one or more text fields you want to be searchable:

```javascript
var documents = [
  {
    id: 1,
    title: "Moby Dick",
    body: "Call me Ishmael. Some years ago..."
  },
  {
    id: 2,
    title: "Dracula",
    body: "3 May. Bistritz. Left Munich at 8:35 P.M."
  },
  {
    id: 3,
    title: "Frankenstein",
    body: "You will rejoice to hear that no disaster has accompanied the commencement of an enterprise which you have regarded with such evil forebodings."
  }
]
```

## Step 3: Build the index

```javascript
var idx = lunr(function () {
  this.ref('id')
  this.field('title')
  this.field('body')

  for (var i = 0; i < documents.length; i++) {
    this.add(documents[i])
  }
})
```

What each line does:

- `this.ref('id')` — tells lunr.js which field uniquely identifies a document, so results can point
  back to it.
- `this.field('title')` / `this.field('body')` — tells lunr.js which fields should be indexed and
  searchable.
- `this.add(documents[i])` — adds one document to the index.

**Important:** all documents must be added *inside* this function. Once `lunr(...)` returns, the
index is finished and immutable — there's no `idx.add(...)` afterwards. If your data changes,
you rebuild the index from scratch (see [4. How to Customize & Pre-build](04-how-to-customize.html)
for how to make rebuilding fast).

## Step 4: Search

```javascript
var results = idx.search("ishmael")
console.log(results)
```

This returns an array, ranked with the best match first:

```javascript
[
  {
    ref: "1",
    score: 0.63,
    matchData: { metadata: { ishmael: { body: {} } } }
  }
]
```

- `ref` — the id you passed to `this.ref(...)`, as a string.
- `score` — how well this document matches; higher is more relevant. Scores are only meaningful
  *relative to each other* within one search, not as an absolute number.
- `matchData` — which term(s) matched and in which field(s); useful for highlighting matches, but
  safe to ignore when you're starting out.

If `documents` doesn't contain the id you're looking for, look it up yourself, e.g.
`documents.find(function (d) { return String(d.id) === results[0].ref })`.

## Full example: Node.js

```javascript
var lunr = require('lunr')

var documents = [
  { id: 1, title: "Moby Dick", body: "Call me Ishmael." },
  { id: 2, title: "Dracula", body: "3 May. Bistritz." }
]

var idx = lunr(function () {
  this.ref('id')
  this.field('title')
  this.field('body')

  for (var i = 0; i < documents.length; i++) {
    this.add(documents[i])
  }
})

console.log(idx.search("ishmael"))
```

Save this as `search.js` and run it with `node search.js`.

## Full example: browser page

```html
<!DOCTYPE html>
<html>
<head>
  <script src="https://unpkg.com/lunr/lunr.js"></script>
</head>
<body>
  <script>
    var documents = [
      { id: 1, title: "Moby Dick", body: "Call me Ishmael." },
      { id: 2, title: "Dracula", body: "3 May. Bistritz." }
    ]

    var idx = lunr(function () {
      this.ref('id')
      this.field('title')
      this.field('body')

      for (var i = 0; i < documents.length; i++) {
        this.add(documents[i])
      }
    })

    console.log(idx.search("ishmael"))
  </script>
</body>
</html>
```

Save this as `index.html` and open it in a browser, then check the developer console (F12) to see
the results printed there.

## A note on `forEach`

If you'd rather use `documents.forEach(...)` instead of a `for` loop, use an arrow function so
`this` still refers to the index being built:

```javascript
documents.forEach(function (doc) {
  this.add(doc)   // BREAKS — this is no longer the index here
})

documents.forEach((doc) => this.add(doc))   // WORKS — arrow functions keep the outer `this`
```

This is a common trap: a plain `function () {}` gets its *own* `this`, but an arrow function
`() => {}` does not — it reuses `this` from the surrounding code. See
[1. JavaScript Basics](01-javascript-basics.html#what-this-means-inside-a-callback) for more on
`this`.

---

Next: [3. How to Search](03-how-to-search.html) — the full query syntax (wildcards, boosts, fuzzy
matching, and more).
