# Typography

Typography is the art and technique of arranging type to make written language legible, readable, and appealing. A well-designed typography system creates visual hierarchy and improves content comprehension.

## System Fonts

System fonts provide the fastest loading experience and familiar appearance across platforms.

### Modern Font Stack

```css
/* Recommended modern stack */
body {
  font-family: 
    /* macOS & iOS */
    -apple-system,
    BlinkMacSystemFont,
    /* Windows */
    "Segoe UI",
    /* Android */
    Roboto,
    /* Linux */
    Ubuntu,
    /* Generic fallbacks */
    Cantarell,
    "Helvetica Neue",
    sans-serif;
}
```

### CSS Generic Font Stack

Using the generic `system-ui`:

```css
body {
  font-family: system-ui, sans-serif;
}
```

### Resources

- [CSS Fonts Module Level 3](https://drafts.csswg.org/css-fonts-3/)
- [System Font Stack Guide](https://www.ctrl.blog/entry/font-stack-text.html)
- [Implementing System Fonts - Booking.com](https://booking.design/implementing-system-fonts-on-booking-com-a-lesson-learned-bdc984df627f)

## Typography Scale

### Type Scale

Choose a type scale for consistent sizing:

```css
:root {
  /* Major Third (1.25) */
  --font-size-xs: 0.64rem;   /* 10.24px */
  --font-size-sm: 0.8rem;    /* 12.8px */
  --font-size-base: 1rem;    /* 16px */
  --font-size-md: 1.25rem;   /* 20px */
  --font-size-lg: 1.563rem;  /* 25px */
  --font-size-xl: 1.953rem;  /* 31.25px */
  --font-size-2xl: 2.441rem; /* 39.06px */
  --font-size-3xl: 3.052rem; /* 48.83px */
}
```

### Type Scale Implementation

```tsx
const typography = {
  h1: {
    fontSize: '3.052rem',
    lineHeight: 1.2,
    fontWeight: 700,
  },
  h2: {
    fontSize: '2.441rem',
    lineHeight: 1.3,
    fontWeight: 700,
  },
  h3: {
    fontSize: '1.953rem',
    lineHeight: 1.4,
    fontWeight: 600,
  },
  h4: {
    fontSize: '1.563rem',
    lineHeight: 1.4,
    fontWeight: 600,
  },
  body: {
    fontSize: '1rem',
    lineHeight: 1.5,
    fontWeight: 400,
  },
  small: {
    fontSize: '0.8rem',
    lineHeight: 1.5,
    fontWeight: 400,
  },
};
```

## Font Loading Strategies

See [Assets/Fonts.md](Assets/Fonts.md) for comprehensive font loading strategies.

### Quick Reference

```html
<!-- Preload critical fonts -->
<link rel="preload" href="/fonts/inter-regular.woff2" as="font" type="font/woff2" crossorigin>

<!-- Load fonts with fallback -->
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap" rel="stylesheet">
```

## Type Pairing

### Choosing Typefaces

**Heading + Body Combination:**

```css
/* Pair 1: Sans-serif + Sans-serif */
.heading {
  font-family: 'Inter', sans-serif;
  font-weight: 700;
}

.body {
  font-family: 'Inter', sans-serif;
  font-weight: 400;
}

/* Pair 2: Serif + Sans-serif */
.heading {
  font-family: 'Merriweather', serif;
}

.body {
  font-family: 'Inter', sans-serif;
}

/* Pair 3: Display + Sans-serif */
.heading {
  font-family: 'Playfair Display', serif;
}

.body {
  font-family: 'Source Sans Pro', sans-serif;
}
```

### Testing Pairings

```tsx
function TypePairing({ headingFont, bodyFont }) {
  return (
    <div>
      <h1 style={{ fontFamily: headingFont }}>
        Heading Font
      </h1>
      <p style={{ fontFamily: bodyFont }}>
        Body font should be readable at all sizes and complement the heading font.
      </p>
    </div>
  );
}
```

## Responsive Typography

### Fluid Typography

```css
/* Scales smoothly between breakpoints */
h1 {
  font-size: clamp(2rem, 5vw, 3rem);
}

p {
  font-size: clamp(1rem, 2.5vw, 1.125rem);
}
```

### Breakpoint-Based Typography

```css
h1 {
  font-size: 2rem;
}

@media (min-width: 768px) {
  h1 {
    font-size: 2.5rem;
  }
}

@media (min-width: 1024px) {
  h1 {
    font-size: 3rem;
  }
}
```

## Accessibility

### Line Length

Optimal reading line length: 45-75 characters

```css
p {
  max-width: 65ch; /* Approximately 65 characters */
}
```

### Line Height

```css
body {
  line-height: 1.5; /* Comfortable for body text */
}

h1, h2, h3 {
  line-height: 1.2; /* Tighter for headings */
}
```

### Font Size

Minimum readable size: 16px for body text

```css
body {
  font-size: 16px; /* Base size */
}

.small-text {
  font-size: 14px; /* Minimum for secondary text */
  /* Never go below 14px for readable text */
}
```

## Best Practices

### 1. Limit Font Families

Use 2-3 font families maximum.

### 2. Use Font Weights Wisely

```css
/* Don't use too many weights */
font-weight: 400; /* Normal */
font-weight: 600; /* Semibold for emphasis */
font-weight: 700; /* Bold for headings */
```

### 3. Consistent Line Heights

```css
/* Body text */
line-height: 1.5;

/* Headings */
line-height: 1.2;
```

### 4. Appropriate Letter Spacing

```css
/* Headings (tighter) */
h1, h2 {
  letter-spacing: -0.02em;
}

/* Body (normal) */
body {
  letter-spacing: 0;
}

/* ALL CAPS (wider) */
.caps {
  letter-spacing: 0.1em;
  text-transform: uppercase;
}
```

## Resources

- [Font Loading Best Practices](Assets/Fonts.md)
- [System Fonts Guide](https://www.ctrl.blog/entry/font-stack-text.html)
- [CSS Font Stack](https://www.cssfontstack.com/)

## Next Steps

- [Assets Guide](Assets/README.md) - Asset management
- [Style Guide](Style%20compositions.md) - Typography in components
