# Advanced React Patterns

Effective patterns for building flexible, maintainable components in design systems.

## Layout Components

### Separation of Concerns

- Split screen
- Lists
- Modals
- etc.

The core content components should be **unaware** and **unconcerned** about their precise location within the page.

**Principle:** Components should only care about their own styling and behavior, not their layout context.

```tsx
// ✅ Good: Component doesn't care where it's placed
const Card = ({ children }) => (
  <div className="card">{children}</div>
);

// Usage: Layout handles positioning
<div className="grid grid-cols-2 gap-4">
  <Card>Item 1</Card>
  <Card>Item 2</Card>
</div>

// ❌ Bad: Component tries to control its layout
const BadCard = ({ children }) => (
  <div className="card" style={{ marginBottom: '1rem' }}>
    {children}
  </div>
);
```

## Design Patterns

Effective solutions for common design system challenges.

### Container Components

Container components handle data fetching and state management, while presentational components handle rendering.

```tsx
// Container: Fetches data and manages state
const UserListContainer = () => {
  const [users, setUsers] = useState([]);
  
  useEffect(() => {
    fetchUsers().then(setUsers);
  }, []);
  
  return <UserList users={users} />;
};

// Presentational: Pure rendering component
const UserList = ({ users }) => (
  <ul>
    {users.map(user => (
      <li key={user.id}>{user.name}</li>
    ))}
  </ul>
);
```

### Compound Components

When we need to pass complex content into different places inside the container, we should think about compound components.

**Benefits:**

- Flexible API
- Better composition
- Reduced prop drilling

**Example: Card with Header, Body, Footer**

```tsx
// Compound component pattern
const Card = ({ children, ...props }) => (
  <div className="card" {...props}>
    {children}
  </div>
);

Card.Header = ({ children, ...props }) => (
  <header className="card-header" {...props}>
    {children}
  </header>
);

Card.Body = ({ children, ...props }) => (
  <div className="card-body" {...props}>
    {children}
  </div>
);

Card.Footer = ({ children, ...props }) => (
  <footer className="card-footer" {...props}>
    {children}
  </footer>
);

// Usage
<Card>
  <Card.Header>
    <h3>Card Title</h3>
  </Card.Header>
  <Card.Body>
    <p>Card content goes here</p>
  </Card.Body>
  <Card.Footer>
    <Button>Action</Button>
  </Card.Footer>
</Card>
```

**Example: Sidebar**

```tsx
const Sidebar = ({ children }) => (
  <aside className="sidebar">
    {children}
  </aside>
);

Sidebar.Header = ({ title }) => (
  <header className="sidebar-header">
    <h2>{title}</h2>
  </header>
);

Sidebar.Menu = ({ children }) => (
  <nav className="sidebar-menu">
    {children}
  </nav>
);

// Usage
<Sidebar>
  <Sidebar.Header title="Navigation" />
  <Sidebar.Menu>
    <NavLink to="/dashboard">Dashboard</NavLink>
    <NavLink to="/settings">Settings</NavLink>
  </Sidebar.Menu>
</Sidebar>
```

**Advanced: Context-Based Compound Components**

```tsx
const CardContext = createContext();

const Card = ({ children, variant = 'default' }) => {
  const [isExpanded, setIsExpanded] = useState(false);
  
  return (
    <CardContext.Provider value={{ variant, isExpanded, setIsExpanded }}>
      <div className={`card card--${variant}`}>
        {children}
      </div>
    </CardContext.Provider>
  );
};

const CardHeader = ({ children }) => {
  const { setIsExpanded, isExpanded } = useContext(CardContext);
  
  return (
    <header className="card-header">
      {children}
      <button onClick={() => setIsExpanded(!isExpanded)}>
        {isExpanded ? 'Collapse' : 'Expand'}
      </button>
    </header>
  );
};

Card.Header = CardHeader;
```

### Observer Components

Emit => Subscribe pattern for global state management.

**Example: Toast Notifications**

```tsx
// Context for toast management
const ToastContext = createContext();

const ToastProvider = ({ children }) => {
  const [toasts, setToasts] = useState([]);
  
  const addToast = (message, type = 'info') => {
    const id = Math.random().toString(36);
    setToasts(prev => [...prev, { id, message, type }]);
    
    setTimeout(() => {
      removeToast(id);
    }, 5000);
  };
  
  const removeToast = (id) => {
    setToasts(prev => prev.filter(toast => toast.id !== id));
  };
  
  return (
    <ToastContext.Provider value={{ toasts, addToast, removeToast }}>
      {children}
      <ToastContainer toasts={toasts} />
    </ToastContext.Provider>
  );
};

// Hook for components
const useToast = () => {
  const context = useContext(ToastContext);
  if (!context) {
    throw new Error('useToast must be used within ToastProvider');
  }
  return context;
};

// Usage in component
const MyComponent = () => {
  const { addToast } = useToast();
  
  const handleClick = () => {
    addToast('Item saved successfully!', 'success');
  };
  
  return <Button onClick={handleClick}>Save</Button>;
};
```

## Render Props Pattern

Share code between components using a prop whose value is a function.

```tsx
const Mouse = ({ render }) => {
  const [mouse, setMouse] = useState({ x: 0, y: 0 });
  
  useEffect(() => {
    const handleMouseMove = (e) => {
      setMouse({ x: e.clientX, y: e.clientY });
    };
    
    window.addEventListener('mousemove', handleMouseMove);
    return () => window.removeEventListener('mousemove', handleMouseMove);
  }, []);
  
  return render(mouse);
};

// Usage
<Mouse render={({ x, y }) => (
  <div>Mouse position: {x}, {y}</div>
)} />
```

## Higher-Order Components (HOCs)

Transform a component into another component.

```tsx
const withLoading = (Component) => {
  return function WrappedComponent(props) {
    const [loading, setLoading] = useState(true);
    
    useEffect(() => {
      // Simulate loading
      setTimeout(() => setLoading(false), 1000);
    }, []);
    
    if (loading) {
      return <div>Loading...</div>;
    }
    
    return <Component {...props} />;
  };
};

const UserProfile = ({ user }) => (
  <div>User: {user.name}</div>
);

const UserProfileWithLoading = withLoading(UserProfile);
```

## Custom Hooks Pattern

Extract component logic into reusable functions.

```tsx
// Custom hook for form state
const useForm = (initialValues) => {
  const [values, setValues] = useState(initialValues);
  
  const handleChange = (e) => {
    setValues({
      ...values,
      [e.target.name]: e.target.value,
    });
  };
  
  const handleSubmit = (onSubmit) => (e) => {
    e.preventDefault();
    onSubmit(values);
  };
  
  return { values, handleChange, handleSubmit };
};

// Usage
const LoginForm = () => {
  const { values, handleChange, handleSubmit } = useForm({
    email: '',
    password: '',
  });
  
  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      <input
        name="email"
        value={values.email}
        onChange={handleChange}
      />
      <input
        name="password"
        type="password"
        value={values.password}
        onChange={handleChange}
      />
      <button type="submit">Submit</button>
    </form>
  );
};
```

## Performance Patterns

### React.memo

Prevent unnecessary re-renders.

```tsx
const ExpensiveComponent = React.memo(({ data }) => {
  return <div>{/* Expensive rendering */}</div>;
}, (prevProps, nextProps) => {
  // Custom comparison
  return prevProps.data.id === nextProps.data.id;
});
```

### useMemo and useCallback

Memoize expensive calculations and callbacks.

```tsx
const ExpensiveList = ({ items, searchTerm }) => {
  const filteredItems = useMemo(() => {
    return items.filter(item => 
      item.name.includes(searchTerm)
    );
  }, [items, searchTerm]);
  
  const handleItemClick = useCallback((itemId) => {
    console.log('Item clicked:', itemId);
  }, []);
  
  return (
    <ul>
      {filteredItems.map(item => (
        <li key={item.id} onClick={() => handleItemClick(item.id)}>
          {item.name}
        </li>
      ))}
    </ul>
  );
};
```

## Resources

- [React Patterns](https://reactpatterns.com/)
- [Advanced React Patterns](https://kentcdodds.com/blog/compound-components-with-react-hooks)

## Next Steps

- [Component Patterns](../Components/component-patterns-and-apis.md) - Core patterns
- [Polymorphic Components](../Components/Polymorphic%20component.md) - Flexible components
