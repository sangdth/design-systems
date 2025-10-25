# Icons

Icons are visual symbols that communicate meaning quickly and efficiently. A well-designed icon system enhances usability and creates visual consistency.

## Icon Implementation Strategies

### Inline SVG (Recommended)

Best for customization, performance, and accessibility.

```tsx
function Icon({ name, size = 24, color }: IconProps) {
  const iconPaths = {
    home: <path d="M10 20v-6h4v6h5v-8h3L12 3 2 12h3v8z" />,
    search: <path d="M15.5 14h-.79l-.28-.27C15.41 12.59 16 11.11 16 9.5 16 5.91 13.09 3 9.5 3S3 5.91 3 9.5 5.91 16 9.5 16c1.61 0 3.09-.59 4.23-1.57l.27.28v.79l5 4.99L20.49 19l-4.99-5zm-6 0C7.01 14 5 11.99 5 9.5S7.01 5 9.5 5 14 7.01 14 9.5 11.99 14 9.5 14z" />,
  };
  
  return (
    <svg
      width={size}
      height={size}
      viewBox="0 0 24 24"
      fill="currentColor"
      style={{ color }}
      aria-hidden="true"
    >
      {iconPaths[name]}
    </svg>
  );
}
```

### Icon Fonts

Old approach, not recommended for modern apps.

```html
<!-- Icon fonts like Font Awesome -->
<i class="fa fa-home"></i>
```

**Drawbacks**:

- Large file size
- Flash of unstyled text
- Limited customization
- Accessibility issues

### SVG Sprite

Single SVG file with multiple icons referenced.

```html
<svg>
  <symbol id="icon-home" viewBox="0 0 24 24">
    <path d="..."/>
  </symbol>
  <symbol id="icon-search" viewBox="0 0 24 24">
    <path d="..."/>
  </symbol>
</svg>

<!-- Usage -->
<svg>
  <use href="#icon-home" />
</svg>
```

## Accessible Icons

### Decorative Icons

Icons that don't convey meaning:

```tsx
<button>
  <Icon name="close" aria-hidden="true" />
  Close
</button>
```

### Semantic Icons

Icons that convey meaning:

```tsx
// With visible text
<button aria-label="Close dialog">
  <Icon name="close" />
</button>

// Icon only
<IconButton icon={<HomeIcon />} aria-label="Home" />

// Status icon
<span aria-label="Success">
  <Icon name="check" />
</span>
```

### Animated Icons

Show state changes:

```tsx
function AnimatedIcon({ isExpanded }: { isExpanded: boolean }) {
  return (
    <motion.svg
      animate={{ rotate: isExpanded ? 180 : 0 }}
      transition={{ duration: 0.2 }}
    >
      <path d="..." />
    </motion.svg>
  );
}
```

## Icon Sizes

### Standard Sizes

```typescript
const iconSizes = {
  xs: 12,
  sm: 16,
  md: 20,
  lg: 24,
  xl: 32,
  '2xl': 48,
};
```

### Implementation

```tsx
interface IconProps {
  name: string;
  size?: keyof typeof iconSizes;
  color?: string;
}

function Icon({ name, size = 'md', color }: IconProps) {
  return (
    <svg
      width={iconSizes[size]}
      height={iconSizes[size]}
      viewBox="0 0 24 24"
      fill="currentColor"
      style={{ color }}
    >
      {/* Icon paths */}
    </svg>
  );
}
```

## Icon Systems

### Complete Icon Component

```tsx
import { SVGProps } from 'react';

interface IconProps extends Omit<SVGProps<SVGSVGElement>, 'children'> {
  name: string;
  size?: number | string;
}

const icons = {
  home: (
    <path d="M10 20v-6h4v6h5v-8h3L12 3 2 12h3v8z" />
  ),
  search: (
    <path d="M15.5 14h-.79l-.28-.27C15.41 12.59 16 11.11 16 9.5 16 5.91 13.09 3 9.5 3S3 5.91 3 9.5 5.91 16 9.5 16c1.61 0 3.09-.59 4.23-1.57l.27.28v.79l5 4.99L20.49 19l-4.99-5zm-6 0C7.01 14 5 11.99 5 9.5S7.01 5 9.5 5 14 7.01 14 9.5 11.99 14 9.5 14z" />
  ),
  // ... more icons
};

export function Icon({
  name,
  size = 24,
  color = 'currentColor',
  className,
  ...props
}: IconProps) {
  const iconPath = icons[name];
  
  if (!iconPath) {
    console.warn(`Icon "${name}" not found`);
    return null;
  }
  
  return (
    <svg
      width={size}
      height={size}
      viewBox="0 0 24 24"
      fill="none"
      className={className}
      {...props}
    >
      {iconPath}
    </svg>
  );
}
```

## Icon Variants

### Colored Icons

```tsx
<Icon name="check" className="text-green-500" />
```

### Outlined vs Filled

```tsx
// Use different icon variants
<Icon name="star" variant="outline" />
<Icon name="star" variant="filled" />
```

### Size Variants

```tsx
<Icon name="user" size="sm" />
<Icon name="user" size="lg" />
```

## SVG Optimization

### Best Practices

1. **Remove unnecessary attributes**
   - Remove `xmlns`, `version` attributes
   - Remove metadata

2. **Optimize paths**
   - Use tools like SVGO
   - Round coordinates

3. **Remove fills**
   - Let CSS control color

4. **Unify viewBox**
   - Use consistent viewBox (0 0 24 24)

### SVGO Example

```bash
# Install SVGO
npm install -g svgo

# Optimize SVG
svgo icons/*.svg --config svgo.config.js
```

### svgo.config.js

```javascript
module.exports = {
  plugins: [
    {
      name: 'preset-default',
      params: {
        overrides: {
          removeViewBox: false, // Keep viewBox
          removeUselessStrokeAndFill: false, // Keep fills/strokes
        },
      },
    },
  ],
};
```

## Icon Libraries

### Popular Choices

1. **Heroicons** - Tailwind CSS creators
2. **Lucide** - Fork of Feather icons
3. **Phosphor** - Flexible icon family
4. **Material Icons** - Google's icon set
5. **Font Awesome** - Comprehensive icon library

### Installing

```bash
# Heroicons
npm install @heroicons/react

# Lucide
npm install lucide-react
```

### Usage Example

```tsx
import { HomeIcon, SearchIcon } from '@heroicons/react/24/outline';

function MyComponent() {
  return (
    <div>
      <HomeIcon className="w-5 h-5" />
      <SearchIcon className="w-5 h-5" />
    </div>
  );
}
```

## Custom Icons

### SVG Workflow

1. Design in Figma/Sketch
2. Export as SVG
3. Optimize with SVGO
4. Extract path data
5. Add to icon system

### From Design Tools

```tsx
// Exported from Figma
const CustomIcon = () => (
  <svg viewBox="0 0 24 24" fill="none">
    <path
      d="M12 2L2 7v10l10 5 10-5V7L12 2z"
      stroke="currentColor"
      strokeWidth="2"
    />
  </svg>
);
```

## Icon Usage Patterns

### With Text

```tsx
<button>
  <Icon name="save" />
  Save
</button>
```

### Icon Only

```tsx
<IconButton icon={<Icon name="menu" />} aria-label="Menu" />
```

### Icon + Badge

```tsx
<div className="relative inline-block">
  <Icon name="notification" />
  <Badge position="top-right">3</Badge>
</div>
```

### Icon in Input

```tsx
<div className="relative">
  <Icon name="search" className="absolute left-3" />
  <input className="pl-10" />
</div>
```

## Best Practices

1. **Consistent style** - Use same style family
2. **Proper sizing** - Use standard sizes
3. **Accessibility** - Always provide labels for semantic icons
4. **Performance** - Inline SVG preferred over icon fonts
5. **Color** - Use `currentColor` for flexibility
6. **ViewBox** - Consistent viewBox for all icons
7. **Optimization** - Optimize SVG files

## Resources

- [Heroicons](https://heroicons.com/)
- [Lucide Icons](https://lucide.dev/)
- [SVGO](https://github.com/svg/svgo) - SVG optimizer
- [Iconify](https://iconify.design/) - Icon search

## Next Steps

- [Components](Components/README.md) - Icon usage in components
- [Assets Overview](README.md) - Asset management
- [Accessibility](Accessibility/README.md) - Icon accessibility
