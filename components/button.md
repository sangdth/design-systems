# Button Component

Buttons trigger actions. They're the most common interactive element in user interfaces.

## Basic Button

```tsx
interface ButtonProps {
  children: React.ReactNode;
  onClick?: () => void;
  type?: 'button' | 'submit' | 'reset';
  disabled?: boolean;
}

function Button({ children, onClick, type = 'button', disabled }: ButtonProps) {
  return (
    <button type={type} onClick={onClick} disabled={disabled}>
      {children}
    </button>
  );
}
```

## Variants

### Visual Variants

```tsx
interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'outline' | 'ghost' | 'plain';
  children: React.ReactNode;
  onClick?: () => void;
}

const buttonVariants = {
  primary: 'bg-blue-600 text-white hover:bg-blue-700',
  secondary: 'bg-gray-200 text-gray-900 hover:bg-gray-300',
  outline: 'border-2 border-blue-600 text-blue-600 hover:bg-blue-50',
  ghost: 'bg-transparent text-gray-700 hover:bg-gray-100',
  plain: 'bg-transparent text-gray-700 hover:bg-gray-50',
};

function Button({ variant = 'primary', children, onClick }: ButtonProps) {
  return (
    <button
      className={`px-4 py-2 rounded ${buttonVariants[variant]}`}
      onClick={onClick}
    >
      {children}
    </button>
  );
}
```

### Size Variants

```tsx
interface ButtonProps {
  size?: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
}

const buttonSizes = {
  sm: 'px-3 py-1.5 text-sm',
  md: 'px-4 py-2 text-base',
  lg: 'px-6 py-3 text-lg',
};

function Button({ size = 'md', children, ...props }: ButtonProps) {
  return (
    <button className={`rounded ${buttonSizes[size]}`} {...props}>
      {children}
    </button>
  );
}
```

## States

### Loading State

```tsx
interface ButtonProps {
  loading?: boolean;
  children: React.ReactNode;
  onClick?: () => void;
}

function Button({ loading, children, onClick }: ButtonProps) {
  return (
    <button
      onClick={onClick}
      disabled={loading}
      aria-busy={loading}
    >
      {loading ? (
        <>
          <Spinner />
          <span className="sr-only">Loading...</span>
        </>
      ) : (
        children
      )}
    </button>
  );
}
```

### Disabled State

```tsx
function Button({ disabled, children, ...props }: ButtonProps) {
  return (
    <button
      disabled={disabled}
      aria-disabled={disabled}
      className={disabled ? 'opacity-50 cursor-not-allowed' : ''}
      {...props}
    >
      {children}
    </button>
  );
}
```

### Active/Focus States

```tsx
function Button({ children, ...props }: ButtonProps) {
  return (
    <button
      className="
        focus:outline-none
        focus:ring-2
        focus:ring-blue-500
        focus:ring-offset-2
        active:scale-95
        transition-transform
      "
      {...props}
    >
      {children}
    </button>
  );
}
```

## Composition with Icons

### Icon Button

```tsx
interface IconButtonProps {
  icon: React.ReactNode;
  label: string;
  onClick?: () => void;
}

function IconButton({ icon, label, onClick }: IconButtonProps) {
  return (
    <button
      onClick={onClick}
      aria-label={label}
      className="p-2 rounded hover:bg-gray-100"
    >
      {icon}
      <span className="sr-only">{label}</span>
    </button>
  );
}
```

### Button with Icon

```tsx
interface ButtonWithIconProps {
  icon?: React.ReactNode;
  iconPosition?: 'left' | 'right';
  children: React.ReactNode;
}

function ButtonWithIcon({
  icon,
  iconPosition = 'left',
  children,
  ...props
}: ButtonWithIconProps) {
  return (
    <button {...props}>
      {icon && iconPosition === 'left' && icon}
      {children}
      {icon && iconPosition === 'right' && icon}
    </button>
  );
}
```

## Full-Width Button

```tsx
interface ButtonProps {
  fullWidth?: boolean;
  children: React.ReactNode;
}

function Button({ fullWidth, children, ...props }: ButtonProps) {
  return (
    <button
      className={fullWidth ? 'w-full' : ''}
      {...props}
    >
      {children}
    </button>
  );
}
```

## Complete Button Component

```tsx
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'outline' | 'ghost' | 'plain';
  size?: 'sm' | 'md' | 'lg';
  fullWidth?: boolean;
  loading?: boolean;
  icon?: React.ReactNode;
  iconPosition?: 'left' | 'right';
  children: React.ReactNode;
}

const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  (
    {
      variant = 'primary',
      size = 'md',
      fullWidth = false,
      loading = false,
      icon,
      iconPosition = 'left',
      disabled,
      children,
      className,
      ...props
    },
    ref
  ) => {
    const variantClasses = {
      primary: 'bg-blue-600 text-white hover:bg-blue-700',
      secondary: 'bg-gray-200 text-gray-900 hover:bg-gray-300',
      outline: 'border-2 border-blue-600 text-blue-600 hover:bg-blue-50',
      ghost: 'bg-transparent text-gray-700 hover:bg-gray-100',
      plain: 'bg-transparent text-gray-700 hover:bg-gray-50',
    };

    const sizeClasses = {
      sm: 'px-3 py-1.5 text-sm',
      md: 'px-4 py-2 text-base',
      lg: 'px-6 py-3 text-lg',
    };

    const isDisabled = disabled || loading;

    return (
      <button
        ref={ref}
        disabled={isDisabled}
        aria-busy={loading}
        className={cn(
          'rounded font-medium transition-colors',
          'focus:outline-none focus:ring-2 focus:ring-offset-2',
          'disabled:opacity-50 disabled:cursor-not-allowed',
          variantClasses[variant],
          sizeClasses[size],
          fullWidth && 'w-full',
          className
        )}
        {...props}
      >
        {loading && <Spinner aria-hidden="true" />}
        {icon && iconPosition === 'left' && !loading && icon}
        {children}
        {icon && iconPosition === 'right' && !loading && icon}
      </button>
    );
  }
);

Button.displayName = 'Button';
```

## Button Groups

```tsx
interface ButtonGroupProps {
  children: React.ReactNode;
}

function ButtonGroup({ children }: ButtonGroupProps) {
  return (
    <div role="group" className="inline-flex">
      {React.Children.map(children, (child, index) => {
        if (!React.isValidElement(child)) return child;
        
        const isFirst = index === 0;
        const isLast = index === React.Children.count(children) - 1;
        
        return React.cloneElement(child, {
          className: cn(
            child.props.className,
            !isFirst && 'rounded-l-none',
            !isLast && 'rounded-r-none',
            !isLast && 'border-r-0'
          ),
        });
      })}
    </div>
  );
}

// Usage
<ButtonGroup>
  <Button>First</Button>
  <Button>Second</Button>
  <Button>Third</Button>
</ButtonGroup>
```

## Split Button

```tsx
interface SplitButtonProps {
  mainAction: () => void;
  dropdownActions: Array<{ label: string; action: () => void }>;
  children: React.ReactNode;
}

function SplitButton({ mainAction, dropdownActions, children }: SplitButtonProps) {
  const [isOpen, setIsOpen] = useState(false);
  const buttonRef = useRef<HTMLButtonElement>(null);
  
  return (
    <div className="inline-flex">
      <Button onClick={mainAction}>{children}</Button>
      <div className="relative">
        <Button
          ref={buttonRef}
          onClick={() => setIsOpen(!isOpen)}
          aria-label="More options"
        >
          <ChevronDown />
        </Button>
        
        {isOpen && (
          <Dropdown
            items={dropdownActions}
            onClose={() => setIsOpen(false)}
            anchorElement={buttonRef.current}
          />
        )}
      </div>
    </div>
  );
}
```

## Accessibility

### Keyboard Support

```tsx
function Button({ onClick, children, ...props }: ButtonProps) {
  const handleKeyDown = (e: React.KeyboardEvent) => {
    // Space and Enter activate buttons
    if (e.key === ' ' || e.key === 'Enter') {
      e.preventDefault();
      onClick?.();
    }
  };
  
  return (
    <button
      onClick={onClick}
      onKeyDown={handleKeyDown}
      {...props}
    >
      {children}
    </button>
  );
}
```

### ARIA Labels

```tsx
// Icon-only buttons need labels
<IconButton icon={<MenuIcon />} aria-label="Open menu" />

// Buttons that close modals
<Button aria-label="Close dialog">×</Button>

// Loading buttons announce state
<Button aria-busy={loading}>
  {loading ? 'Saving...' : 'Save'}
</Button>
```

## Best Practices

1. **Use semantic HTML** - `<button>` for actions, not `<div>`
2. **Provide labels** - Always have visible text or aria-label
3. **Loading states** - Show feedback during async operations
4. **Disabled states** - Prevent interaction when action isn't available
5. **Focus indicators** - Make focus visible for keyboard users
6. **Touch targets** - Minimum 44x44px for mobile

## Examples

### Form Submit

```tsx
<form onSubmit={handleSubmit}>
  <Button type="submit" loading={isSubmitting}>
    {isSubmitting ? 'Creating...' : 'Create Account'}
  </Button>
</form>
```

### Destructive Action

```tsx
// Destructive actions typically use danger variant
// If implementing, add to variant types
<Button variant="primary" className="bg-red-600 hover:bg-red-700" onClick={handleDelete}>
  Delete Account
</Button>
```

### Navigation Button

```tsx
<Button variant="ghost" as="a" href="/settings">
  <SettingsIcon />
  Settings
</Button>
```

## Accessibility References

### ARIA Design Patterns

- [Button Pattern - W3C ARIA APG](https://www.w3.org/WAI/ARIA/apg/patterns/button/)
- [Toggle Button Pattern - W3C ARIA APG](https://www.w3.org/WAI/ARIA/apg/patterns/button/#toggle-button)

### Web Standards

- [MDN: HTML button element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button)
- [MDN: ARIA button role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/button_role)
- [MDN: Keyboard navigation and focus](https://developer.mozilla.org/en-US/docs/Web/Accessibility/Keyboard-navigable_JavaScript_widgets)

## Next Steps

- [Polymorphic Components](Polymorphic%20component.md) - Dynamic element rendering
- [Input Component](input.md) - Form input patterns
- [Accessibility Guide](../Accessibility/README.md) - Accessibility patterns
