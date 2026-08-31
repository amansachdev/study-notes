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

---

# Posting List

## Core Idea
The list of documents associated with a particular term in an inverted index.

| Term   | Posting List  |
|--------|---------------|
| apple  | [1, 3, 5, 8]  |
| banana | [2, 3, 5, 9]  |
| mango  | [1, 4, 7]     |

- `apple`, `banana`, `mango` → terms
- `[1, 3, 5, 8]` → posting list for "apple"
- Each number → document ID

Think of it as: **Term → Posting List**

## In Real Engines
A posting list can hold more than document IDs — it may also store:
- **Term frequency** (how often the term appears in a doc)
- **Positions** (where the term occurs within each doc)

## Reading a Posting List
`apple → [1, 3, 5, 8]` means documents **1, 3, 5, and 8** contain "apple".

## Revision Questions
1. What is a posting list? → The list of document IDs for a given term.
2. What does `5` in `apple → [1, 3, 5, 8]` represent? → Document ID 5.
3. What extra info can a posting list store? → Term frequency and positions.

---

# Analyzer

## Core Idea
Converts **raw text** into **searchable tokens**.

```
Input: "The Quick Brown Fox"
            ↓
Analyzer:  ["quick", "brown", "fox"]
```

## Steps an Analyzer May Perform
- **Tokenization** — split text into words
- **Lowercasing** — `Quick` → `quick`
- **Stop-word removal** — `the` → removed
- **Stemming/lemmatization** — `running` → `run` (config-dependent)

## Pipeline
```
Document
   ↓
Analyzer
   ↓
Tokens
   ↓
Inverted Index
   ↓
Posting Lists
```

> Key insight: the analyzer determines **which tokens** get indexed — so it directly shapes search behavior (e.g., "Quick" and "quick" match after lowercasing).

## Revision Questions
1. What does an analyzer do? → Turns raw text into searchable tokens.
2. Name the steps. → Tokenization, lowercasing, stop-word removal, stemming/lemmatization.
3. Where does it sit in the pipeline? → Between the document and the inverted index.

---

# Tokenization & Analysis Steps

## Hierarchy
```
Raw text
   ↓
Tokenization
   ↓
Tokens
   ↓
Other analysis steps
   ↓
Indexed tokens
```

## Example
`"I love system design"` → `["I", "love", "system", "design"]` (tokenization)
Then optional: lowercasing, stop-word removal, etc.

---

# Query Analyzer Consistency

## Core Idea
The **query must go through the same analyzer as the documents** — otherwise the query looks for terms that don't exist in the index.

## Example
Document: `"The Apple Store"` → `["apple", "store"]`
Query `"Apple Store"` must also become `["apple", "store"]` to match.

> If the document analyzer lowercases `Apple` → `apple`, but the query analyzer doesn't, the query would try to find `Apple` — which isn't in the index → no match.

## Revision Questions
1. Why analyze the query with the same analyzer? → So query terms match the indexed terms (consistency).
2. What breaks if they differ? → Queries miss matches (e.g., "Apple" vs "apple").

---

# Stemming

## Core Idea
Reduces a word to a common root-like **stem** so related word variations match during search.

`connected`, `connecting`, `connection` → `connect`

A search for `"connect"` can then match documents containing `"connecting"`.

## Stemming ≠ Tokenization
`"Users are running quickly"`

| Tokenization   | Stemming        |
|----------------|-----------------|
| Users, are, running, quickly | user, are, run, quick |

Exact result depends on the stemming algorithm.

## Revision Questions
1. What is stemming? → Reducing words to a common stem so variations match.
2. How is it different from tokenization? → Tokenization splits words; stemming normalizes them to roots.
3. Example? → connecting/connection/connected → connect.