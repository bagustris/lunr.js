---
layout: default
title: How to Search
order: 4
---

# 3. How to Search

[← Back to docs home](index.html) · [← 2. Getting Started](02-getting-started.html)

Once you have an index (see [2. Getting Started](02-getting-started.html)), every search goes
through one method:

```javascript
idx.search("your query here")
```

The query string supports a small syntax of its own. This page is a cookbook of what you can put
in that string.

## Plain terms

```javascript
idx.search("ishmael")
```

Matches documents containing that word (after normalization — see the note on stemming below).

## Multiple terms (OR, not AND)

```javascript
idx.search("whale ship")
```

Matches documents containing **either** word. Documents containing *both* score higher than
documents containing just one — but a document with only "ship" still shows up. If you want to
*require* both, see [required terms](#required-and-prohibited-terms) below.

## Search a specific field

If your query targets one field only, prefix the term with the field name and a colon:

```javascript
idx.search("title:whale")
```

Only matches the `title` field — the field name must be one you registered with `this.field(...)`
when building the index.

## Wildcards

Use `*` to match any number of unknown characters, anywhere in a term:

```javascript
idx.search("whal*")   // starts with "whal"
idx.search("*hale")   // ends with "hale"
idx.search("w*e")     // starts with "w", ends with "e"
```

Two caveats:
- A wildcard at the *start* of a term (`*hale`) is slower on large indexes, because it can't
  narrow the search down as quickly.
- Wildcarded terms skip the normal stemming step (see below), so `*rolls*` won't find a document
  indexed as `roll`. Search for the unstemmed word you actually expect to appear.

## Boosting terms

Make one term matter more than the others in the same query, using `^` followed by a number:

```javascript
idx.search("whale^10 ship")
```

Here "whale" is weighted 10× more heavily than "ship" when scoring — documents mentioning "whale"
will tend to rank higher.

## Fuzzy matching (typo tolerance)

Add `~` followed by a number to allow that many character-level differences (added, removed,
changed, or swapped characters):

```javascript
idx.search("whale~1")
```

Matches "whale" itself, and anything one edit away — "whal" (dropped a letter), "whales" (added a
letter), "whake" (changed a letter), and so on. Keep this number small (1 or 2); larger values get
slow and start returning irrelevant matches.

## Required and prohibited terms

By default every term is optional (see the OR behavior above). You can change that per term:

| Prefix | Meaning |
|--------|---------|
| `+term` | document **must** contain this term |
| `-term` | document **must not** contain this term |
| `term`  | document *may* contain this term (default) |

```javascript
idx.search("+whale -moby")   // must have "whale", must NOT have "moby"
idx.search("+whale +ship")   // must have BOTH — this is how you simulate AND
```

## Combining modifiers

Field, boost, fuzzy, and presence can all be combined on one term:

```javascript
idx.search("+title:whale^10~1")
```

Must appear in the `title` field, weighted 10×, allowing 1 character of fuzziness.

## Why word order and exact spelling sometimes don't matter

Before indexing, lunr.js runs your text through a processing pipeline that:

1. splits it into individual words (tokens),
2. removes very common words ("the", "and", "of" — these are called *stop words*),
3. reduces words to a root form (*stemming*) — "searching", "searched", and "searches" all become
   "search".

The same pipeline runs on your query terms (except wildcarded ones, as noted above), which is why
searching "searched" can still find a document containing "searching".

## Quick reference

| Syntax                | Meaning                                  |
|------------------------|-------------------------------------------|
| `foo bar`              | documents with "foo" OR "bar"             |
| `title:foo`            | "foo" only in the `title` field           |
| `foo*` / `*foo` / `f*o`| wildcard match                            |
| `foo^10`               | boost this term's weight                  |
| `foo~1`                | fuzzy match, up to 1 edit distance        |
| `+foo`                 | "foo" is required                         |
| `-foo`                 | "foo" is prohibited                       |

---

Next: [4. How to Customize & Pre-build](04-how-to-customize.html) — speeding things up for larger
datasets and static sites.
