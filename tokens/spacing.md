# Spacing

Consistent spacing creates visual rhythm and hierarchy in your design system. A well-defined spacing scale ensures components align properly and maintain visual consistency.

## Why Spacing Matters

### Visual Hierarchy

Spacing helps users understand relationships between elements.

### Breathing Room

Adequate spacing improves readability and reduces visual clutter.

### Consistency

A unified scale prevents arbitrary spacing decisions.

## Spacing Scales

### Common Base Values

Most design systems use a base unit of **4px** or **8px**.

#### 4px Base Scale

```typescript
const spacing = {
  0: '0',
  1: '4px',   // 0.25rem
  2: '8px',   // 0.5rem
  3: '12px',  // 0.75rem
  4: '16px',  // 1rem
  5: '20px',  // 1.25rem
  6: '24px',  // 1.5rem
  8: '32px',  // 2rem
  10: '40px', // 2.5rem
  12: '48px', // 3rem
  16: '64px', // 4rem
  20: '80px', // 5rem
};
```

#### 8px Base Scale

```typescript
const spacing = {
  0: '0',
  1: '8px',   // 0.5rem
  2: '16px',  // 1rem
  3: '24px',  // 1.5rem
  4: '32px',  // 2rem
  5: '40px',  // 2.5rem
  6: '48px',  // 3rem
  8: '64px',  // 4rem
  10: '80px', // 5rem
  12: '96px', // 6rem
  16: '128px',// 8rem
};
```

### Named Scale (Semantic)

```typescript
const spacing = {
  xs: '4px',
  sm: '8px',
  md: '16px',
  lg: '24px',
  xl: '32px',
  '2xl': '48px',
  '3xl': '64px',
  '4xl': '96px',
};
```

### Hybrid Approach (Recommended)

Combine numeric with semantic names:

```typescript
const spacing = {
  // Numeric scale
  0: '0',
  1: '4px',
  2: '8px',
  3: '12px',
  4: '16px',
  5: '20px',
  6: '24px',
  8: '32px',
  10: '40px',
  12: '48px',
  16: '64px',
  
  // Semantic aliases
  xs: '4px',
  sm: '8px',
  md: '16px',
  lg: '24px',
  xl: '32px',
  '2xl': '48px',
  '3xl': '64px',
};
```

## Spacing Patterns

### Margin

Space outside an element.

```tsx
<Box margin="md" padding="sm">
  Content
</Box>
```

### Padding

Space inside an element.

```tsx
<Box padding={{ top: 'lg', bottom: 'lg', x: 'md' }}>
  Content
</Box>
```

### Gap

Space between flex/grid children.

```tsx
<Stack gap="md">
  <Item />
  <Item />
  <Item />
</Stack>
```

## Responsive Spacing

Adapt spacing for different screen sizes:

```typescript
const spacing = {
  base: {
    sm: '8px',
    md: '16px',
    lg: '24px',
  },
  tablet: {
    sm: '12px',
    md: '24px',
    lg: '32px',
  },
  desktop: {
    sm: '16px',
    md: '32px',
    lg: '48px',
  },
};
```

## Using Spacing in Components

### CSS Variables

```css
:root {
  --spacing-xs: 4px;
  --spacing-sm: 8px;
  --spacing-md: 16px;
  --spacing-lg: 24px;
}

.card {
  padding: var(--spacing-md);
  margin-bottom: var(--spacing-lg);
}
```

### Styled Components

```tsx
const Button = styled.button<{ spacing?: string }>`
  padding: ${props => props.spacing || tokens.spacing.sm}
    ${props => props.spacing || tokens.spacing.md};
`;
```

### Tailwind-style Utilities

```tsx
function getSpacing(value: keyof typeof spacing) {
  return spacing[value];
}

// Usage
<div className={getSpacing('md')}>Content</div>
```

## Vertical Rhythm

Maintain consistent vertical spacing for text and elements:

```typescript
const verticalRhythm = {
  paragraph: '1em',        // Line height for paragraphs
  heading: '1.25em',       // Tighter spacing for headings
  component: '24px',       // Space between components
};
```

## Grid Systems

Use spacing tokens in grid layouts:

```tsx
<Grid gap="md" columns={12}>
  <Column span={6}>
    Content
  </Column>
  <Column span={6}>
    Content
  </Column>
</Grid>
```

## Common Patterns

### Card Padding

```typescript
const patterns = {
  cardPadding: 'md',      // 16px
  cardGap: 'lg',          // 24px
  containerPadding: 'md', // 16px mobile, more on desktop
};
```

### Form Spacing

```typescript
const formSpacing = {
  fieldGap: 'md',         // Space between form fields
  fieldLabelGap: 'xs',    // Space between label and input
  groupGap: 'lg',         // Space between form sections
};
```

## Testing Spacing

### Visual Inspection

- Use browser DevTools to inspect spacing
- Look for consistent gaps and alignment
- Check responsive behavior

### Automated Testing

```tsx
test('Button has correct padding', () => {
  const { container } = render(<Button>Click</Button>);
  const button = container.firstChild;
  expect(button).toHaveStyle({ padding: '8px 16px' });
});
```

## Best Practices

1. **Use the scale**: Avoid arbitrary values like `13px` or `37px`
2. **Be consistent**: Use the same spacing values for similar situations
3. **Document exceptions**: If you must break the scale, document why
4. **Consider context**: Dense UIs may need tighter spacing, spacious UIs need more room
5. **Test on devices**: Spacing perception varies by screen size

## Resources

- [Material Design Spacing](https://material.io/design/layout/spacing-methods.html)
- [8pt Grid System](https://spec.fm/specifics/8-pt-grid)

## Next Steps

- [Sizing](sizing.md) - Size tokens and dimensions
- [Components: Layout](Components/layout-components.md) - Using spacing in layouts
- [Typography](Typography.md) - Vertical rhythm and line spacing
