# HTML & the DOM

## HTML Basics

**HTML (HyperText Markup Language)** structures a web page's content: headings, text, forms, images, metadata, and other components. The browser receives HTML from the server and **renders** it into what you see.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Page Title</title>
    </head>
    <body>
        <h1>A Heading</h1>
        <p>A Paragraph</p>
    </body>
</html>
```
This is a hierarchical structure:
```
document
└── html
    ├── head
    │   └── title
    └── body
        ├── h1
        └── p
```

## Elements, Tags & Attributes

An element = opening tag + content + closing tag:
```html
<p>A Paragraph</p>
```
Attributes like `id` and `class` identify elements for scripting/styling:
```html
<p id="para1">A Paragraph</p>
<p class="red-paragraph">A Paragraph</p>
```

## `<head>` vs. `<body>`

| | Contains | Directly visible? |
|---|---|---|
| `<head>` | `<title>`, `<style>` (CSS), `<script>` (JS), metadata | No |
| `<body>` | The actual page content | Yes |

## The DOM (Document Object Model)

The **DOM** is a language-neutral, in-memory representation of a document's content, structure, and style, exposed as a tree of objects that scripts (and the browser itself) can read and modify:
```javascript
document.head
```
Elements can be located by `id`, tag name, or `class`. Three commonly-referenced DOM flavors: **Core DOM** (all document types), **XML DOM**, **HTML DOM**.

⚠️ **Page source ≠ live DOM.** "View Page Source" (`Ctrl+U`) shows the original HTML *as sent by the server*. Once JavaScript runs, it can modify the DOM — adding, removing, or changing elements — without those changes ever appearing in "View Source." To see the page's *current* live state, use the browser's **Inspector/Elements** panel instead, which reflects the DOM as it exists right now, post-JavaScript.

## Page Source and Hidden Information

Page source contains everything the server sent — including things not meant to be seen:
```html
<!-- comment -->
```
HTML comments are **not rendered** on the page, but they remain fully present in the source, retrievable by anyone who checks. Never assume something is "hidden" just because it isn't visually rendered.

## URL Encoding (Cross-Reference)

HTML sometimes needs to represent URLs or characters unsafe for direct inclusion — this is the same percent-encoding scheme covered in file 02 (URLs & URL Encoding); e.g. a literal `'` becomes `%27`.

## Cheat Sheet

```
<!DOCTYPE html><html><head>...</head><body>...</body></html>

<head>  → title, CSS, JS, metadata — not directly visible
<body>  → actual visible content

id="x"      unique identifier, for CSS/JS targeting
class="x"    shared identifier, for styling/grouping

DOM = live, in-memory, scriptable tree representation of the document
View Source = ORIGINAL server-sent HTML — does NOT reflect JS-driven DOM changes
<!-- comment -->  → invisible when rendered, but still fully present in the source
```
