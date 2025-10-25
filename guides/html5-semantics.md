# HTML5 and Semantic Markup

Semantic HTML is the foundation of accessible, maintainable web applications. HTML5 provides meaningful elements that both improve SEO and accessibility.

## Goals of HTML5

The following are the main goals:

- The use of semantic (meaningful) markup is encouraged
- Distinguishing design from content and promoting design responsiveness
- The overlap between HTML, CSS, and JavaScript is being reduced
- Rich media experiences can be supported without plugins like Flash, Java

## Core Principle: Semantics Over Styles

Always use HTML tags when they can add meaning, structure, or semantics to your content.

### ✅ Good: Semantic HTML

```html
<!-- Meaningful emphasis -->
<p>I <strong>love</strong> cheese</p>

<!-- Clear heading hierarchy -->
<h1>Main Title</h1>
<h2>Section Title</h2>
<h3>Subsection Title</h3>

<!-- Proper article structure -->
<article>
  <h2>Article Title</h2>
  <p>Article content...</p>
</article>
```

### ❌ Bad: Div Soup

```html
<!-- ❌ No meaning -->
<div class="title">Main Title</div>
<div class="content">Content goes here</div>
<div class="emphasis">Important text</div>
```

Always use CSS when you are changing the visual appearance of your page. The title heading on your page is a `<h1>...</h1>` (end of story), but you can make it bold or not, big or not, purple or not using CSS.

## The Acid Test

A good acid test is to imagine how a screen reader will interpret your page. If you view the page without any stylesheets attached, it should ideally show your content in a linear, minimal fashion, that is in fact quite ugly, but that conveys all the content you wanted to include on the page.

## Semantic HTML Elements

### Document Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document Title</title>
</head>
<body>
  <header>Site header</header>
  <nav>Navigation</nav>
  <main>
    <article>
      <h1>Article Title</h1>
      <p>Content</p>
    </article>
  </main>
  <aside>Sidebar content</aside>
  <footer>Site footer</footer>
</body>
</html>
```

### Text Semantics

```html
<!-- Emphasis vs strong -->
<p><em>This is emphasized text</em></p>
<p><strong>This is strongly emphasized text</strong></p>

<!-- Citations -->
<blockquote cite="source-url">
  <p>A quote or citation</p>
  <footer>— <cite>Author Name</cite></footer>
</blockquote>

<!-- Time -->
<time datetime="2024-01-15">January 15, 2024</time>

<!-- Code -->
<code>function example() { }</code>

<!-- Preformatted text -->
<pre><code>const greeting = "Hello";</code></pre>
```

## Form Elements

```html
<form>
  <fieldset>
    <legend>Personal Information</legend>
    
    <label for="name">Name:</label>
    <input type="text" id="name" name="name" required>
    
    <label for="email">Email:</label>
    <input type="email" id="email" name="email" required>
    
    <label for="message">Message:</label>
    <textarea id="message" name="message"></textarea>
    
    <button type="submit">Submit</button>
  </fieldset>
</form>
```

## Accessible Components

### Buttons

```html
<!-- Semantic button for actions -->
<button type="button">Click me</button>

<!-- Anchor for navigation -->
<a href="/page">Navigate</a>

<!-- Don't use div with onClick -->
<!-- ❌ Bad: <div onClick={handler}>Click</div> -->
```

### Lists

```html
<!-- Navigation list -->
<nav>
  <ul>
    <li><a href="/">Home</a></li>
    <li><a href="/about">About</a></li>
  </ul>
</nav>

<!-- Description list -->
<dl>
  <dt>Term</dt>
  <dd>Description</dd>
</dl>
```

### Tables

```html
<table>
  <caption>Monthly Sales</caption>
  <thead>
    <tr>
      <th scope="col">Month</th>
      <th scope="col">Sales</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">January</th>
      <td>$1000</td>
    </tr>
  </tbody>
</table>
```

## Design System Components

### Cards

```html
<!-- Semantic card structure -->
<article class="card">
  <header>
    <h2>Card Title</h2>
  </header>
  <div class="card-body">
    <p>Card content</p>
  </div>
  <footer>
    <button>Action</button>
  </footer>
</article>
```

### Modals

```html
<!-- Dialog element for modals -->
<dialog id="modal">
  <h2>Modal Title</h2>
  <p>Modal content</p>
  <button onclick="document.getElementById('modal').close()">
    Close
  </button>
</dialog>
```

### Accordions

```html
<details>
  <summary>Expand me</summary>
  <p>Hidden content revealed when expanded.</p>
</details>
```

## Accessibility Integration

HTML semantics directly support accessibility:

```html
<!-- ARIA attributes enhance semantics -->
<button aria-expanded="false" aria-controls="dropdown">
  Menu
</button>

<!-- Role attributes for complex components -->
<div role="alert" aria-live="polite">
  Error message
</div>
```

## Responsive Semantics

Some HTML elements are responsive by default:

```html
<!-- Responsive image -->
<img 
  src="image.jpg" 
  alt="Description"
  srcset="image-1x.jpg 1x, image-2x.jpg 2x"
  sizes="(max-width: 600px) 100vw, 50vw"
>

<!-- Responsive video -->
<video controls>
  <source src="video.mp4" type="video/mp4">
  <source src="video.webm" type="video/webm">
</video>
```

## Best Practices

### 1. Choose the Right Element

```html
<!-- ✅ Good: Using semantic elements -->
<nav>Navigation</nav>
<main>Main content</main>
<footer>Footer</footer>

<!-- ❌ Bad: Generic divs -->
<div>Navigation</div>
<div>Main content</div>
<div>Footer</div>
```

### 2. Maintain Hierarchy

```html
<!-- ✅ Good: Proper heading hierarchy -->
<h1>Main Title</h1>
  <h2>Section</h2>
    <h3>Subsection</h3>
      <h4>Sub-subsection</h4>

<!-- ❌ Bad: Skipping levels -->
<h1>Main Title</h1>
  <h3>Section</h3> <!-- Should be h2 -->
```

### 3. Use Native Features

```html
<!-- Use native validation -->
<input type="email" required>

<!-- Use native controls -->
<video controls src="video.mp4"></video>

<!-- Use native behavior -->
<details>
  <summary>Click to expand</summary>
</details>
```

### 4. Keep Styles Separate

```html
<!-- HTML for structure -->
<h1 class="heading-primary">Title</h1>

/* CSS for appearance */
.heading-primary {
  font-size: 2rem;
  color: blue;
}
```

## SEO Benefits

Semantic HTML improves search engine understanding:

```html
<article itemscope itemtype="http://schema.org/Article">
  <h1 itemprop="headline">Article Title</h1>
  <p itemprop="description">Article description</p>
  <meta itemprop="datePublished" content="2024-01-15">
</article>
```

## Modern HTML5 Features

### Custom Elements

```html
<!-- Web Components -->
<my-component data-name="value">
  Content
</my-component>
```

### Template Element

```html
<template id="list-item">
  <li>
    <span class="title"></span>
    <span class="description"></span>
  </li>
</template>

<script>
const template = document.getElementById('list-item');
const clone = template.content.cloneNode(true);
clone.querySelector('.title').textContent = 'Item';
</script>
```

## Browser Support

All modern browsers support HTML5 semantic elements. Use polyfills for older browsers if needed.

## Resources

- [MDN HTML Elements Reference](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)
- [HTML Standard](https://html.spec.whatwg.org/)
- [Semantic HTML - web.dev](https://web.dev/semantic-html/)
- [Accessibility Guidelines](Accessibility/README.md)

## Next Steps

- [Accessibility Guide](Accessibility/README.md) - Semantic HTML for accessibility
- [Components](Components/README.md) - Building semantic components
