# Fonts

Typography is a fundamental part of design systems. Choosing and implementing fonts correctly impacts performance, accessibility, and user experience.

## Font Format Support

### WOFF (Web Open Font Format)

WOFF should be a first-class citizen in your font strategy.

There are three main benefits to using WOFF:

1. **Compression**: The font data is compressed, so sites using WOFF will use less bandwidth and will load faster than if they used equivalent uncompressed TrueType or OpenType files.
2. **Availability**: Many font vendors that are unwilling to license their TrueType or OpenType format fonts for use on the web will license WOFF format fonts. This improves availability of fonts to site designers.
3. **Interoperability**: Both proprietary and free software browser vendors like the WOFF format, so it has the potential of becoming a truly universal, interoperable font format for the web, unlike other current font formats.

### Font Format Hierarchy

```css
@font-face {
  font-family: 'MyFont';
  src: url('myfont.woff2') format('woff2'), /* Best compression, modern browsers */
       url('myfont.woff') format('woff'),   /* Good compression, older browsers */
       url('myfont.ttf') format('truetype'); /* Fallback */
  font-weight: normal;
  font-style: normal;
}
```

## Font Loading Strategies

### @font-face Declaration

```css
@font-face {
  font-family: 'Inter';
  src: url('/fonts/inter-regular.woff2') format('woff2'),
       url('/fonts/inter-regular.woff') format('woff');
  font-weight: 400;
  font-style: normal;
  font-display: swap;
}

@font-face {
  font-family: 'Inter';
  src: url('/fonts/inter-bold.woff2') format('woff2');
  font-weight: 700;
  font-style: normal;
  font-display: swap;
}
```

### Font Display Property

Controls how fonts are rendered during the loading phase:

```css
/* swap: Show fallback immediately, swap when font loads */
font-display: swap;

/* optional: Only show if already cached */
font-display: optional;

/* block: Hide text until font loads (not recommended) */
font-display: block;

/* fallback: Short block, then swap */
font-display: fallback;
```

**Recommendation**: Use `swap` for body text, `optional` for decorative fonts.

## FOIT vs FOUT

### FOIT (Flash of Invisible Text)

Text is invisible until custom font loads. Poor UX.

```css
font-display: block; /* Don't use */
```

### FOUT (Flash of Unstyled Text)

Text renders in fallback font, swaps to custom font when loaded. Better UX.

```css
font-display: swap; /* Recommended */
```

## Font Loading Optimization

### Preload Critical Fonts

```html
<head>
  <link rel="preload" href="/fonts/inter-regular.woff2" as="font" type="font/woff2" crossorigin>
  <link rel="preload" href="/fonts/inter-bold.woff2" as="font" type="font/woff2" crossorigin>
</head>
```

### CSS Font Loading API

```javascript
const font = new FontFace('MyFont', 'url(/fonts/myfont.woff2)');

font.load().then((loadedFont) => {
  document.fonts.add(loadedFont);
  document.body.classList.add('fonts-loaded');
});
```

### Font Loading With React

```tsx
function useFonts(fonts: string[]) {
  const [fontsLoaded, setFontsLoaded] = useState(false);
  
  useEffect(() => {
    Promise.all(
      fonts.map(font => document.fonts.load(font))
    ).then(() => setFontsLoaded(true));
  }, [fonts]);
  
  return fontsLoaded;
}
```

## System Fonts

### Benefits

- Fast (already installed)
- Familiar to users
- No loading time
- No layout shifts

### Font Stack

```css
font-family: 
  -apple-system,           /* macOS */
  BlinkMacSystemFont,      /* Safari */
  "Segoe UI",              /* Windows */
  Roboto,                  /* Android */
  "Helvetica Neue",
  Arial,
  sans-serif;
```

### Using System Fonts

```css
body {
  font-family: system-ui, -apple-system, sans-serif;
}
```

## Variable Fonts

Single font file with multiple axes (weight, width, slant, etc.).

```css
@font-face {
  font-family: 'Inter';
  src: url('inter-variable.woff2') format('woff2');
  font-weight: 100 900;
  font-style: oblique 0deg 10deg;
}
```

### Usage

```css
.inter-light {
  font-family: 'Inter';
  font-weight: 300;
}

.inter-bold {
  font-family: 'Inter';
  font-weight: 700;
}

.inter-oblique {
  font-family: 'Inter';
  font-style: oblique 8deg;
}
```

**Benefits**:
- Smaller file size (one file instead of multiple)
- Smooth weight transitions
- More design flexibility

## Font Subsetting

Reduce font file size by including only needed characters.

### Google Fonts Subsetting

```html
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&text=ABCDEFGHIJKLMNOPQRSTUVWXYZ" rel="stylesheet">
```

### Manual Subsetting

Use tools like `pyftsubset` or online services to create subset fonts.

## Font Pairing

Choose complementary fonts for headings and body text.

### Common Pairings

```css
/* Pairing 1: Sans-serif + Sans-serif */
.heading-font {
  font-family: 'Inter', sans-serif;
  font-weight: 700;
}

.body-font {
  font-family: 'Inter', sans-serif;
  font-weight: 400;
}

/* Pairing 2: Serif + Sans-serif */
.heading-font {
  font-family: 'Merriweather', serif;
}

.body-font {
  font-family: 'Inter', sans-serif;
}

/* Pairing 3: Display + Sans-serif */
.heading-font {
  font-family: 'Playfair Display', serif;
}

.body-font {
  font-family: 'Source Sans Pro', sans-serif;
}
```

## Performance Considerations

### File Size

- Keep total font size under 100KB if possible
- Use font subsetting to reduce size
- Consider variable fonts

### Request Optimization

```html
<!-- ✅ Good: Preconnect to font CDN -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>

<!-- Load only what you need -->
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap" rel="stylesheet">
```

### Critical Fonts

Load only essential font weights initially:

```css
/* Initial load: Only regular and bold */
@font-face {
  font-family: 'Inter';
  src: url('inter-regular.woff2');
  font-weight: 400;
}

@font-face {
  font-family: 'Inter';
  src: url('inter-bold.woff2');
  font-weight: 700;
}

/* Load other weights asynchronously */
```

## Font Implementation

### Typography Scale

```css
:root {
  font-size: 16px;
  line-height: 1.5;
}

.h1 {
  font-size: 3rem;    /* 48px */
  line-height: 1.2;
  font-weight: 700;
}

.h2 {
  font-size: 2.25rem; /* 36px */
  line-height: 1.3;
  font-weight: 700;
}

.h3 {
  font-size: 1.875rem; /* 30px */
  line-height: 1.4;
  font-weight: 600;
}

/* ... smaller sizes */
```

### Responsive Typography

```css
h1 {
  font-size: clamp(2rem, 5vw, 3rem);
}

p {
  font-size: clamp(1rem, 2.5vw, 1.125rem);
}
```

## Accessibility

### Readability

- Minimum font size: 16px for body text
- Line height: 1.5 for body, 1.2-1.3 for headings
- Line length: 45-75 characters optimal
- Letter spacing: -0.01em to 0.03em for most fonts

### Contrast

Ensure text meets WCAG contrast requirements (4.5:1 for normal text, 3:1 for large text).

## Best Practices

1. **Preload critical fonts** - Reduce layout shifts
2. **Use font-display: swap** - Better UX than blocking
3. **Subset fonts** - Include only needed characters
4. **Preconnect to CDNs** - Reduce connection time
5. **Limit font weights** - 2-3 weights are usually enough
6. **Test loading performance** - Monitor FCP, LCP
7. **Provide fallbacks** - System fonts in stack

## Resources

- [Google Fonts](https://fonts.google.com/)
- [Variable Fonts](https://v-fonts.com/)
- [Font Pairing Tool](https://www.fontpair.co/)
- [Can I Use - Font Display](https://caniuse.com/css-font-rendering-controls)

## Next Steps

- [Typography](Typography.md) - Complete typography guide
- [Assets Overview](README.md) - Asset management
- [Performance](performance.md) - Loading performance