# Declarative Shadow DOM - DSD
DSD is a web platform feature that allows you to create and use HTML *Shadow DOM* without needing JavaScript.

Before DSD, the *Shadow DOM* (which encapsulates styles and markup for *Web Components*)
could only be created imperatively using JavaScript via `element.attachShadow({mode: 'open'})`.
Because it relied on JavaScript, it meant *Web Components* could not be cleanly rendered on the server
(*Server-Side Rendering*, or *SSR*) because the server had no native way
to represent that encapsulated boundary in plain HTML.

## How DSD Works (Output Format)
DSD solves the SSR problem by introducing a new template attribute: `<template shadowrootmode="open">`.

When a modern browser parses a backend-rendered response containing this tag,
it automatically converts the template into a live Shadow DOM boundary on the fly.
```html
<!-- Server Output (DSD) -->
<my-custom-element>
  <template shadowrootmode="open">
    <style>
      p { color: royalblue; }
    </style>
    <p>Hello from the encapsulated server-rendered DOM!</p>
  </template>
</my-custom-element>
```


## Why it is not great as an authoring format

While DSD is an excellent blueprint for a server to send down to a browser,
writing raw DSD by hand (authoring it) is tedious and repetitive for developers because:
1. Redundancy: If you use `<my-custom-element>` 50 times on a page,
   you have to duplicate the entire `<template shadowrootmode="open">` block
   and its internal `<style>` blocks 50 times in your HTML source file.
2. Maintenance: Updating a single class or style inside the component
   requires updating every single instance across your backend template pages.

Because of this, developers prefer using an *authoring format* (like *JSX*,
component classes, Lit-ssr, or custom backend frameworks) where they define
the component's markup and styles exactly once.

The framework or build tool then takes care of duplicating that layout
and emitting the raw DSD HTML as the final output format sent to the client.