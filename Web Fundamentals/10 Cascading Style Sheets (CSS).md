# Cascading Style Sheets (CSS)

## CSS Basics

**CSS** is a stylesheet language that defines the appearance and layout of HTML elements — fonts, colors, alignment, backgrounds, sizing, borders, margins/padding, and positioning.

A CSS rule = a **selector** + one or more declarations:
```css
selector {
    property: value;
}
```
```css
h1 {
    color: white;
}
```
`h1` — selector, `color` — property, `white` — value.

## Selectors

```css
body { background-color: black; }     /* targets ALL <body> elements (tag selector) */

.text { font-size: 16px; }              /* targets class="text" */
#title { color: red; }                    /* targets id="title" */
```
```html
<p class="text">Hello</p>
<p id="title">Welcome</p>
```
Tag selectors apply broadly; `class` selectors (`.name`) apply to any element sharing that class; `id` selectors (`#name`) target exactly one element (IDs are meant to be unique per page).

## Animations

CSS can animate elements without JavaScript:
```
@keyframes           defines the animation's stages
animation              shorthand property combining animation settings
animation-duration      how long it runs
animation-direction      forward, reverse, alternate, etc.
```
CSS and JavaScript are often combined: JS toggles classes or inline styles, and CSS handles the resulting visual transition/animation.

## CSS Frameworks

Pre-built styles and components that reduce how much you need to style manually: **Bootstrap**, **SASS**, **Foundation**, **Bulma**, **Pure**. Commonly used alongside JavaScript frameworks in modern web apps.

## Cheat Sheet

```
selector { property: value; }

tag selector      →  h1 { ... }        all matching elements
.class selector    →  .text { ... }      elements with that class
#id selector         →  #title { ... }     one specific element

@keyframes / animation / animation-duration / animation-direction

Frameworks: Bootstrap, SASS, Foundation, Bulma, Pure
```
