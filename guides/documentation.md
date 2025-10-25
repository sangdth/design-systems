# Documentation Strategy

Make sure guidelines and descriptions are up to date so other developers can understand and know how to use components effectively.

Good documentation isn't just words – the code itself should be written in a descriptive way.

## Philosophy

### Self-Documenting Code First

```typescript
// ❌ Bad: What does this do?
const calc = (a, b, c) => a + b * c;

// ✅ Good: Clear from the name
const calculateTotal = (base: number, tax: number, quantity: number) => 
  base + tax * quantity;
```

### Documentation as a Learning Tool

Documentation should help developers:
1. **Understand** what something does
2. **Learn** how to use it
3. **Discover** what's possible
4. **Avoid** common mistakes

## Component Documentation

### Basic Structure

```markdown
# Component Name

Brief one-line description of what the component does.

## Usage

Basic example showing common use case.

## Props

Table of all props with types, defaults, and descriptions.

## Examples

Multiple real-world examples with explanations.

## Accessibility

Accessibility considerations and implementation details.

## API Reference

Complete technical details for advanced usage.
```

### Example: Button Component

```markdown
# Button

A button component that triggers actions and supports multiple variants, sizes, and states.

## Usage

\`\`\`tsx
import { Button } from '@design-system/components';

function App() {
  return (
    <Button onClick={() => console.log('clicked')}>
      Click me
    </Button>
  );
}
\`\`\`

## Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| variant | 'primary' \| 'secondary' | 'primary' | Visual style of button |
| size | 'sm' \| 'md' \| 'lg' | 'md' | Size of button |
| disabled | boolean | false | Whether button is disabled |
| loading | boolean | false | Show loading state |
| onClick | () => void | undefined | Click handler |

## Examples

### Variants

\`\`\`tsx
<Button variant="primary">Primary</Button>
<Button variant="secondary">Secondary</Button>
<Button variant="outline">Outline</Button>
\`\`\`

### Sizes

\`\`\`tsx
<Button size="sm">Small</Button>
<Button size="md">Medium</Button>
<Button size="lg">Large</Button>
\`\`\`

## Accessibility

- Keyboard accessible (Space and Enter activate)
- Focus visible
- ARIA labels for icon-only buttons
- Loading state announces to screen readers

## API Reference

For advanced usage, see the [full API documentation](./api/Button.md).
```

## Code Documentation Standards

### JSDoc Comments

```typescript
/**
 * Button component for triggering actions
 * 
 * @example
 * ```tsx
 * <Button variant="primary" onClick={handleClick}>
 *   Submit
 * </Button>
 * ```
 */
export const Button = ({ 
  variant = 'primary',
  children,
  ...props 
}: ButtonProps) => {
  // Implementation
};
```

### TypeScript Types as Documentation

```typescript
// ✅ Good: Types document the API
interface ButtonProps {
  /**
   * Visual style variant
   * @default 'primary'
   */
  variant?: 'primary' | 'secondary' | 'outline';
  
  /**
   * Size of the button
   * @default 'md'
   */
  size?: 'sm' | 'md' | 'lg';
  
  children: React.ReactNode;
}

// ❌ Bad: No type information
const Button = (props: any) => { };
```

## Keeping Documentation Current

### Problem: Outdated Docs

```markdown
<!-- What's in docs -->
<Button variant="primary" />

<!-- What actually works -->
<Button variant="primary" size="md" />
```

### Solution: Automated Testing

```typescript
// Test examples in documentation
describe('Documentation Examples', () => {
  it('runs example from README', () => {
    render(<Button variant="primary">Click</Button>);
    expect(screen.getByRole('button')).toBeInTheDocument();
  });
});
```

### Documentation Reviews

```markdown
## Documentation Review Checklist

- [ ] Examples actually work
- [ ] All props documented
- [ ] Type information is current
- [ ] Accessibility notes are accurate
- [ ] Code matches current implementation
```

## Types of Documentation

### 1. Usage Documentation

**For:** End users of the design system

```markdown
## Getting Started

Install the package:
\`\`\`bash
npm install @design-system/components
\`\`\`

Import and use:
\`\`\`tsx
import { Button } from '@design-system/components';
\`\`\`
```

### 2. API Documentation

**For:** Developers building with the system

```typescript
/**
 * Button component
 * 
 * @param props - Button properties
 * @param props.variant - Visual variant
 * @param props.size - Size of button
 * @returns Button element
 */
```

### 3. Architecture Documentation

**For:** Contributors and maintainers

```markdown
## Component Architecture

The Button component is built on top of:
- Styled-components for styling
- Reakit for accessibility primitives
- React.forwardRef for ref forwarding
```

## Documentation Tools

### Storybook

Interactive component documentation:

```typescript
// Button.stories.tsx
export default {
  title: 'Components/Button',
  component: Button,
};

export const Primary = {
  args: {
    children: 'Click me',
    variant: 'primary',
  },
};
```

### JSDoc

Generate API documentation:

```typescript
/**
 * Calculates the total price
 * @param {number} basePrice - Base price
 * @param {number} taxRate - Tax rate (0-1)
 * @returns {number} Total price including tax
 */
function calculateTotal(basePrice: number, taxRate: number): number {
  return basePrice * (1 + taxRate);
}
```

### TypeDoc

TypeScript documentation generator:

```bash
typedoc --out docs src/
```

## Best Practices

### 1. Write for Different Audiences

- **New users**: Simple examples
- **Experienced users**: Advanced patterns
- **Contributors**: Architecture and design decisions

### 2. Include Real Examples

```markdown
<!-- ❌ Bad: Lorem ipsum -->
<Card title="Card Title" description="Lorem ipsum dolor sit amet" />

<!-- ✅ Good: Real use case -->
<Card 
  title="User Profile"
  description="Manage your account settings"
/>
```

### 3. Show Common Patterns

```markdown
## Common Patterns

### Form Submission

\`\`\`tsx
<form onSubmit={handleSubmit}>
  <Input name="email" />
  <Button type="submit" loading={isSubmitting}>
    {isSubmitting ? 'Submitting...' : 'Submit'}
  </Button>
</form>
\`\`\`

### Navigation

\`\`\`tsx
<Button as="a" href="/dashboard">
  Go to Dashboard
</Button>
\`\`\`
```

### 4. Document Edge Cases

```markdown
## Edge Cases

### Loading State

When `loading={true}`, the button shows a spinner and is disabled:

\`\`\`tsx
<Button loading={true}>Never Clickable</Button>
\`\`\`

### Long Text

Buttons wrap text by default. To truncate:

\`\`\`tsx
<Button className="truncate">Very long text...</Button>
\`\`\`
```

### 5. Keep Examples Working

```bash
# Run documentation examples as tests
npm run test:docs

# Validate examples in CI
npm run validate:examples
```

## Documentation Maintenance

### Regular Updates

- Review docs with each release
- Update when API changes
- Remove deprecated examples
- Add new patterns as they emerge

### Feedback Loop

```markdown
## Documentation Feedback

Found a problem or unclear documentation?
- Open an issue with "docs" label
- Submit PR with improvements
- Discuss in team meeting
```

## Resources

- [Storybook](https://storybook.js.org/)
- [TypeDoc](https://typedoc.org/)
- [Writing Documentation - web.dev](https://web.dev/documentation/)

## Next Steps

- [Getting Started Guide](../docs/getting-started.md)
- [Component Documentation Template](./components/TEMPLATE.md)
- [Contributing Guide](../contribution-guide.md) 