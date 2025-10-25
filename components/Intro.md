# Component Patterns and APIs

Components are reusable UI building blocks. This guide covers core patterns for designing flexible, maintainable components.

## Core Principle

Keep components broad and open to several applications and interpretations. Design for flexibility while maintaining consistency. In short: **allow as much as possible** within your constraints.

## Component Anatomy

### 1. Props Interface

Design clear, intuitive props that describe intent.

```tsx
interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'ghost';
  size?: 'sm' | 'md' | 'lg';
  fullWidth?: boolean;
  loading?: boolean;
  disabled?: boolean;
  children: React.ReactNode;
  onClick?: () => void;
}
```

### 2. Default Values

Provide sensible defaults.

```tsx
function Button({
  variant = 'primary',
  size = 'md',
  fullWidth = false,
  // ...
}: ButtonProps) {
  // Implementation
}
```

### 3. Forward Refs

Support ref forwarding for imperative actions.

```tsx
const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  ({ children, ...props }, ref) => {
    return (
      <button ref={ref} {...props}>
        {children}
      </button>
    );
  }
);
```

## Composition Patterns

### Compound Components

Combine related components into a single logical unit.

```tsx
function Card({ children }) {
  return <div className="card">{children}</div>;
}

Card.Header = function CardHeader({ children }) {
  return <div className="card-header">{children}</div>;
};

Card.Body = function CardBody({ children }) {
  return <div className="card-body">{children}</div>;
};

Card.Footer = function CardFooter({ children }) {
  return <div className="card-footer">{children}</div>;
};

// Usage
<Card>
  <Card.Header>Title</Card.Header>
  <Card.Body>Content</Card.Body>
  <Card.Footer>Actions</Card.Footer>
</Card>
```

### Render Props

Pass functions as children for flexible rendering.

```tsx
interface DataProviderProps {
  url: string;
  children: (data: any, loading: boolean, error: Error) => ReactNode;
}

function DataProvider({ url, children }: DataProviderProps) {
  const { data, loading, error } = useFetch(url);
  return <>{children(data, loading, error)}</>;
}

// Usage
<DataProvider url="/api/users">
  {(users, loading, error) => {
    if (loading) return <Spinner />;
    if (error) return <ErrorMessage />;
    return <UserList users={users} />;
  }}
</DataProvider>
```

### Higher-Order Components (HOCs)

Wrap components with additional functionality.

```tsx
function withLoading<T extends object>(
  Component: ComponentType<T>
) {
  return function WithLoading(props: T) {
    const { loading, ...rest } = props as any;
    if (loading) return <Spinner />;
    return <Component {...(rest as T)} />;
  };
}

const ButtonWithLoading = withLoading(Button);
```

## Prop Patterns

### Controlled vs Uncontrolled

```tsx
// Controlled
function Input({ value, onChange }) {
  return <input value={value} onChange={onChange} />;
}

// Uncontrolled
function Input({ defaultValue, ref }) {
  return <input defaultValue={defaultValue} ref={ref} />;
}

// Hybrid (best of both)
function Input({ value, defaultValue, onChange }) {
  return (
    <input
      value={value}
      defaultValue={defaultValue}
      onChange={onChange}
    />
  );
}
```

### Polymorphic Props

Components that can render as different elements.

```tsx
interface BoxProps {
  as?: React.ElementType;
  children: React.ReactNode;
}

function Box({ as: Component = 'div', ...props }: BoxProps) {
  return <Component {...props} />;
}

// Usage
<Box as="section">Content</Box>
<Box as={CustomComponent}>Content</Box>
```

### Style Props

Accept style props for flexibility.

```tsx
interface FlexProps {
  direction?: 'row' | 'column';
  align?: 'start' | 'center' | 'end';
  justify?: 'start' | 'center' | 'end' | 'space-between';
  gap?: string;
  className?: string;
  style?: React.CSSProperties;
  children: React.ReactNode;
}

function Flex({ direction, align, justify, gap, className, style, children }: FlexProps) {
  return (
    <div
      className={cn('flex', className)}
      style={{
        flexDirection: direction,
        alignItems: align,
        justifyContent: justify,
        gap,
        ...style,
      }}
    >
      {children}
    </div>
  );
}
```

## State Management

### Internal State

Manage UI-specific state internally.

```tsx
function Dropdown() {
  const [isOpen, setIsOpen] = useState(false);
  // ...
}
```

### External State

Accept state from parent for complex flows.

```tsx
interface DropdownProps {
  isOpen: boolean;
  onToggle: (open: boolean) => void;
}

function Dropdown({ isOpen, onToggle }: DropdownProps) {
  // ...
}
```

### Hybrid Approach

Provide both internal and controlled modes.

```tsx
interface DropdownProps {
  isOpen?: boolean;
  onToggle?: (open: boolean) => void;
  defaultOpen?: boolean;
}

function Dropdown({ isOpen, onToggle, defaultOpen }: DropdownProps) {
  const [internalOpen, setInternalOpen] = useState(defaultOpen || false);
  
  const actualOpen = isOpen !== undefined ? isOpen : internalOpen;
  const handleToggle = onToggle || setInternalOpen;
  // ...
}
```

## Accessibility Patterns

### Keyboard Navigation

```tsx
function Button({ onClick, children }) {
  const handleKeyDown = (e: React.KeyboardEvent) => {
    if (e.key === 'Enter' || e.key === ' ') {
      e.preventDefault();
      onClick?.();
    }
  };
  
  return (
    <button onClick={onClick} onKeyDown={handleKeyDown}>
      {children}
    </button>
  );
}
```

### ARIA Attributes

```tsx
function Dialog({ open, onClose, children }) {
  return (
    <>
      {open && (
        <div
          role="dialog"
          aria-modal="true"
          aria-labelledby="dialog-title"
        >
          {children}
        </div>
      )}
    </>
  );
}
```

### Focus Management

```tsx
function Modal({ open, onClose, children }) {
  const modalRef = useRef<HTMLDivElement>(null);
  
  useEffect(() => {
    if (open && modalRef.current) {
      modalRef.current.focus();
    }
  }, [open]);
  
  return (
    <div ref={modalRef} tabIndex={-1} onKeyDown={handleEscape}>
      {children}
    </div>
  );
}
```

## Best Practices

### 1. Single Responsibility

Each component should do one thing well.

### 2. Small and Focused

Break complex components into smaller, composable pieces.

### 3. Consistent Naming

Use clear, descriptive names that indicate purpose.

### 4. Document APIs

Provide JSDoc comments for complex props.

### 5. Type Safety

Use TypeScript for better DX and fewer bugs.

### 6. Test Edge Cases

Handle loading, error, and empty states.

## Next Steps

- [Polymorphic Components](Polymorphic%20component.md) - Dynamic element rendering
- [Button Component](button.md) - Complete button implementation
- [Input Component](input.md) - Form input patterns
- [Layout Components](layout-components.md) - Layout primitives
