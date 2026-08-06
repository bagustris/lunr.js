---
layout: default
title: Reference
order: 6
---

# 5. Reference

[← Back to docs home](index.html) · [← 4. How to Customize & Pre-build](04-how-to-customize.html)

A condensed cheat sheet of everything covered in this guide. For the full generated API
documentation (every method, every option), see [lunrjs.com/docs](https://lunrjs.com/docs/index.html).

## Building an index

```javascript
var idx = lunr(function () {
  this.ref('id')          // required: the unique identifier field
  this.field('title')     // a searchable field
  this.field('body')      // another searchable field

  this.add(document)      // add one document — call once per document
})
```

| Method | Purpose |
|---|---|
| `this.ref(name)` | Sets which document field is the unique id. Call once. |
| `this.field(name)` | Registers a field as searchable. Call once per field. |
| `this.add(doc)` | Adds one document to the index. Must be called inside the builder function. |
| `this.use(plugin, ...args)` | Applies a plugin (e.g. a language, from `lunr-languages`) — see [4. Plugins](04-how-to-customize.html#plugins). |
| `this.pipeline` / `this.searchPipeline` | The text-processing pipelines for indexing / querying — see [4. How to Customize](04-how-to-customize.html#customizing-text-processing-pipeline-functions). |
| `this.metadataWhitelist` | Array of metadata keys to keep in search results — see [4. Storing extra metadata](04-how-to-customize.html#storing-extra-metadata). |
| `this.k1(n)` / `this.b(n)` | BM25 relevance-scoring tuning — see [4. Similarity tuning](04-how-to-customize.html#similarity-tuning-k1-and-b). |

The index returned by `lunr(...)` is **immutable** — no documents can be added, changed, or
removed after the builder function returns. Rebuild the index if your data changes.

## Searching

```javascript
idx.search(queryString)
```

Returns an array of results, best match first:

```javascript
[
  { ref: "1", score: 0.63, matchData: { metadata: { /* ... */ } } }
]
```

| Field | Meaning |
|---|---|
| `ref` | The document's id, as a string. |
| `score` | Relevance ranking; only meaningful relative to other results in the same search. |
| `matchData.metadata` | Which term(s) matched, in which field(s) — for highlighting matches. |

## Query syntax

| Syntax                 | Meaning                              |
|--------------------------|----------------------------------------|
| `foo bar`                | documents with "foo" OR "bar"          |
| `title:foo`               | "foo" only in the `title` field        |
| `foo*`, `*foo`, `f*o`     | wildcard match                         |
| `foo^10`                  | boost this term's weight               |
| `foo~1`                   | fuzzy match, up to N edits             |
| `+foo`                    | "foo" required                         |
| `-foo`                    | "foo" prohibited                       |

Full explanation with examples: [3. How to Search](03-how-to-search.html).

## Serializing and loading a pre-built index

```javascript
// build time (Node.js)
var json = JSON.stringify(idx)

// load time (browser or Node.js)
var idx = lunr.Index.load(JSON.parse(json))
```

Full explanation: [4. How to Customize & Pre-build](04-how-to-customize.html#pre-building-an-index-for-speed).

## Installing

```
npm install lunr
```

```html
<script src="https://unpkg.com/lunr/lunr.js"></script>
```

## Upgrading from lunr 0.x / 1.x

If you're migrating an old project, the two breaking changes to know about:

- **Indexes are immutable.** All `this.add(doc)` calls must happen inside the `lunr(function () {...})`
  builder — there's no `idx.add(...)` afterwards (see [2. Getting Started](02-getting-started.html)).
- **Pipeline functions take a `lunr.Token`, not a string.** Update `return token.toLowerCase()` to
  `return token.update(function (str) { return str.toLowerCase() })`.

Multi-term search behavior (OR, not AND) is unchanged in how you call `idx.search(...)`. Full
details: [official Upgrading guide](https://lunrjs.com/guides/upgrading.html).

## Elsewhere

- Official guides and full generated API reference: [lunrjs.com](https://lunrjs.com/)
- Source code and issue tracker: [github.com/olivernn/lunr.js](https://github.com/olivernn/lunr.js)
- Non-English language support: [lunr-languages](https://github.com/MihaiValentin/lunr-languages)
