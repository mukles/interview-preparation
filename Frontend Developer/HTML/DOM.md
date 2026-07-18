

**What is the Real DOM?**

### Answer

> The **Real DOM (Document Object Model)** is the browser's actual in-memory representation of an HTML document. When the browser parses HTML, it creates a tree-like structure where every HTML element, attribute, and text node becomes a node in the DOM tree.
> 
> JavaScript can interact with this DOM through browser APIs like `document.getElementById()`, `querySelector()`, or `createElement()`, allowing developers to read, modify, add, or remove elements dynamically.
> 
> The Real DOM is what the browser uses to render the user interface on the screen. Any changes made to it can trigger layout calculations, repainting, or reflow, which are relatively expensive operations, especially in large applications.

---

### Example

```html
<body>
  <div id="app">
    <h1>Hello</h1>
    <button>Click</button>
  </div>
</body>
```

The browser creates a DOM tree like:

```text
Document
│
└── html
    │
    ├── head
    │
    └── body
         │
         └── div#app
               │
               ├── h1
               │     └── "Hello"
               │
               └── button
                     └── "Click"
```

Each node is a JavaScript object managed by the browser.

---

## Why is updating the Real DOM expensive?

Updating the Real DOM is more than just changing an element. The browser may need to:

1. Update the DOM tree.
    
2. Recalculate styles (CSS).
    
3. Perform **layout (reflow)** to determine element positions and sizes.
    
4. **Repaint** pixels on the screen.
    
5. **Composite** layers to produce the final display.
    

These steps can be costly, especially if many elements change frequently.

---

## Example

Without React:

```javascript
const title = document.getElementById("title");
title.textContent = "Hello Mukles";
```

This directly updates the Real DOM. The browser may then need to recalculate layout and repaint the affected area.

---

## Why React introduced the Virtual DOM

React doesn't update the Real DOM on every state change. Instead, it:

1. Creates a new **Virtual DOM** tree.
    
2. Compares it with the previous Virtual DOM using the **reconciliation** process.
    
3. Identifies the minimal set of changes.
    
4. Applies only those changes to the Real DOM.
    

This reduces unnecessary DOM manipulations and helps improve performance.




# What is Reflow?

**Reflow** (also called **Layout**) is the process where the browser recalculates the **size and position** of elements on the page.

Whenever a change affects the layout, the browser needs to determine where every affected element should be placed.

### Examples that trigger Reflow

- Changing an element's width or height
    
- Changing font size
    
- Adding or removing DOM elements
    
- Changing margins or padding
    
- Resizing the browser window
    
- Changing `display` from `none` to `block`
    

Example:

```javascript
const box = document.getElementById("box");
box.style.width = "300px";
```

Since the width changes, the browser must recalculate the layout.

---

# What is Repaint?

**Repaint** is the process of redrawing an element **without changing its layout**.

If only the appearance changes, the browser doesn't need to recalculate positions—it just repaints the pixels.

### Examples that trigger Repaint

- Changing text color
    
- Changing background color
    
- Changing border color
    
- Changing visibility (in some cases)
    

Example:

```javascript
box.style.backgroundColor = "red";
```

The element stays in the same position and size, so only a repaint is needed.

---

# Difference

|Reflow|Repaint|
|---|---|
|Recalculates layout|Redraws pixels|
|Changes size or position|Changes appearance only|
|More expensive|Less expensive|
|Usually triggers a repaint|Doesn't trigger a reflow|

---

# Browser Rendering Pipeline

When rendering a page, the browser generally goes through these stages:

```text
HTML
   ↓
DOM Tree

CSS
   ↓
CSSOM

DOM + CSSOM
      ↓
Render Tree
      ↓
Layout (Reflow)
      ↓
Paint (Repaint)
      ↓
Composite
      ↓
Screen
```

### What each stage does

- **DOM**: Parses HTML into a tree.
    
- **CSSOM**: Parses CSS into a tree.
    
- **Render Tree**: Combines visible DOM elements with their computed styles.
    
- **Layout (Reflow)**: Calculates the size and position of each element.
    
- **Paint (Repaint)**: Draws pixels (text, colors, borders, shadows, etc.).
    
- **Composite**: Combines layers and displays the final result on the screen.
    

---

# Real Example

Imagine this HTML:

```html
<div class="box">Hello</div>
```

### Change 1

```javascript
box.style.color = "blue";
```

The size and position don't change.

✅ Repaint only.

---

### Change 2

```javascript
box.style.width = "500px";
```

The width changes.

The browser must:

- Recalculate layout (**Reflow**)
    
- Redraw the element (**Repaint**)
    

---

### Change 3

```javascript
box.remove();
```

Removing an element changes the document structure.

The browser must:

- Recalculate layout (**Reflow**)
    
- Redraw the page (**Repaint**)
    

---

# Why React Helps

A React state update doesn't automatically mean the browser performs a reflow or repaint.

React first:

1. Creates a new Virtual DOM.
    
2. Compares it with the previous Virtual DOM.
    
3. Updates only the parts of the Real DOM that actually changed.
    

By minimizing DOM changes, React reduces unnecessary layout recalculations and paints.

---

# Interview Answer (1 minute)

> "Reflow, also known as layout, is when the browser recalculates the size and position of elements after a layout-affecting change, such as modifying an element's width or adding a new DOM node. Repaint is when the browser redraws an element because its visual appearance changed, such as its color or background, without affecting its layout. Reflow is generally more expensive because it can require recalculating the layout of other elements, and it is often followed by a repaint. React helps reduce unnecessary reflows and repaints by minimizing changes to the Real DOM through its Virtual DOM and reconciliation process."