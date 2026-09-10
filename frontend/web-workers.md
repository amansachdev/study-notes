# Web Workers

## Core Idea
A Web Worker lets frontend JavaScript run in a **separate thread** from the browser's main thread — to stop CPU-heavy JS from blocking the UI.

The main thread handles:
```
Main thread
├── JavaScript
├── DOM work
├── Style/layout
├── Paint coordination
└── User interactions
```

Expensive JS on the main thread → UI can't respond:
```
User clicks
     ↓
     X  ← UI can't respond promptly
Heavy JavaScript
     ↓
Browser becomes responsive again
```

With a worker:
```
Main Thread                    Worker Thread
     │                              │
 UI + DOM                      Heavy calculation
     │                              │
     │ ─────── message ────────────>│
     │                              │
     │ <────── result ──────────────│
     ▼
Update UI
```

## Creating a Worker
Main JavaScript:
```js
const worker = new Worker("worker.js");
worker.postMessage({ numbers: [1, 2, 3, 4] });
worker.onmessage = (event) => {
  console.log(event.data);
};
```
worker.js:
```js
self.onmessage = (event) => {
  const numbers = event.data.numbers;
  const result = expensiveCalculation(numbers);
  self.postMessage(result);
};
```

Key mechanism: **message passing**.
```
Main thread
    │
    │ postMessage()
    ▼
 Worker
    │
    │ postMessage()
    ▼
Main thread
```

## Important Limitation
A normal Worker **cannot access the DOM**:
```js
document.querySelector("#app"); // doesn't work in a worker
```
Intentional — the worker is for computation/background work; the main thread owns the DOM.

## When Are Workers Useful?
- Image/video processing
- Large data processing
- Cryptographic calculations
- Parsing large files
- Complex math
- Search/indexing over large datasets

## Workers + IndexedDB
```
             Main Thread
                 │
          request data/work
                 │
                 ▼
              Worker
              /     \
             /       \
      IndexedDB    computation
             \       /
              \     /
                 │
                 ▼
             Main Thread
                 │
              Update UI
```

## Key Insight
> A Worker doesn't make the calculation **faster** — it keeps expensive work from monopolizing the main UI thread. The UI stays responsive.

## Revision Questions
1. What is a Web Worker? → JS running in a separate thread from the main thread.
2. Why use one? → So CPU-heavy work doesn't block UI/DOM responsiveness.
3. How do main and worker communicate? → Message passing via `postMessage` / `onmessage`.
4. Can a worker touch the DOM? → No — the main thread owns the DOM.
5. Does a worker speed up the calculation? → No — it just keeps it off the main thread.
6. Why process a 500 MB JSON file in a worker? → The heavy parse runs off the main thread, so the UI stays responsive while it works.