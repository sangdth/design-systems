# Opportunistic Refactoring

Every system will come to a phase where it needs to be refactored; a design system is not exceptional.

## Philosophy

Refactoring should be an opportunistic activity – it should be done **whenever** and **wherever** code needs to be cleaned up – by **whoever**.

### The Boy Scout Rule

Always leave the code behind in a **better** state than you found it.

But don't go down the rabbit hole too long.

## Prerequisites for Opportunistic Refactoring

### Clear Boundaries

```typescript
// Well-defined boundaries enable safe refactoring
interface ButtonProps {
  variant: 'primary' | 'secondary';
  children: React.ReactNode;
}

export const Button = ({ variant, children }: ButtonProps) => {
  // Clear implementation
};
```

### Stable APIs

```typescript
// Public API is stable and documented
/**
 * @public
 * Button component for triggering actions
 */
export const Button = (props: ButtonProps) => {
  // Internal implementation can change without breaking API
};
```

## When to Refactor Opportunistically

### Green Light Conditions

- **Small scope**: Can be done in 15-30 minutes
- **Clear boundaries**: Changes are isolated
- **No feature work**: Not in the middle of a feature
- **Tests exist**: Have coverage for safety
- **Immediate benefit**: Removes friction right away

### Red Light Conditions

- **Large scope**: Would take hours or days
- **Touching many files**: High risk of conflicts
- **No tests**: Danger of breaking things
- **Feature branch**: Risk of merge conflicts
- **Complex dependencies**: Requires coordination

**Strong code ownership + feature branch usually lead to discouragement of opportunistic refactoring** because when a feature lasts longer than many days, touching multiple places will most likely cause trouble.

## Types of Opportunistic Refactoring

### 1. Naming Improvements

```typescript
// Before
const handleClick = (e) => {
  dispatch({ type: 'CLICK', payload: e });
};

// After
const handleButtonClick = (event: React.MouseEvent) => {
  dispatch({ type: 'BUTTON_CLICK', payload: event });
};
```

### 2. Extracting Functions

```typescript
// Before: Logic embedded in component
const Component = () => {
  const result = data
    .filter(item => item.status === 'active')
    .map(item => item.value)
    .reduce((sum, val) => sum + val, 0);
  // ...
};

// After: Extracted to meaningful function
const calculateActiveSum = (data: Item[]) => {
  return data
    .filter(item => item.status === 'active')
    .map(item => item.value)
    .reduce((sum, val) => sum + val, 0);
};

const Component = () => {
  const result = calculateActiveSum(data);
  // ...
};
```

### 3. Removing Duplication

```typescript
// Before: Duplicated logic
const Card = ({ title }) => (
  <div className="card">
    <h3 className="card-title">{title}</h3>
  </div>
);

const Modal = ({ title }) => (
  <div className="modal">
    <h3 className="modal-title">{title}</h3>
  </div>
);

// After: Shared component
const Title = ({ children, variant = 'card' }) => (
  <h3 className={`${variant}-title`}>{children}</h3>
);
```

### 4. Simplifying Conditionals

```typescript
// Before: Complex logic
const getVariant = (primary, secondary, danger) => {
  if (primary) return 'primary';
  if (secondary) return 'secondary';
  if (danger) return 'danger';
  return 'default';
};

// After: Cleaner API
const getVariant = (variant: 'primary' | 'secondary' | 'danger' | 'default') => {
  return variant;
};
```

### 5. Type Safety Improvements

```typescript
// Before: Loose types
const handleSubmit = (data: any) => {
  // ...
};

// After: Proper types
interface SubmitData {
  email: string;
  password: string;
}

const handleSubmit = (data: SubmitData) => {
  // ...
};
```

## Refactoring Strategies

### Small, Frequent Changes

```bash
# Better: Small, focused refactorings
git commit -m "refactor: improve Button prop types"
git commit -m "refactor: extract utility function"

# Worse: Large refactoring in one commit
git commit -m "refactor: massive refactoring of entire component library"
```

### Within Existing Tests

```typescript
// Refactor while tests pass
describe('Button', () => {
  it('renders correctly', () => {
    // Tests provide safety net for refactoring
    const { container } = render(<Button>Click</Button>);
    expect(container).toMatchSnapshot();
  });
});

// Now refactor implementation with confidence
```

### Documentation Updates

```typescript
// Before refactoring
/**
 * Button component
 */

// After: Opportunistic documentation improvement
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
```

## The Rabbit Hole Warning

### When to Stop

Signs you're going too deep:

- **Time estimate doubles**: "This will be quick" → 2 hours later
- **Scope creeps**: Started with one file, now touching ten
- **New tests needed**: Realize existing tests don't cover this
- **Breaking changes**: Started refactor, now needs API changes

### Time Limits

Set a timer:

```bash
# Time-boxed refactoring
# Set 30-minute timer
# If not done, create ticket for later
```

## Real-World Scenarios

### Scenario 1: During Bug Fix

```typescript
// Found bug while fixing something else
const Button = ({ variant }) => {
  // BUG: Missing type check
  
  // FIX: Add validation
  if (!['primary', 'secondary'].includes(variant)) {
    console.warn(`Invalid variant: ${variant}`);
  }
  
  // OPPORTUNISTIC REFACTOR: Also improve types
  const handleClick = (e: React.MouseEvent) => {
    // Better typing
  };
};
```

### Scenario 2: Code Review

```typescript
// Reviewing PR, notice improvement opportunity
// ADD COMMENT:

// 💡 While you're here, we could also extract this logic:

const useButtonState = (initialValue) => {
  // Extracted for reuse
};

// LEAVE FOR AUTHOR to decide if they want to include
```

### Scenario 3: Adding Feature

```typescript
// Adding new feature, notice duplication
// OPPORTUNISTIC: Extract shared logic

// Before: Duplication
const Modal = () => {
  const [isOpen, setIsOpen] = useState(false);
  // ...
};

const Dropdown = () => {
  const [isOpen, setIsOpen] = useState(false);
  // ...
};

// After: Extract hook
const useToggle = (initialValue = false) => {
  const [isOpen, setIsOpen] = useState(initialValue);
  const toggle = () => setIsOpen(prev => !prev);
  return [isOpen, toggle];
};
```

## Best Practices

### 1. Tests First

```typescript
// Add tests before refactoring
describe('Button', () => {
  it('handles click', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick} />);
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalled();
  });
});

// Now refactor with confidence
```

### 2. One Thing at a Time

```typescript
// Good: Single responsibility
// Commit 1: Rename variable
// Commit 2: Extract function
// Commit 3: Improve types

// Bad: Multiple concerns in one commit
// Rename, extract, add features, fix bugs...
```

### 3. Document Rationale

```typescript
// Commit message explains why
git commit -m "refactor: extract calculation logic

Extracts calculateTotal to improve readability and reusability.
Preparation for upcoming discount feature."

// Not just "refactor"
```

## Migration Strategies

### Gradual Migration

```typescript
// Old API still works
export function Button({ variant, ...props }: ButtonProps) {
  // New implementation
  return <button {...props} />;
}

// New API also available
export function ButtonV2({ variant, ...props }: ButtonPropsV2) {
  return <button {...props} />;
}

// Deprecation period
// v1.5: Add ButtonV2
// v2.0: Remove Button
```

### Codemods

```bash
# Automated refactoring
npx codemod --template=rename-props Button.tsx

# Transforms code automatically
<Button variant="primary" /> 
# → 
<Button buttonVariant="primary" />
```

## Measuring Refactoring Success

### Metrics

```typescript
interface RefactoringMetrics {
  timeSaved: number;        // Daily time saved
  bugsPrevented: number;    // Bugs caught by refactoring
  codeComplexity: number;   // Cyclomatic complexity reduced
  testCoverage: number;     // Test coverage after refactoring
}
```

### Code Quality Indicators

- **Smaller functions**: Average function length decreased
- **Fewer dependencies**: Coupling reduced
- **Better tests**: Coverage increased
- **Fewer bugs**: Post-refactoring bug count decreased

## Resources

- [Refactoring by Martin Fowler](https://refactoring.com/)
- [Working Effectively with Legacy Code](https://www.amazon.com/Working-Effectively-Legacy-Michael-Feathers/dp/0131177052)

## Next Steps

- [Papercut Workflow](../Papercut.md) - Small improvements
- [Common Mistakes](../Mistakes%20to%20avoid.md) - What to avoid
- [Contributing Guide](../contribution-guide.md) - Refactoring as contribution 