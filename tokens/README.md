# Design Tokens

Design tokens are the atomic design decisions that form the foundation of a design system. They are named values that store design attributes like colors, spacing, typography, and more.

## What Are Design Tokens?

Think of design tokens as the building blocks of your design system. Instead of hardcoding values like `#0066cc` or `16px`, you use meaningful names like `color.primary` or `spacing.medium`. This creates a single source of truth for design decisions.

```typescript
// ❌ Without tokens (hardcoded values)
const Button = styled.button`
  background-color: #0066cc;
  padding: 8px 16px;
  font-size: 14px;
`;

// ✅ With tokens (semantic names)
const Button = styled.button`
  background-color: ${tokens.color.primary};
  padding: ${tokens.spacing.sm} ${tokens.spacing.md};
  font-size: ${tokens.fontSize.sm};
`;
```

## Benefits

### 1. Consistency

Ensures the same values are used across all components and platforms.

### 2. Maintainability

Update values in one place, and changes propagate everywhere.

### 3. Theming

Enable dark mode, brand variations, and accessibility adjustments with token swaps.

### 4. Cross-Platform

Share tokens across web, mobile, and other platforms.

## Token Categories

### Color Tokens

Visual identity and semantic meaning.

- **Primitive**: Raw color values (`blue500`, `gray300`)
- **Semantic**: Meaning-based (`primary`, `success`, `error`)
- **Component**: Component-specific (`buttonBackground`, `inputBorder`)

### Typography Tokens

Font families, sizes, weights, and spacing.

- Font families
- Font sizes
- Font weights
- Line heights
- Letter spacing

### Spacing Tokens

Consistent spacing rhythm.

- Base scale (4px, 8px, 16px, etc.)
- Named tokens (`xs`, `sm`, `md`, `lg`)
- Component spacing

### Sizing Tokens

Dimensions and measurements.

- Component sizes: `container`
- Icon sizes
- Border radius: `xs`, `sm`, `md`, `lg`, `xl`, `full`
- Shadows: `xs`, `sm`, `md`, `lg`, `xl`, `2xl`

## Token Hierarchy

```text
Theme
├── Color
│   ├── Primitive (blue500, red300)
│   └── Semantic (primary, error)
├── Spacing
│   └── Scale (xs, sm, md, lg)
├── Typography
│   ├── Font Family
│   ├── Font Size
│   └── Font Weight
└── Elevation
    └── Shadows
```

## Implementation Approaches

### 1. CSS Variables

```css
:root {
  --color-primary: #0066cc;
  --spacing-md: 16px;
}
```

### 2. JavaScript/TypeScript Objects

```typescript
export const tokens = {
  color: {
    primary: '#0066cc',
    secondary: '#6c757d',
  },
  spacing: {
    xs: '4px',
    sm: '8px',
    md: '16px',
  },
};
```

### 3. JSON/YAML for Cross-Platform

```json
{
  "color": {
    "primary": {
      "value": "#0066cc"
    }
  }
}
```

## Token Naming Conventions

### Good Names

- `color.primary`
- `spacing.md`
- `fontSize.lg`
- `shadow.medium`

### Bad Names

- `blue` (not semantic)
- `size16` (not descriptive)
- `big` (not specific)
- `thing` (meaningless)

## Related Topics

- [Color Palette](color-palette.md) - Complete color system
- [Spacing](spacing.md) - Spacing scales and rhythm
- [Sizing](sizing.md) - Size tokens and responsive sizing
- [Component Generator](component-generator.md) - Token consumption patterns
