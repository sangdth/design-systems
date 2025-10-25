# Common Mistakes to Avoid

Learning from others' mistakes saves time and prevents costly redesigns. Here are the most common pitfalls when building and maintaining design systems.

## The Big Four

### 1. Starting for Scale

Building for hypothetical future needs instead of current requirements.

**Problem:**

Creating complex component APIs "just in case":

```tsx
// ❌ Bad: Over-engineered for future flexibility
<Button
  variant="primary"
  size="medium"
  shape="rounded"
  elevation="medium"
  animation="bounce"
  interaction="ripple"
  theme="dark"
  responsive={true}
  adaptive={true}
  accessibility="enhanced"
>
  Click me
</Button>
```

**Solution:**

Start simple, add complexity only when needed:

```tsx
// ✅ Good: Simple and sufficient
<Button variant="primary">
  Click me
</Button>
```

**When to Add Complexity:**

- Three or more similar use cases require it
- Developer feedback indicates need
- Data shows actual usage patterns

### 2. Educating Before Building

Spending too much time on documentation and standards before shipping working components.

**Problem:**

- Weeks of design principles documents
- Style guides with no code
- Theoretical specifications
- No real usage to learn from

**Solution:**

Build → Use → Learn → Document:

```markdown
## Development Workflow

1. Build minimum viable component
2. Use in real application
3. Gather feedback
4. Document patterns that emerge
5. Refine based on learning
```

**Signs You're Over-Documenting:**

- More documentation than code
- No components shipped in months
- Developer questions reveal gaps between docs and reality

### 3. Not Discussing the Workflow

Building components without understanding how they'll be used in practice.

**Problem:**

```tsx
// Designed based on assumptions
<Card
  title="Card Title"
  description="Card description"
  actions={<Button>Action</Button>}
/>
```

**Reality:**

```tsx
// What developers actually need
<Card>
  <CardHeader>
    <h3>Custom Title with Icon</h3>
    <Badge>New</Badge>
  </CardHeader>
  <CardBody>
    <p>Custom content with any markup</p>
  </CardBody>
</CardBody>
```

**Solution:**

1. Interview 5-10 target users before building
2. Build from real use cases
3. Test with real content (not "Lorem ipsum")
4. Iterate based on actual feedback

### 4. Not Documenting Decisions

Making decisions in meetings but not recording why.

**Problem:**

Three months later:

- "Why did we choose this approach?"
- "Can we change this?"
- "Who decided this?"

**Solution:**

Document as you go:

```markdown
## Decision: Button Variant System

**Date:** 2024-01-15
**Participants:** Design Team, Engineering Lead
**Decision:** Use string-based variants instead of boolean props

**Rationale:**
- More scalable as variants grow
- Prevents prop explosion (`isPrimary`, `isSecondary`, `isDanger`, etc.)
- Easier to extend with compound variants

**Alternatives Considered:**
- Boolean props: Too many props, not scalable
- Separate components: Code duplication
- CSS class composition: Runtime overhead

**Trade-offs:**
- Slightly less TypeScript safety (string instead of enum)
- Worth it for better DX at scale
```

## Additional Mistakes

### Not Establishing Design Tokens First

Building components with hardcoded values:

```tsx
// ❌ Bad: Hardcoded values
<button style={{ padding: '8px 16px', backgroundColor: '#007AFF' }}>

// ✅ Good: Token-based
<button style={{ padding: tokens.spacing.md, backgroundColor: tokens.color.primary }}>
```

### Inconsistent Naming Conventions

```tsx
// ❌ Bad: Inconsistent naming
<Button isLoading={true} />
<Card isExpanded={false} />
<Modal isOpen={true} />

// ✅ Good: Consistent naming
<Button loading={true} />
<Card expanded={false} />
<Modal open={true} />
```

### Breaking Changes Without Migration Paths

```typescript
// ❌ Bad: Breaking change without path
// v2.0.0 - Removed Button.defaultProps (BREAKING)

// ✅ Good: Gradual migration
// v1.5.0 - Deprecate old API, add new API
// v2.0.0 - Remove deprecated API
Button.propTypes = {
  variant: PropTypes.oneOf(['primary', 'secondary']), // Deprecated: use 'outline' instead
};
```

### Ignoring Performance Early

```tsx
// ❌ Bad: No performance budget
<div style={{ backgroundColor: 'blue' }}> // Inline styles everywhere
  {heavyComputation()}
</div>

// ✅ Good: Performance-conscious
<div className="bg-blue"> {/* CSS classes */}
  <MemoizedComponent />
</div>
```

### Not Testing Accessibility

```tsx
// ❌ Bad: No accessibility testing
<div onClick={handleClick}>Click me</div>

// ✅ Good: Accessibility tested
<button onClick={handleClick} aria-label="Submit form">
  Click me
</button>

// With automated tests
test('Button is accessible', () => {
  const { container } = render(<Button>Click</Button>);
  expect(screen.getByRole('button')).toBeInTheDocument();
});
```

## Prevention Strategies

### 1. Start with Real Use Cases

Before building any component, have 3+ real examples from production apps.

### 2. Ship Early, Learn Fast

Release v0.1.0 quickly. Every component should see production usage within 2 weeks.

### 3. Document as You Go

Don't wait until everything is "perfect" to document. Perfect never comes.

### 4. Measure Everything

- Component usage analytics
- Developer satisfaction surveys
- Load times, bundle sizes
- Accessibility audit scores

### 5. Regular Reviews

Monthly reviews of:

- Most used components (should match your roadmap)
- Most buggy components (needs refactor)
- Least used components (consider removing)

## Anti-Patterns Summary

| Mistake | Impact | Prevention |
|---------|--------|-----------|
| Over-engineering | Harder to use, maintain | Start simple, add when needed |
| Premature documentation | Wasted time, outdated docs | Document from real usage |
| Ignoring workflow | Unusable components | Interview users first |
| No decision records | Repeated discussions | Document meetings |
| No tokens | Inconsistent design | Start with tokens |
| Breaking changes | Developer frustration | Gradual migration |
| No performance budget | Slow apps | Measure from day 1 |
| Ignoring accessibility | Legal risk, bad UX | Test early |

## Resources

- [Design Systems Handbook](https://www.designbetter.co/design-systems-handbook)
- [Common Mistakes in Design Systems](https://www.bradfrost.com/blog/post/common-design-system-mistakes/)
- [UXPin State of Design Systems](https://www.uxpin.com/studio/blog/design-systems-survey-uxdesign/)

## Next Steps

- [Papercut Workflow](Papercut.md) - Handling small improvements
- [Refactoring Guide](Refactoring/Opportunistic%20Refactoring.md) - Incremental improvements
- [Contributing Guide](../contribution-guide.md) - Preventing mistakes through process
