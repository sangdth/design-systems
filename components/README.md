# Components

Components are the building blocks of user interfaces. They encapsulate behavior, styling, and accessibility features into reusable pieces.

## Component Philosophy

### Keep It Flexible

Components should be broad and open to several applications and interpretations. Design for flexibility while maintaining consistency. Allow as much as possible within the constraints of good design.

```tsx
// ✅ Good: Flexible and composable
<Button variant="primary" size="md" fullWidth loading>
  Submit
</Button>

// ❌ Bad: Too rigid
<SubmitButtonOnly />
```

## Component Anatomy

### 1. API Design

Clear, intuitive props that describe what the component does, not how it looks.

### 2. States and Variations

Handle different states (hover, active, disabled, loading) and visual variations.

### 3. Accessibility

Built-in support for keyboard navigation, screen readers, and ARIA attributes.

### 4. Composition

Work together seamlessly to build complex interfaces.

## Component Types

### Atoms (Primitives)

Basic building blocks like buttons, inputs, labels.

### Molecules

Simple combinations like form fields, search bars.

### Organisms

Complex UI sections like headers, forms, cards.

### Patterns

Complete solutions like authentication flows, data tables.

## Core Principles

### 1. Composition Over Configuration

```tsx
// ✅ Prefer composition
<Card>
  <CardHeader>Title</CardHeader>
  <CardBody>Content</CardBody>
</Card>

// ❌ Avoid over-configuration
<Card hasHeader hasBody title="Title" content="Content" />
```

### 2. Progressive Enhancement

Build basic functionality first, enhance with JavaScript.

### 3. Accessibility First

Every component works without JavaScript and with assistive technologies.

### 4. Consistent APIs

Similar components share similar prop names and patterns.

## Component Documentation

Every component should document:

- **Purpose**: What problem does it solve?
- **Usage**: Basic and advanced examples
- **Props**: Complete API reference
- **States**: Available variants and states
- **Accessibility**: Keyboard navigation, ARIA attributes
- **Design guidelines**: When and how to use

## Related Topics

- [Getting Started](Intro.md) - Component patterns and APIs
- [Polymorphic Components](Polymorphic%20component.md) - Flexible component APIs
- [Button](button.md) - Button component patterns
- [Input](input.md) - Form input patterns
- [Dialog](dialog.md) - Modal patterns
- [Layout Components](layout-components.md) - Layout primitives
