---
layout: default
title: 4. How to Customize & Pre-build
---

# 4. How to Customize & Pre-build

[← Back to docs home](index.html) · [← 3. How to Search](03-how-to-search.html)

This page covers three common needs that go beyond the basics: making a large index load fast,
tweaking how text gets normalized, and handling languages other than English.

## Pre-building an index for speed

Building an index (the `lunr(function () {...})` step) takes real work — for a handful of
documents it's instant, but for thousands of documents it can noticeably block the browser while
it runs. If your documents don't change per-visitor (a blog, a docs site, a static site), you
should build the index **once**, save it, and have visitors load the already-built index instead
of rebuilding it every time.

### Step 1: Build and save the index (once, ahead of time)

Run this as a one-off Node.js script, as part of your site's build process:

```javascript
// build-index.js
var lunr = require('lunr')
var fs = require('fs')

var documents = JSON.parse(fs.readFileSync('documents.json', 'utf8'))

var idx = lunr(function () {
  this.ref('id')
  this.field('title')
  this.field('body')

  for (var i = 0; i < documents.length; i++) {
    this.add(documents[i])
  }
})

fs.writeFileSync('search-index.json', JSON.stringify(idx))
```

Run it once with `node build-index.js`. This produces `search-index.json` — a plain JSON file
containing the finished index, which you deploy alongside your site's other static assets.

### Step 2: Load the pre-built index (in the browser, per visitor)

```javascript
fetch('/search-index.json')
  .then(function (response) { return response.json() })
  .then(function (serializedIndex) {
    var idx = lunr.Index.load(serializedIndex)
    console.log(idx.search('ishmael'))
  })
```

`lunr.Index.load(...)` reconstructs a ready-to-search index from the saved JSON — this is
dramatically faster than rebuilding from the original documents, because none of the text
processing needs to happen again.

**Rule of thumb:** if your data changes, re-run the build script and redeploy the JSON file. A
loaded index is just as immutable as a freshly built one — you can search it, but not add to it.

## Customizing text processing (pipeline functions)

The steps that normalize text before indexing (splitting into words, removing stop words,
stemming — see [3. How to Search](03-how-to-search.html#why-word-order-and-exact-spelling-sometimes-dont-matter))
are called the **pipeline**, and you can insert your own step into it.

A common example: treating spelling variants as the same word, e.g. "gray" and "grey":

```javascript
function normalizeSpelling(token) {
  return token.update(function (str) {
    return str === "grey" ? "gray" : str
  })
}

lunr.Pipeline.registerFunction(normalizeSpelling, 'normalizeSpelling')

var idx = lunr(function () {
  this.pipeline.before(lunr.stemmer, normalizeSpelling)
  this.searchPipeline.before(lunr.stemmer, normalizeSpelling)

  this.ref('id')
  this.field('body')
  // this.add(...) calls as usual
})
```

Two things to note:

- `lunr.Pipeline.registerFunction(...)` gives your function a name so it can be saved and restored
  correctly if you serialize the index (step above).
- Add the function to **both** `this.pipeline` (used when indexing documents) and
  `this.searchPipeline` (used when processing a query), or a query for "grey" won't match a
  document indexed as "gray".

This is an advanced feature — most projects never need it. Reach for it only if the default
tokenizing/stemming behavior is actively working against your data.

## Searching languages other than English

lunr.js's built-in stemmer is English-only. For other languages, use the companion
[lunr-languages](https://github.com/MihaiValentin/lunr-languages) package, which currently
supports German, Danish, Spanish, Finnish, French, Hungarian, Italian, Japanese, Dutch, Norwegian,
Portuguese, Romanian, Russian, Thai, and more.

```
npm install lunr-languages
```

```javascript
var lunr = require('lunr')
require('lunr-languages/lunr.stemmer.support')(lunr)
require('lunr-languages/lunr.fr')(lunr)

var idx = lunr(function () {
  this.use(lunr.fr)   // switch this index to French processing
  this.ref('id')
  this.field('text')

  this.add({ id: 1, text: "Ceci n'est pas une pipe" })
})

idx.search('pipe')
```

If a single index needs to mix multiple languages, `lunr-languages` also provides a
`multiLanguage` helper — see its README for details.

## Advanced tuning (pointer, not covered here)

lunr.js also exposes low-level relevance-scoring parameters (`k1` and `b`, from the BM25 ranking
algorithm) for teams that need to fine-tune scoring behavior for a specific dataset. This is rarely
necessary and is out of scope for this guide — see the official
[Customisation guide](https://lunrjs.com/guides/customising.html) if you need it.

---

Next: [5. Reference](05-reference.html) — a condensed cheat sheet of everything covered so far.
