# Sizing

Size tokens define dimensions, heights, widths, and other measurements in your design system. They work with spacing tokens to create consistent, scalable components.

## Types of Sizes

### Component Sizes

Defining standard sizes for common components like buttons, inputs, and avatars.

```typescript
const componentSizes = {
  xs: { height: '24px', fontSize: '12px' },
  sm: { height: '32px', fontSize: '14px' },
  md: { height: '40px', fontSize: '16px' },
  lg: { height: '48px', fontSize: '18px' },
  xl: { height: '56px', fontSize: '20px' },
};
```

### Icon Sizes

Standardized icon dimensions.

```typescript
const iconSizes = {
  xs: '12px',
  sm: '16px',
  md: '20px',
  lg: '24px',
  xl: '32px',
  '2xl': '48px',
};
```

### Border Radius

Corner rounding for elements.

```typescript
const borderRadius = {
  none: '0',
  sm: '4px',
  md: '8px',
  lg: '12px',
  xl: '16px',
  full: '9999px',
};
```

### Shadows (Elevation)

Depth and elevation for layering.

```typescript
const shadows = {
  xs: '0 1px 2px rgba(0, 0, 0, 0.05)',
  sm: '0 1px 3px rgba(0, 0, 0, 0.1)',
  md: '0 4px 6px rgba(0, 0, 0, 0.1)',
  lg: '0 10px 15px rgba(0, 0, 0, 0.1)',
  xl: '0 20px 25px rgba(0, 0, 0, 0.1)',
};
```

## Implementation

### CSS Variables

```css
:root {
  --size-button-sm: 32px;
  --size-button-md: 40px;
  --size-button-lg: 48px;
  
  --size-icon-sm: 16px;
  --size-icon-md: 24px;
  --size-icon-lg: 32px;
  
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 12px;
}
```

### TypeScript Objects

```typescript
export const sizes = {
  button: {
    sm: { height: '32px', minWidth: '80px' },
    md: { height: '40px', minWidth: '100px' },
    lg: { height: '48px', minWidth: '120px' },
  },
  icon: {
    sm: '16px',
    md: '24px',
    lg: '32px',
  },
  avatar: {
    sm: '32px',
    md: '48px',
    lg: '64px',
    xl: '96px',
  },
};
```

## Using Sizes in Components

### Button Example

```tsx
interface ButtonProps {
  size?: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
}

function Button({ size = 'md', children }: ButtonProps) {
  const sizeStyles = {
    sm: { height: '32px', fontSize: '14px', padding: '4px 12px' },
    md: { height: '40px', fontSize: '16px', padding: '8px 16px' },
    lg: { height: '48px', fontSize: '18px', padding: '12px 24px' },
  };
  
  return (
    <button style={sizeStyles[size]}>
      {children}
    </button>
  );
}
```

### Icon Example

```tsx
interface IconProps {
  size?: keyof typeof iconSizes;
  name: string;
}

function Icon({ size = 'md', name }: IconProps) {
  return (
    <svg 
      width={iconSizes[size]} 
      height={iconSizes[size]}
      aria-hidden="true"
    >
      {/* Icon content */}
    </svg>
  );
}
```

## Responsive Sizing

Adapt component sizes based on viewport:

```typescript
const responsiveSizes = {
  button: {
    mobile: { height: '32px', fontSize: '14px' },
    tablet: { height: '40px', fontSize: '16px' },
    desktop: { height: '48px', fontSize: '18px' },
  },
  container: {
    mobile: '100%',
    tablet: '720px',
    desktop: '1200px',
  },
};
```

## Aspect Ratios

Maintain consistent proportions:

```typescript
const aspectRatios = {
  square: '1 / 1',
  landscape: '16 / 9',
  portrait: '3 / 4',
  video: '4 / 3',
};
```

## Z-Index Scale

Layer management for overlays and stacking:

```typescript
const zIndex = {
  base: 0,
  dropdown: 1000,
  sticky: 1010,
  fixed: 1020,
  modal: 1030,
  popover: 1040,
  tooltip: 1050,
};
```

## Constraints and Boundaries

### Min/Max Widths

```typescript
const constraints = {
  textContainer: {
    minWidth: '320px',
    maxWidth: '720px',
  },
  card: {
    minWidth: '200px',
    maxWidth: '400px',
  },
};
```

### Line Heights

```typescript
const lineHeight = {
  tight: 1.25,
  normal: 1.5,
  relaxed: 1.75,
  loose: 2,
};
```

## Best Practices

1. **Use relative units**: Prefer `rem` or `em` for text-related sizing
2. **Be consistent**: Reuse size tokens across components
3. **Document exceptions**: When you need non-standard sizes, note why
4. **Consider accessibility**: Ensure minimum touch targets (44x44px)
5. **Plan for scaling**: Make sizes responsive when appropriate

## Common Size Patterns

### Input Fields

```typescript
const inputSizes = {
  sm: { height: '32px', fontSize: '14px' },
  md: { height: '40px', fontSize: '16px' },
  lg: { height: '48px', fontSize: '16px' },
};
```

### Tables

```typescript
const tableSizes = {
  cellPadding: '12px 16px',
  rowHeight: '48px',
  headerHeight: '56px',
};
```

### Containers

```typescript
const containerSizes = {
  xs: '480px',
  sm: '640px',
  md: '768px',
  lg: '1024px',
  xl: '1280px',
};
```

## Next Steps

- [Spacing](spacing.md) - Spacing scales and rhythm
- [Component Generator](component-generator.md) - Token consumption patterns
- [Components](Components/README.md) - Using sizes in components
