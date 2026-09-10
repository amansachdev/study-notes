# Critical Rendering Path (CRP)

## Core Idea
The sequence of steps the browser takes to turn HTML + CSS into pixels on screen.

```
HTML
 ↓
Parse HTML
 ↓
DOM
 +
CSS
 ↓
Parse CSS
 ↓
CSSOM
 ↓
DOM + CSSOM
 ↓
Render Tree
 ↓
Layout
 ↓
Paint
 ↓
Compositing
 ↓
Pixels on screen
```

## 1. DOM
The browser parses HTML and builds the DOM tree.

```html
<body>
  <h1>Hello</h1>
  <p>World</p>
</body>
```

```
Document
└── body
    ├── h1
    └── p
```

## 2. CSSOM
The browser parses CSS and builds the CSS Object Model — rules that can be matched against DOM elements.

```css
h1 {
  color: red;
  font-size: 32px;
}
```

## 3. Render Tree
The browser does **not** simply combine the entire DOM + CSSOM. It determines **which elements need to be rendered** and what styles apply → the render tree.

```html
<div>
  <h1>Hello</h1>
  <p style="display: none">Secret</p>
</div>
```

`<p>` exists in the DOM but `display: none` excludes it from rendering → not in the render tree.

```
DOM + CSSOM
     ↓
Render Tree
```

> Key correction: `DOM + CSSOM → Render Tree` means "determine visible elements + their styles", not "combine the two trees and identify visible elements."

## 4. Layout
The browser calculates geometry of rendered elements:
- width · height · x/y position
- margins · padding
- line wrapping

Also called **layout / reflow**.

```
h1: x=20, y=10, width=500, height=40
p:  x=20, y=60, width=500, height=20
```

## 5. Paint
Determines **what** gets drawn: text, backgrounds, borders, images, shadows.

Produces drawing instructions — not immediately "individual physical pixels."

## 6. Compositing
The browser may divide content into **compositing layers** and the compositor assembles them into the final image.

GPU acceleration can be involved (transforms, animations) — but compositing is **not simply "GPU rendering"**; CPU and GPU both play a role depending on browser and situation.

## Key Distinctions
- **Layout**: where/how big things are.
- **Paint**: what gets drawn.
- **Compositing**: assembles the resulting layers.

> Next up: reflow vs repaint vs compositing — which steps re-run when the page changes.

## Revision Questions
1. What does the render tree contain? → Only elements that participate in rendering (e.g., no `display: none`), with their styles.
2. Is the render tree just DOM + CSSOM combined? → No — it's the subset of elements that need to be rendered with applicable styles.
3. Layout vs Paint? → Layout = geometry; Paint = drawing instructions.
4. Does compositing mean GPU rendering? → No — it assembles layers; CPU/GPU usage varies by browser and situation.
5. Where does `display: none` drop out? → The element is in the DOM but excluded from the render tree.