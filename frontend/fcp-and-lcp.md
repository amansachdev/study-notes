# First Contentful Paint (FCP)

## Core Idea
A frontend performance metric measuring how quickly the browser **first renders any actual content** from the page.

> FCP = time from navigation start until the first piece of DOM content is painted.

That content can be:
- Text
- An image
- An SVG
- A `<canvas>` element
- Other non-white content

## Pipeline
```
User enters URL
      ↓
HTML received
      ↓
DOM + CSSOM
      ↓
Render Tree
      ↓
Layout
      ↓
Paint
      ↓
┌─────────────────┐
│     Hello       │  ← First visible content
└─────────────────┘
      ↑
     FCP
```

## FCP vs Page Load
FCP does **not** mean the page has finished loading.

```
0 ms        800 ms              5,000 ms
 |------------|--------------------|
 navigation   FCP                  fully loaded
              ↑
          user sees content
```

If the first text appears at 800 ms → FCP = 800 ms, even if the page keeps loading for seconds.

## FCP vs First Paint (FP)
- **FP**: first time the browser paints *anything*.
- **FCP**: first time it paints *meaningful content* (text, image, etc.).

A blank background or browser-generated visual can count toward FP, but not FCP.

## FCP and the CRP
Anything that delays reaching the first meaningful paint increases FCP — e.g. render-blocking CSS or a slow server response.

> Mental model: **FCP answers "When did the user first see something from the page?"**

## Revision Questions
1. What is FCP? → Time from navigation until the first piece of content is painted.
2. What counts as "content"? → Text, images, SVG, canvas, non-white content.
3. FP vs FCP? → FP = first paint of anything; FCP = first paint of meaningful content.
4. Does FCP mean page load is done? → No — it's just when content first appears.

---

# Largest Contentful Paint (LCP)

## Core Idea
Measures when the **largest qualifying content element** visible in the viewport has **finished rendering**.

> "Largest" refers to the **size of the content on screen**, not the time taken to render it.

## Example
```
Page loads
   ↓
FCP at 800 ms
   ↓
"Welcome to our website" appears
   ↓
Hero image finishes rendering at 2,000 ms
   ↓
LCP = 2,000 ms
```

The hero image is larger visually → becomes the LCP candidate.

## Typical LCP Candidates
- Large `<img>`
- Large image loaded via CSS
- `<video>` poster/image
- Large heading or block of text
- Background image (in some cases)

## FCP vs LCP
- **FCP**: "When did the first piece of content appear?"
- **LCP**: "When did the largest important piece of content in the viewport appear?"

The time is the metric's **value**; "largest" describes the **content element** being measured.

## Revision Questions
1. What does LCP measure? → When the largest qualifying element in the viewport finishes rendering.
2. Does "largest" mean the longest render time? → No — it means the largest on-screen size.
3. Typical LCP candidates? → Large images, video posters, big headings/text blocks.
4. FCP vs LCP? → FCP = first content; LCP = largest content.