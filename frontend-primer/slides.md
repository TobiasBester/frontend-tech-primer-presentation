---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
# page transition
transition: slide-left
# use UnoCSS
css: unocss
---

# Frontend Tech Primer

Tobias Bester | EPI-USE Labs | 2023 | xxxxx

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---

# Contents

- (Meta-)Frameworks and Tools to look out for
- Features and trends of modern web frameworks
  - Rendering on the server
  - Tooling
  - Syntax
  - Loading handlers
  - JS Compilation
  - File-based Routing
  - Resumability
  - Islands

---

# State of JS Frameworks

- [State of JS 2022](https://2022.stateofjs.com/en-US/libraries/front-end-frameworks/)
  - React is (still) very popular
  - Stable usage rate for Vue and Angular
  - Svelte and Solid has a lot of interest and retention
  - Qwik and Solid < 3 years old, gaining attention

<div v-click="1">

- [Stack Overflow Survey 2022](https://survey.stackoverflow.co/2022/#most-loved-dreaded-and-wanted-webframe-love-dread)
  - Phoenix, Svelte, and .NET are beloved

</div>

<div v-click="2">

- [2022 JS Rising Stars](https://risingstars.js.org/2022/en)
  - Rising popularity of "full-stack" frameworks

</div>

---

# Meta-frameworks


- Built on top of standard JS frameworks

<div v-click="1">

- Use existing libraries and new features to provide functionality you probably need:
  - Routing
  - State management
  - Data fetch management
  - Build optimization
- Different rendering modes

</div>

<div v-click="2">

<div class="flex pt-4 justify-around">
  <div class="flex">
    <img src="/React-icon.svg.png" style="max-width: 160px" alt="React Logo" />
    <img src="/Nextjs-icon.png" style="max-width: 160px" alt="NextJS Logo" />
  </div>
  <div class="flex">
    <img src="/Vue-icon.png" style="max-width: 160px" alt="Vue Logo" />
    <img src="/nuxt-icon.svg" style="max-width: 160px" alt="Nuxt Logo" />
  </div>
</div>

</div>

---

# Rendering Modes - Traditional SPA

- Most work is done at build time and on the client

<img src="/trad-spa.png" style="max-width: 720px" alt="Traditional SPA workflow">

---

# Rendering Modes - Static Site Generation
 
- All prerendered at build time

<img src="/ssg.png" style="max-width: 720px" alt="Traditional Static Site generation" />

---

# Rendering Modes - Server-side Rendered

- Render HTML on every request, hydrate on client

<img src="/ssr.png" style="max-width: 720px" alt="Server-side rendering workflow" />

---

# Rendering Modes - Incremental Static Regeneration

- In between SSG and SSR: Generate at build time, SSR upon revalidation

<img src="/isr.png" style="max-width: 720px" alt="Incremental Static Regeneration workflow" />

---

# Rendering Modes - Server Components

- Selected components can be prerendered on the server at request time, e.g. layouts

<img src="/server-components.png" style="max-width: 720px" alt="Server Components workflow" />

---

# Why render server-side?

- ✅ More compute power than browser
- ✅ Access backend resources directly
- ✅ Keep sensitive info on the server
- ✅ Keep large dependencies on the server

<div v-click="1">

- ❌ Interactive pages
- ❌ Pages using browser-only APIs
- ❌ More expensive
- ❌ Requires shift in UI development - edit modal vs edit page

</div>

<div v-click="2">

- Is it viable for user-input and data-driven apps?
  - Maybe sometimes e.g. for reports and read-only data tables
  - Less relevant for non-JS backends

</div>

---

# Tooling

- Improved web standards are paving the way for these frameworks
  - ESModules - Avoid bundling when developing locally
  - Improved HTML Forms - Submit forms without JS
  - fetch(), Request/Response, URL Parsing
- Vite, Deno, Bun

---

# Svelte

<div class="flex justify-between">
  <img src="/svelte.png" style="max-width: 300px" alt="Svelte Logo" />
  <img src="/sveltekit.png" style="max-width: 300px" alt="SvelteKit Logo" />
</div>

---
layout: two-cols
---

# Simpler Syntax

```javascript
import TableRow from './TableRow.svelte'

// reactive
let products = []
let productName = ''

// computed
$: numProducts = products.length

const addProduct = () => {
  products = [...products, {
    id: products.length + 1,
    name: productName,
    price: 50
  }]
  productName = ''
}

```

::right::

```html
<input bind:value={productName} />
<button on:click={addProduct}>Add Product</button>

<p>Displaying {numProducts} products</p>

{#if numProducts === 0}
  <h4>Add a product to get started</h4>
{:else}
  <table>
      <thead>
          <th>ID</th>
          <th>Name</th>
          <th>Price</th>
      </thead>
      <tbody>
          {#each products as product}
          <TableRow {product} />
          {/each}
      </tbody>
  </table>
{/if}
```

---
layout: two-cols
---

# Managing Data Loading

```javascript
import { onMount } from 'svelte'

async function fetchProduct() {
  const res = await fetch('/api/product/1')
  const product = await res.json()
  
  if (res.ok) {
    return product
  } else {
    throw new Error('Oi noi')
  }
}

let promise = fetchProduct()
```

::right::

```html
{#await promise}
  <p>Fetching Product... BE PATIENT 😡</p>
{:then product}
  <Product {product} />
{:catch error}
  <p>{error.message}</p>
{/await}
```

---

# Managing Data Loading

- In Vue 3 (experimental)

```vue
<Suspense>
  <!-- Async component -->
  <ProductList />

  <template #fallback>
    <p>Fetching Products...</p>
  </template>
</Suspense>
```

---

# Layouts

- Re-use templates for layout
- `src/routes/checkout/+layout.svelte`

```html
<nav>
  <a href="/">Home</a>
  <a href="/search">Search</a>
</nav>

<button>Back</button>
<slot />
```

---

# Progressive Enhancement

- Enable JS features when we're sure the user has JS
- If JS is available, post data, update page and component props, render an error message if necessary
- If JS is unavailable, post data, reload page on response

```html
<script>
  import { enhance } from '$apps/forms'
  /* ... */
</script>

<form method="POST" action="?/create" use:enhance>
  <!-- ... --->
</form>
```

---

# SolidJS

<div class="flex justify-around">
  <div class="cols-12">
    <img src="/solid-logo.png" style="max-width: 300px" alt="Solid Logo" />
  </div>
  <div>
    <img src="/solid-start-logo.png" style="max-width: 300px" alt="Solid Start Logo" />
  </div>
</div>

---

# SolidJS

- Similar syntax to React, simpler state

```jsx
function Products() {
  const [products, setProducts] = createSignal([])
  const [productName, setProductName] = createSignal('')
  const numProducts = () => products().length

  return (
    <Show
      when={numProducts() > 0}
      fallback={<h4>Add a product to get started</h4>}
    >
      <table>
        <tbody>
          <Index each={products()}>{(product, i) =>
            <TableRow product={product()}/>
          }</Index>
        </tbody>
      </table>
    </Show>
  )
}
```

---

# Solid [Performance](https://krausest.github.io/js-framework-benchmark/2023/table_chrome_110.0.5481.77.html)

- Solid [Signals](https://www.solidjs.com/guides/reactivity) provide fine-grained reactivity, similar to Vue's `ref()` and `computed()`
- Render functions are more like constructors which are run once and then vanished
  - Updates are done separately
  - Lazy prop evaluation - only when accessed in the child
  - This has implications for code
- No virtual DOM, JSX is [compiled](https://www.solidjs.com/guides/rendering) into optimized JS, direct DOM updates

<div v-click="1">

- [Vue Vapor Mode](https://blog.vuejs.org/posts/2022-year-in-review.html)

</div>

---

# File-Based Routing

- `hogwarts.com/blog ➜ /routes/blog.tsx`
- `hogwarts.com/staff/positions ➜ /routes/staff/positions/index.tsx`
- `hogwarts.com/students/:id ➜ /routes/students/[id].tsx`

<div v-click="1">

- [SolidStart](https://start.solidjs.com/core-concepts/routing) provides Route Groups and Index Renaming to solve some routing headaches

```
|-- routes/
    |-- (static)
        |-- about-us/                   // example.com/about-us
            |-- index.tsx
        |-- users/                      // example.com/contact-us
            |-- (all-users).tsx         // example.com/users
            |-- [id].tsx                // example.com/users/:id
            |-- projects.tsx            // example.com/users/projects
    |-- shop/
        |-- ...
```

</div>

---

# API Routes

- `/routes/api/students.ts` -> `/api/students`

```ts
export async function GET({ params }) {
    const students = await hogwarts.getStudents(params.house, params.year)
    return json({ students })
}
```

- Reasons to use:
  - Expose a public-facing REST API
  - Serve additional clients (not just app client)
  - Write webhooks or auth callback handlers for OAuth
  - Serve non-HTML content like PDFs or images
- Might be an alternative to full-blown full-stack frameworks, but probably not dedicated backends

---

# Qwik

<div class="flex justify-around">
  <img src="/qwik-logo.png" style="max-width: 400px" alt="Qwik Logo" />
</div>

---

# Placing more focus on the server

- Once a page is rendered on the server, hydration takes place on the client
- Hydration involves:
  - Installing event listeners on the associated DOM nodes
  - Building (internal) component tree
  - Restoring application state

<div v-click="1">

- Instead of hydration, Qwik uses Resumability
  - Pause rebuilding application state on the server and resume on the client
  - Serialize execution elements into the HTML itself
  - All JS initialization is done on the server, so no client runtime is sent
- In general, the bare minimum JS is sent and executed as late as possible

</div>

---

- Serialized state in HTML

```html
  <html q:key="0t_1">
    <head q:head>
      <title q:head>Tutorial</title>
    </head>
    <body>
      <!--qv q:id=0 q:key=Ncbm:0t_0--><tt>&lt;App&gt;</tt><tt>largeData</tt>:
      {"data":"PRETEND THIS IS LARGE DATASET"}<br />Click
      <button
        on:click="app_component__fragment_button_onclick_mnnondgjcgg.js#app_component__Fragment_button_onClick_MNNOnDGJCgg[0]"
        q:id="1"
      >
        +1</button
      ><!--qv q:id=2 q:key=5Sva:4e_0--><tt>&lt;Child&gt;</tt>
      <!--t=3-->1<!----><!--/qv--><!--/qv-->
    </body>
  </html>
```

---
layout: two-cols
---

## Execute client-side code on the server 🤨

- No need to call `fetch`
- Possibly useful for analytics

::right::

<Tweet id="1625473748777291780" scale="0.65" />

---

# Astro

<div class="flex justify-around">
  <div style="background: white">
    <img src="/astro-icon.svg" style="max-width: 600px" alt="Astro Logo" />
  </div>
</div>

---

# Islands Architecture

- A sea of static HTML with islands of interactive UI components

<div class="flex justify-around">
    <img src="/astro-islands.png" style="max-width: 400px" alt="Astro Island Architecture" />
</div>

---

# Meta-frameworkify your favourite framework

- Islands can be implemented using a mixture of different frameworks
- Astro will render the framework code to HTML on the server
- JS will only be sent to the islands on the client
- Control when hydration takes place for an island using directives

```html
<!-- Will initialize upon page load -->
<CtaButton client:load />

<!-- Will initialize when component has scrolled into view -->
<TutorialVideoPlayer client:visible />
```

---

# Q: Does any of this matter?

- ### A: Nope, NextBigThing.js will transform frontend development and make all these frameworks irrelevant forever (until next month)
