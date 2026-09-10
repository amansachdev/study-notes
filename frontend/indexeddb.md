# IndexedDB

## Core Idea
A database built into the browser that JavaScript uses to persist **structured data** on the user's device. Designed for larger amounts of structured data with **asynchronous** operations.

```
Frontend JavaScript
       ↓
 IndexedDB API
       ↓
Browser's IndexedDB implementation
       ↓
Persistent storage on device
```

## Basic Structure
Roughly like a database — **object stores** (analogous to tables, but not relational SQL):

```
Database
│
├── Object Store: users
│      ├── user-1 → {...}
│      ├── user-2 → {...}
│
├── Object Store: products
│      ├── product-1 → {...}
│      └── product-2 → {...}
│
└── Object Store: messages
       └── ...
```

Object stores hold JS values/objects, accessed using a **key**.

## Storing & Reading Data
```js
const request = objectStore.add({
  id: 123,
  name: "Aman",
  role: "developer"
});
```
Later:
```js
const request = objectStore.get(123);
```
The browser looks up the object by key and returns it **asynchronously**.

## Why Asynchronous?
With hundreds of thousands of records, a blocking read would freeze the main thread. Operations go through **requests**:

```
JavaScript
   │
   │ get(123)
   ▼
IndexedDB
   │
   │ ... lookup ...
   ▼
result
   │
   ▼
success callback / Promise
```

The UI keeps working while the DB operation happens.

## Transactions
Operations run inside transactions — a defined unit of database work.
```
Transaction
│
├── read user
├── update user
└── update timestamp
```
Important when multiple operations must succeed together.

## Indexes
To query by a non-key field, create an index:
```
users
--------------------------------
id    name       email
1     Aman       aman@...
2     Rahul      rahul@...
3     Priya      priya@...
```
Index on `email` → efficient lookup without inspecting every object.

## Where Is It Useful?
- Offline-first applications
- Caching API responses
- Storing large datasets
- Email/message apps, notes apps
- Offline synchronization, PWA data
- Persistent temporary state

```
             Internet
                │
             API call
                │
                ▼
           Frontend
           /       \
       online      offline
          │           │
          ▼           ▼
        API       IndexedDB
                    │
                    ▼
               cached data
```

## IndexedDB ≠ Bigger localStorage

|                 | localStorage     | IndexedDB               |
|-----------------|------------------|-------------------------|
| Data            | Strings          | Structured values       |
| API             | Synchronous      | Asynchronous            |
| Structure       | Key-value        | Object stores + indexes |
| Large datasets  | Poor fit         | Designed for it         |
| Transactions    | No               | Yes                     |
| Queries/indexes | Very limited     | Much richer             |

## Key Insight
`db.objectStore("users").get(123)` is a **request**: the browser queues the lookup, does the DB work off the main thread, and fires a success callback / resolves a promise with the result. JS doesn't block waiting for the data.

## Revision Questions
1. What is IndexedDB? → A browser built-in DB for structured, persistent client-side data.
2. What is an object store? → The IndexedDB container for objects (≈ table, but not SQL).
3. Why is it asynchronous? → So large reads/writes don't freeze the UI main thread.
4. What are transactions? → Units of DB work where related operations succeed together.
5. What are indexes for? → Efficient lookup by non-key fields (e.g. email) without scanning every object.
6. localStorage vs IndexedDB? → Strings/sync/key-value vs structured/async/object stores with indexes + transactions.