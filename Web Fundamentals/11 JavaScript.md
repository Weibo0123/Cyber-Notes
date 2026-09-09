# JavaScript

## Role in the Stack

```
HTML  → structure
CSS   → appearance
JavaScript → behavior / interactivity
```
JavaScript runs primarily **client-side**, inside the browser, but can also run **server-side** (e.g. Node.js) — so "JavaScript" alone doesn't tell you which side of the client/server boundary a given piece of code executes on.

## Including JavaScript

```html
<script type="text/javascript"> /* inline code */ </script>
<script src="./script.js"></script>          <!-- external file -->
```

## DOM Manipulation

JavaScript reads and modifies the page live through the DOM (file 09):
```javascript
document.getElementById("button1").innerHTML = "Changed Text!";
```
`getElementById()` locates an element; `innerHTML` replaces its content — and because `innerHTML` **parses** whatever string it's given as HTML, feeding it unsanitized data is a common source of injection bugs (see the Web Vulnerabilities notebook, HTML Injection/XSS).

## What JavaScript Is Commonly Used For

- Dynamically updating page content without a reload
- Accepting/processing user input
- Interactive UI behavior
- Making HTTP requests to the back end and handling the results
- Automating multi-step client-side processes

### AJAX

**AJAX (Asynchronous JavaScript and XML)** — despite the name, not limited to XML — lets JavaScript send/receive HTTP requests in the background, without a full page reload. `fetch()` (file 05) is the modern way to do this.

## JavaScript Frameworks

Large applications get unwieldy in plain JavaScript, so frameworks/libraries provide structure and reusable abstractions:
```
Angular   React   Vue   jQuery
```
Some are pure JavaScript; others use a different syntax/language that's compiled or transpiled *into* JavaScript before it runs in the browser.

## Cheat Sheet

```
<script>...</script>              inline JS
<script src="file.js"></script>     external JS

document.getElementById("id")         locate an element
element.innerHTML = "..."               replace content (parses as HTML — injection risk if untrusted)

AJAX / fetch()    → async HTTP requests from JS, no full page reload
Frameworks: Angular, React, Vue, jQuery
JS runs client-side by default; Node.js = JS on the server
```
