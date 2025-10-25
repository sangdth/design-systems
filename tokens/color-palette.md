# Color Palette

A well-structured color palette is fundamental to any design system. Colors communicate meaning, create hierarchy, and establish brand identity.

## Color Theory Basics

### Hue

The color itself (red, blue, yellow).

### Saturation

Intensity or purity of the color (vibrant vs dull).

### Lightness

Brightness of the color (light vs dark).

## Color Systems

### HSB (Hue, Saturation, Brightness)

Most intuitive for designers.

### HSL (Hue, Saturation, Lightness)

Great for web development, easy to manipulate programmatically.

### RGB

What screens display, commonly used in code.

### LAB

Perceptually uniform, best for color distance calculations.

## Palette Structure

### Primary Colors

Main brand colors that define identity.

```typescript
const primary = {
  50: '#e3f2fd',
  100: '#bbdefb',
  200: '#90caf9',
  300: '#64b5f6',
  400: '#42a5f5',
  500: '#2196f3', // Base
  600: '#1e88e5',
  700: '#1976d2',
  800: '#1565c0',
  900: '#0d47a1',
};
```

### Neutral/Grayscale

Foundation for text, backgrounds, and borders.

```typescript
const neutral = {
  50: '#fafafa',
  100: '#f5f5f5',
  200: '#eeeeee',
  300: '#e0e0e0',
  400: '#bdbdbd',
  500: '#9e9e9e',
  600: '#757575',
  700: '#616161',
  800: '#424242',
  900: '#212121',
};
```

### Semantic Colors

Meaning-based colors for feedback and states.

```typescript
const semantic = {
  success: '#4caf50',
  warning: '#ff9800',
  error: '#f44336',
  info: '#2196f3',
};
```

## Accessibility

### Contrast Ratios

WCAG requirements:

- **AA Normal**: 4.5:1 for text
- **AA Large**: 3:1 for large text (18pt+ or 14pt+ bold)
- **AAA Normal**: 7:1 for text
- **AAA Large**: 4.5:1 for large text

### Checking Contrast

Use tools like [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/) or browser DevTools.

### Color Blindness

Ensure information isn't conveyed by color alone:

```tsx
// ❌ Bad: Relies only on color
<span style={{ color: 'red' }}>Error</span>

// ✅ Good: Uses color + icon
<span style={{ color: 'red' }}>
  <Icon name="error" /> Error
</span>
```

## Color Naming Strategies

### 1. Semantic Naming

```typescript
const colors = {
  background: {
    primary: '#ffffff',
    secondary: '#f5f5f5',
  },
  text: {
    primary: '#212121',
    secondary: '#757575',
  },
};
```

### 2. Primitive Naming

```typescript
const colors = {
  blue: {
    50: '#e3f2fd',
    500: '#2196f3',
    900: '#0d47a1',
  },
};
```

### 3. Hybrid Approach (Recommended)

```typescript
// Primitives
const primitive = {
  blue500: '#2196f3',
  gray900: '#212121',
};

// Semantics
const semantic = {
  primary: primitive.blue500,
  textPrimary: primitive.gray900,
};
```

## Implementation

### CSS Variables

```css
:root {
  --color-primary: #2196f3;
  --color-primary-hover: #1976d2;
  --color-background: #ffffff;
  --color-text-primary: #212121;
}

.button-primary {
  background-color: var(--color-primary);
}

.button-primary:hover {
  background-color: var(--color-primary-hover);
}
```

### TypeScript/Object

```typescript
export const colors = {
  primary: {
    DEFAULT: '#2196f3',
    hover: '#1976d2',
    light: '#e3f2fd',
  },
  background: {
    primary: '#ffffff',
    secondary: '#f5f5f5',
  },
};
```

## Generating Palettes

### Online Tools

- [Coolors](https://coolors.co/) - Color palette generator
- [Adobe Color](https://color.adobe.com/) - Color wheel and themes
- [Huemint](https://huemint.com/) - AI-powered color palettes

### Using Design Tools

Tools like Figma and Sketch have plugins for generating color scales.

### Programmatically

Generate scales using color theory formulas:

```typescript
function generateScale(baseColor: string): string[] {
  // Generate tints and shades
  // Return array of colors from lightest to darkest
}
```

## Dark Mode

Support dark mode with separate color tokens:

```typescript
export const lightTheme = {
  background: {
    primary: '#ffffff',
    secondary: '#f5f5f5',
  },
  text: {
    primary: '#212121',
    secondary: '#757575',
  },
};

export const darkTheme = {
  background: {
    primary: '#121212',
    secondary: '#1e1e1e',
  },
  text: {
    primary: '#ffffff',
    secondary: '#b0b0b0',
  },
};
```

## Resources

- [Coolors.co](https://coolors.co/) - Color palette generator
- [Contrast Checker](https://coolors.co/contrast-checker/112a46-acc8e5) - Check color contrast
- [Color Blindness Simulation](https://css-tricks.com/accessibility-basics-testing-your-page-for-color-blindness/) - Test color accessibility
- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/) - Official contrast checker

## Next Steps

- [Typography](Typography.md) - Text and fonts
- [Spacing](spacing.md) - Spacing scales
- [Sizing](sizing.md) - Size tokens
- [Accessibility: Colors](Accessibility/2.%20Colors.md) - Color accessibility
