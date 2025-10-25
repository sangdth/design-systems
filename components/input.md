# Input Component

Input components handle user text entry, from simple text fields to complex validated forms.

## Basic Input

```tsx
interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label?: string;
  error?: string;
  helperText?: string;
}

function Input({ label, error, helperText, ...props }: InputProps) {
  const id = useId();
  
  return (
    <div>
      {label && (
        <label htmlFor={id} className="block mb-1">
          {label}
        </label>
      )}
      <input
        id={id}
        className={cn(
          'w-full px-3 py-2 border rounded',
          error ? 'border-red-500' : 'border-gray-300',
          'focus:outline-none focus:ring-2 focus:ring-blue-500'
        )}
        aria-invalid={error ? 'true' : undefined}
        aria-describedby={
          error ? `${id}-error` : helperText ? `${id}-help` : undefined
        }
        {...props}
      />
      {helperText && !error && (
        <p id={`${id}-help`} className="mt-1 text-sm text-gray-600">
          {helperText}
        </p>
      )}
      {error && (
        <p id={`${id}-error`} className="mt-1 text-sm text-red-600" role="alert">
          {error}
        </p>
      )}
    </div>
  );
}
```

## Input Types

### Text Input

```tsx
<Input type="text" placeholder="Enter your name" />
```

### Email Input

```tsx
<Input
  type="email"
  placeholder="you@example.com"
  required
  aria-label="Email address"
/>
```

### Password Input

```tsx
const [showPassword, setShowPassword] = useState(false);

<Input
  type={showPassword ? 'text' : 'password'}
  placeholder="Enter password"
  endIcon={
    <button
      onClick={() => setShowPassword(!showPassword)}
      aria-label={showPassword ? 'Hide password' : 'Show password'}
    >
      {showPassword ? <EyeOffIcon /> : <EyeIcon />}
    </button>
  }
/>
```

### Search Input

```tsx
<Input
  type="search"
  placeholder="Search..."
  startIcon={<SearchIcon />}
/>
```

### Number Input

```tsx
<Input
  type="number"
  min={0}
  max={100}
  step={1}
  placeholder="Enter a number"
/>
```

## Input Variants

### With Icons

```tsx
interface InputProps {
  startIcon?: React.ReactNode;
  endIcon?: React.ReactNode;
}

function InputWithIcons({ startIcon, endIcon, ...props }: InputProps) {
  return (
    <div className="relative">
      {startIcon && (
        <span className="absolute left-3 top-1/2 -translate-y-1/2">
          {startIcon}
        </span>
      )}
      <input
        className={cn(
          'w-full py-2 border rounded',
          startIcon && 'pl-10',
          endIcon && 'pr-10'
        )}
        {...props}
      />
      {endIcon && (
        <span className="absolute right-3 top-1/2 -translate-y-1/2">
          {endIcon}
        </span>
      )}
    </div>
  );
}
```

### Sizes

```tsx
interface InputProps {
  size?: 'sm' | 'md' | 'lg';
}

const sizeClasses = {
  sm: 'px-2 py-1 text-sm',
  md: 'px-3 py-2 text-base',
  lg: 'px-4 py-3 text-lg',
};

function Input({ size = 'md', ...props }: InputProps) {
  return <input className={sizeClasses[size]} {...props} />;
}
```

### Disabled State

```tsx
<input
  disabled
  className="opacity-50 cursor-not-allowed bg-gray-100"
  aria-disabled="true"
/>
```

## Validation

### Real-time Validation

```tsx
function ValidatedInput({ value, onChange }: InputProps) {
  const [error, setError] = useState('');
  
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const newValue = e.target.value;
    onChange?.(e);
    
    // Validate
    if (newValue.length < 3) {
      setError('Must be at least 3 characters');
    } else {
      setError('');
    }
  };
  
  return (
    <Input
      value={value}
      onChange={handleChange}
      error={error}
    />
  );
}
```

### Async Validation

```tsx
function AsyncValidatedInput({ onValidate }: InputProps) {
  const [value, setValue] = useState('');
  const [error, setError] = useState('');
  const [validating, setValidating] = useState(false);
  
  const validateEmail = async (email: string) => {
    setValidating(true);
    try {
      const exists = await checkEmailExists(email);
      if (exists) {
        setError('Email already in use');
      } else {
        setError('');
      }
    } finally {
      setValidating(false);
    }
  };
  
  const debouncedValidate = useMemo(
    () => debounce(validateEmail, 500),
    []
  );
  
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const newValue = e.target.value;
    setValue(newValue);
    if (newValue) {
      debouncedValidate(newValue);
    }
  };
  
  return (
    <Input
      value={value}
      onChange={handleChange}
      error={error}
      endIcon={validating ? <Spinner /> : null}
    />
  );
}
```

## Controlled vs Uncontrolled

### Controlled

```tsx
function ControlledInput() {
  const [value, setValue] = useState('');
  
  return (
    <Input
      value={value}
      onChange={(e) => setValue(e.target.value)}
    />
  );
}
```

### Uncontrolled

```tsx
function UncontrolledInput() {
  const inputRef = useRef<HTMLInputElement>(null);
  
  const handleSubmit = () => {
    console.log(inputRef.current?.value);
  };
  
  return (
    <Input
      ref={inputRef}
      defaultValue=""
    />
  );
}
```

## Form Integration

### Label Association

```tsx
function FormField() {
  const id = useId();
  
  return (
    <div>
      <label htmlFor={id}>Email</label>
      <input
        id={id}
        type="email"
        required
        aria-required="true"
      />
    </div>
  );
}
```

### Fieldset

```tsx
function FormFieldset() {
  return (
    <fieldset>
      <legend>Shipping Address</legend>
      
      <Input label="Street" name="street" />
      <Input label="City" name="city" />
      <Input label="ZIP" name="zip" />
    </fieldset>
  );
}
```

## Autocomplete

```tsx
function Autocomplete({ suggestions }: { suggestions: string[] }) {
  const [value, setValue] = useState('');
  const [filtered, setFiltered] = useState<string[]>([]);
  const [showSuggestions, setShowSuggestions] = useState(false);
  
  useEffect(() => {
    if (value) {
      const matches = suggestions.filter(s =>
        s.toLowerCase().startsWith(value.toLowerCase())
      );
      setFiltered(matches);
      setShowSuggestions(true);
    } else {
      setFiltered([]);
      setShowSuggestions(false);
    }
  }, [value, suggestions]);
  
  return (
    <div className="relative">
      <Input
        value={value}
        onChange={(e) => setValue(e.target.value)}
        aria-autocomplete="list"
        aria-controls="suggestions-list"
      />
      {showSuggestions && filtered.length > 0 && (
        <ul
          id="suggestions-list"
          role="listbox"
          className="absolute z-10 w-full bg-white border rounded shadow-lg"
        >
          {filtered.map((suggestion, index) => (
            <li
              key={index}
              role="option"
              onClick={() => setValue(suggestion)}
            >
              {suggestion}
            </li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

## Accessibility

### Required Fields

```tsx
<Input
  label="Email *"
  required
  aria-required="true"
/>
```

### Error Announcements

```tsx
<Input
  error={error}
  aria-invalid={error ? 'true' : 'false'}
  aria-describedby={error ? 'error-message' : undefined}
/>
```

### Help Text

```tsx
<Input
  helperText="Must be at least 8 characters"
  aria-describedby="help-text"
/>
```

## Best Practices

1. **Always use labels** - Associating labels with inputs
2. **Show validation state** - Visual and programmatic feedback
3. **Clear error messages** - Tell users how to fix errors
4. **Provide help text** - Guide users on expected format
5. **Mark required fields** - Using asterisk and aria-required
6. **Autocomplete attributes** - Helpful for password managers
7. **Focus management** - Visible focus indicators

## Complete Input Component

```tsx
interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label?: string;
  helperText?: string;
  error?: string;
  size?: 'sm' | 'md' | 'lg';
  startIcon?: React.ReactNode;
  endIcon?: React.ReactNode;
}

const Input = forwardRef<HTMLInputElement, InputProps>(
  (
    {
      label,
      helperText,
      error,
      size = 'md',
      startIcon,
      endIcon,
      className,
      id,
      ...props
    },
    ref
  ) => {
    const generatedId = useId();
    const inputId = id || generatedId;
    const hasError = Boolean(error);
    
    const sizeClasses = {
      sm: 'px-2 py-1 text-sm',
      md: 'px-3 py-2 text-base',
      lg: 'px-4 py-3 text-lg',
    };
    
    return (
      <div>
        {label && (
          <label htmlFor={inputId} className="block mb-1 font-medium">
            {label}
            {props.required && <span aria-label="required"> *</span>}
          </label>
        )}
        
        <div className="relative">
          {startIcon && (
            <span className="absolute left-3 top-1/2 -translate-y-1/2">
              {startIcon}
            </span>
          )}
          
          <input
            ref={ref}
            id={inputId}
            className={cn(
              'w-full border rounded transition-colors',
              'focus:outline-none focus:ring-2',
              sizeClasses[size],
              hasError
                ? 'border-red-500 focus:ring-red-500'
                : 'border-gray-300 focus:border-blue-500 focus:ring-blue-500',
              startIcon && 'pl-10',
              endIcon && 'pr-10',
              props.disabled && 'opacity-50 cursor-not-allowed bg-gray-100',
              className
            )}
            aria-invalid={hasError}
            aria-describedby={cn(
              error && `${inputId}-error`,
              helperText && !error && `${inputId}-help`
            )}
            {...props}
          />
          
          {endIcon && (
            <span className="absolute right-3 top-1/2 -translate-y-1/2">
              {endIcon}
            </span>
          )}
        </div>
        
        {helperText && !error && (
          <p id={`${inputId}-help`} className="mt-1 text-sm text-gray-600">
            {helperText}
          </p>
        )}
        
        {error && (
          <p
            id={`${inputId}-error`}
            className="mt-1 text-sm text-red-600"
            role="alert"
          >
            {error}
          </p>
        )}
      </div>
    );
  }
);

Input.displayName = 'Input';
```

## Accessibility References

### ARIA Design Patterns

- [Textbox Pattern - W3C ARIA APG](https://www.w3.org/WAI/ARIA/apg/patterns/textbox/)
- [Form Validation Pattern - W3C ARIA APG](https://www.w3.org/WAI/ARIA/apg/patterns/form/#form-validation)

### Web Standards

- [MDN: HTML input element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input)
- [MDN: textbox role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/textbox_role)
- [MDN: ARIA live regions for announcements](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions)

## Next Steps

- [Button Component](button.md) - Submit buttons
- [Components Overview](README.md) - All components
- [Accessibility Guide](../Accessibility/README.md) - Accessibility patterns
