# Inverted Index

## Core Idea
A data structure that maps **words → documents** instead of documents → words.

| Normal index          | Inverted index        |
|-----------------------|-----------------------|
| Doc 1 → apple, banana | apple  → Doc 1, Doc 3 |
| Doc 2 → banana, mango | banana → Doc 1, Doc 2 |
| Doc 3 → apple, mango  | mango  → Doc 2, Doc 3 |

## Example
Doc 1: "apple banana" · Doc 2: "banana mango" · Doc 3: "apple mango"

- `apple  → [Doc 1, Doc 3]`
- `banana → [Doc 1, Doc 2]`
- `mango  → [Doc 2, Doc 3]`

## Why "Inverted"?
The relationship is reversed — index by term, not by document.

## Performance
- **Find term**: ~O(1) via hash table lookup on the word
- **Process results**: O(k), where k = number of matching documents
- **Without inverted index**: O(N) — scan every document
- **With inverted index**: O(1) + O(k)

> Key insight: cost depends on **k (matches)**, not total document count **N**. Result/document size matters when retrieving or ranking results.

## Used In
Web search, document search, Elasticsearch-like systems.

## Revision Questions
1. Why is it faster than scanning all documents? → Direct hash lookup, no full scan.
2. Is the full search still O(1)? → No — postings list size k matters: O(1) + O(k).
3. What changes if the index is not a hash table? → Lookup cost rises (e.g., tree → O(log n)).