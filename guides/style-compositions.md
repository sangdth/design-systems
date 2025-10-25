# Style Compositions

Style composition is how we build up component styles from reusable pieces. Proper composition leads to maintainable, scalable styling.

## Core Principle

**Encapsulate**.

Components should style themselves, but not their layout. Layout is controlled by the parent.

Sometimes you can break this principle, but you should be fully aware of the trade-offs, well documented, and understand the implications.

### Principles

#### 1. Components Don't Lay Themselves Out

Components should not set their **position**, **size**, or **margin**. These should be decided by the parents.

```tsx
// ✅ Good: Component doesn't control layout
function Card({ children }: CardProps) {
  return (
    <div className="card">
      <div className="card-inner">{children}</div>
    </div>
  );
}

// Usage: Parent controls layout
<div className="grid" style={{ gap: '1rem' }}>
  <Card>Card 1</Card>
  <Card>Card 2</Card>
</div>

// ❌ Bad: Component controls its own spacing
function Card({ children }: CardProps) {
  return (
    <div className="card" style={{ margin: '1rem', maxWidth: '400px' }}>
      {children}
    </div>
  );
}
```

#### 2. Style Only Self and Direct Children

Components only style **itself** and only style the **direct children**. Avoid deep nesting selectors.

```tsx
// ✅ Good: Styles target self and direct children
function Card({ children }: CardProps) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

// CSS
.card {
  /* Self styling */
  background: white;
  border-radius: 8px;
  padding: 1rem;
}

.card > * {
  /* Direct children only */
  margin-bottom: 0.5rem;
}

// ❌ Bad: Deep nesting
.card .card-content .card-header {
  /* Too specific, hard to override */
}
```

## Composition Strategies

### CSS Modules

Scoped styles with explicit composition:

```tsx
// Card.module.css
.card {
  background: white;
  border-radius: 8px;
  padding: 1rem;
}

.header {
  font-size: 1.25rem;
  font-weight: bold;
}

// Card.tsx
import styles from './Card.module.css';

function Card({ title, children }: CardProps) {
  return (
    <div className={styles.card}>
      {title && <div className={styles.header}>{title}</div>}
      {children}
    </div>
  );
}

// Composition
import styles from './Card.module.css';
import { cn } from '../utils';

function CustomCard({ className, ...props }: CardProps) {
  return (
    <Card className={cn(styles.card, className)} {...props} />
  );
}
```

### CSS-in-JS (Styled Components)

Template literals with composition:

```tsx
import styled from 'styled-components';

const Card = styled.div`
  background: white;
  border-radius: 8px;
  padding: 1rem;
`;

const Header = styled.div`
  font-size: 1.25rem;
  font-weight: bold;
  margin-bottom: 0.5rem;
`;

// Composition
const CustomCard = styled(Card)`
  border: 2px solid blue;
  background: lightblue;
`;
```

### Utility-First (Tailwind)

Compose classes together:

```tsx
function Card({ className, children }: CardProps) {
  return (
    <div className={cn(
      'bg-white rounded-lg p-4 shadow',
      className
    )}>
      {children}
    </div>
  );
}

// Usage
<Card className="border-2 border-blue-500">
  Content
</Card>
```

### CSS Variables

Style composition through custom properties:

```css
/* Base component */
.card {
  --card-padding: 1rem;
  --card-border: 1px solid #e0e0e0;
  --card-radius: 8px;
  
  padding: var(--card-padding);
  border: var(--card-border);
  border-radius: var(--card-radius);
}

/* Variant */
.card[data-variant="large"] {
  --card-padding: 1.5rem;
  --card-radius: 12px;
}
```

```tsx
function Card({ variant = 'default', children }: CardProps) {
  return (
    <div className="card" data-variant={variant}>
      {children}
    </div>
  );
}
```

## Variant Patterns

### Class-Based Variants

```tsx
function Button({ variant = 'primary', ...props }: ButtonProps) {
  return (
    <button
      className={cn(
        'base-button',
        {
          'button-primary': variant === 'primary',
          'button-secondary': variant === 'secondary',
          'button-ghost': variant === 'ghost',
        }
      )}
      {...props}
    />
  );
}
```

### Data Attribute Variants

```tsx
function Button({ variant = 'primary', ...props }: ButtonProps) {
  return (
    <button
      className="button"
      data-variant={variant}
      {...props}
    />
  );
}

/* CSS */
.button[data-variant="primary"] {
  background: blue;
}

.button[data-variant="secondary"] {
  background: gray;
}
```

### Props-Based Variants

```tsx
function Button({ primary, secondary, ...props }: ButtonProps) {
  return (
    <button
      className={cn({
        'button-primary': primary,
        'button-secondary': secondary,
      })}
      {...props}
    />
  );
}
```

## Style Merging

### Class Name Utilities

```typescript
function cn(...args: (string | undefined | null | boolean)[]): string {
  return args
    .filter(Boolean)
    .join(' ')
    .trim();
}

// Usage
<div className={cn('base', condition && 'conditional', className)} />
```

### Style Objects

```tsx
function Box({ style, ...props }: BoxProps) {
  return (
    <div
      style={{
        padding: '1rem',
        ...style, // Merge with user styles
      }}
      {...props}
    />
  );
}
```

## Composition Best Practices

### 1. Single Responsibility

Each style concern should be separated:

```tsx
// Base styles
const ButtonBase = styled.button`
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 4px;
  cursor: pointer;
`;

// Variant styles
const PrimaryButton = styled(ButtonBase)`
  background: blue;
  color: white;
`;

// State styles
const DisabledButton = styled(ButtonBase)`
  opacity: 0.5;
  cursor: not-allowed;
`;
```

### 2. Composition Over Inheritance

```tsx
// ✅ Good: Compose styles
function Card({ elevated, outlined, ...props }: CardProps) {
  return (
    <div
      className={cn(
        'card',
        elevated && 'card-elevated',
        outlined && 'card-outlined'
      )}
      {...props}
    />
  );
}

// ❌ Bad: Monolithic styles
function Card({ variant }: CardProps) {
  return (
    <div className={`card card-${variant}`} />
  );
}
```

### 3. Maintain Boundaries

```tsx
// ✅ Good: Clear boundaries
<Card>
  <CardHeader />
  <CardBody />
  <CardFooter />
</Card>

// ❌ Bad: Breaking boundaries
<Card style={{ margin: '2rem' }}>
  <div>
    <div>
      <span className="custom-header">Content</span>
    </div>
  </div>
</Card>
```

## Advanced Patterns

### Polymorphic Styling

```tsx
interface BoxProps {
  as?: React.ElementType;
  padding?: string;
  margin?: string;
}

function Box({ as: Component = 'div', padding, margin, ...props }: BoxProps) {
  return (
    <Component
      style={{ padding, margin }}
      {...props}
    />
  );
}

// Usage
<Box as="section" padding="1rem" margin="2rem">
  Content
</Box>
```

### Theme-Aware Styles

```tsx
const Card = styled.div`
  background: ${props => props.theme.colors.surface};
  color: ${props => props.theme.colors.text};
  border: 1px solid ${props => props.theme.colors.border};
`;
```

### Responsive Styles

```tsx
const Container = styled.div`
  padding: 1rem;
  
  @media (min-width: 768px) {
    padding: 2rem;
  }
  
  @media (min-width: 1024px) {
    padding: 3rem;
  }
`;
```

## Common Pitfalls

### ❌ Over-Styling

```tsx
// Too many specific classes
<div className="card card-primary card-elevated card-rounded card-shadow card-padded" />
```

### ❌ Style Props That Should Be Layout

```tsx
// ❌ Bad: Component controls layout
<Button margin="1rem" />
```

### ❌ Global Style Pollution

```css
/* ❌ Bad: Global styles affecting everything */
.card {
  /* This affects ALL cards */
}

.card strong {
  /* This breaks encapsulation */
}
```

## Documentation Example

When you must break principles, document it:

```tsx
/**
 * Modal Component
 * 
 * BREAKS ENCAPSULATION PRINCIPLE: This component controls
 * its own positioning (centered) because modals need to
 * overlay the entire viewport.
 * 
 * Trade-off: Loses reusability in layout contexts but
 * gains required modal behavior.
 */
function Modal({ children }: ModalProps) {
  return (
    <div
      className="modal-overlay"
      style={{
        position: 'fixed',
        top: 0,
        left: 0,
        right: 0,
        bottom: 0,
        display: 'flex',
        justifyContent: 'center',
        alignItems: 'center',
      }}
    >
      <div className="modal-content">{children}</div>
    </div>
  );
}
```

## Next Steps

- [Components](Components/README.md) - Component styling patterns
- [Layout Components](Components/layout-components.md) - Layout composition
- [Polymorphic Components](Components/Polymorphic%20component.md) - Dynamic rendering
