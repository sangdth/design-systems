# Component Generator Pattern

A component generator transforms token-based props into CSS values, enabling flexible, token-driven component APIs.

## Concept

Instead of accepting raw CSS values, components accept semantic token names that get resolved to actual values.

```tsx
// ❌ Without generator (raw values)
<Box padding="16px" margin="8px" />

// ✅ With generator (tokens)
<Box padding="md" margin="sm" />
```

## Basic Implementation

```typescript
// Token definitions
const tokens = {
  spacing: {
    xs: '4px',
    sm: '8px',
    md: '16px',
    lg: '24px',
    xl: '32px',
  },
  color: {
    primary: '#0066cc',
    secondary: '#6c757d',
    background: '#ffffff',
  },
};

// Generator function
function resolveToken(path: string): string {
  const parts = path.split('.');
  let value: any = tokens;
  
  for (const part of parts) {
    value = value[part];
    if (value === undefined) return '';
  }
  
  return typeof value === 'string' ? value : '';
}

// Usage
resolveToken('spacing.md');    // '16px'
resolveToken('color.primary'); // '#0066cc'
```

## Component Implementation

### Simple Generator

```tsx
interface BoxProps {
  padding?: string;
  margin?: string;
  color?: string;
  children: React.ReactNode;
}

function Box({ padding, margin, color, children }: BoxProps) {
  const getToken = (path: string) => {
    const tokens = {
      spacing: { xs: '4px', sm: '8px', md: '16px' },
      color: { primary: '#0066cc', secondary: '#6c757d' },
    };
    
    const parts = path.split('.');
    let value: any = tokens;
    
    for (const part of parts) {
      value = value?.[part];
      if (!value) return '';
    }
    
    return value;
  };
  
  return (
    <div
      style={{
        padding: padding && getToken(`spacing.${padding}`),
        margin: margin && getToken(`spacing.${margin}`),
        color: color && getToken(`color.${color}`),
      }}
    >
      {children}
    </div>
  );
}

// Usage
<Box padding="md" margin="sm" color="primary">
  Content
</Box>
```

### Advanced Generator with TypeScript

```typescript
type TokenPath =
  | `spacing.${'xs' | 'sm' | 'md' | 'lg' | 'xl'}`
  | `color.${'primary' | 'secondary' | 'background'}`;

function resolveToken(path: TokenPath): string {
  const tokens = {
    spacing: {
      xs: '4px', sm: '8px', md: '16px', lg: '24px', xl: '32px',
    },
    color: {
      primary: '#0066cc',
      secondary: '#6c757d',
      background: '#ffffff',
    },
  };
  
  const [category, key] = path.split('.') as [keyof typeof tokens, string];
  return (tokens[category] as any)?.[key] || '';
}

// Type-safe usage
resolveToken('spacing.md');    // ✅ Valid
resolveToken('spacing.huge');  // ❌ TypeScript error
```

## Usage Examples

### Style Props Component

```tsx
interface StyledBoxProps {
  padding?: 'xs' | 'sm' | 'md' | 'lg';
  margin?: 'xs' | 'sm' | 'md' | 'lg';
  backgroundColor?: 'primary' | 'secondary' | 'background';
  borderRadius?: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
}

function StyledBox({
  padding = 'md',
  margin = 'xs',
  backgroundColor = 'background',
  borderRadius = 'sm',
  children,
}: StyledBoxProps) {
  const tokens = {
    spacing: { xs: '4px', sm: '8px', md: '16px', lg: '24px' },
    color: { primary: '#0066cc', secondary: '#6c757d', background: '#ffffff' },
    radius: { sm: '4px', md: '8px', lg: '12px' },
  };
  
  return (
    <div
      style={{
        padding: tokens.spacing[padding],
        margin: tokens.spacing[margin],
        backgroundColor: tokens.color[backgroundColor],
        borderRadius: tokens.radius[borderRadius],
      }}
    >
      {children}
    </div>
  );
}

// Usage
<StyledBox 
  padding="lg" 
  margin="md" 
  backgroundColor="primary"
  borderRadius="lg"
>
  Content
</StyledBox>
```

## With Styled Components

```tsx
import styled from 'styled-components';

const Button = styled.button<{
  size?: 'sm' | 'md' | 'lg';
  variant?: 'primary' | 'secondary';
}>`
  padding: ${props => {
    const tokens = { sm: '8px 12px', md: '12px 16px', lg: '16px 24px' };
    return tokens[props.size || 'md'];
  }};
  
  background-color: ${props => {
    const tokens = { primary: '#0066cc', secondary: '#6c757d' };
    return tokens[props.variant || 'primary'];
  }};
  
  font-size: ${props => {
    const tokens = { sm: '14px', md: '16px', lg: '18px' };
    return tokens[props.size || 'md'];
  }};
`;

// Usage
<Button size="lg" variant="primary">
  Click me
</Button>
```

## Hook-based Approach

```tsx
function useTokens() {
  const tokens = {
    spacing: { xs: '4px', sm: '8px', md: '16px', lg: '24px' },
    color: { primary: '#0066cc', secondary: '#6c757d' },
  };
  
  const getToken = useCallback((path: string) => {
    const [category, key] = path.split('.');
    return (tokens as any)[category]?.[key] || '';
  }, []);
  
  return { getToken, tokens };
}

// Usage in component
function MyComponent() {
  const { getToken } = useTokens();
  
  return (
    <div style={{ padding: getToken('spacing.md') }}>
      Content
    </div>
  );
}
```

## Best Practices

1. **Type safety**: Use TypeScript to ensure token paths are valid
2. **Centralize tokens**: Keep all tokens in one place
3. **Documentation**: Provide autocomplete and documentation for token values
4. **Performance**: Cache token lookups when needed
5. **Fallbacks**: Provide sensible defaults for missing tokens

## Real-World Example

```typescript
// tokens.ts
export const tokens = {
  spacing: { xs: '4px', sm: '8px', md: '16px', lg: '24px', xl: '32px' },
  color: { primary: '#0066cc', secondary: '#6c757d', background: '#ffffff' },
  fontSize: { sm: '14px', md: '16px', lg: '18px', xl: '20px' },
  borderRadius: { sm: '4px', md: '8px', lg: '12px' },
};

// useTokens.ts
export function useTokens() {
  return tokens;
}

// Component.tsx
import { useTokens } from './useTokens';

export function Card({ padding, ...props }: CardProps) {
  const tokens = useTokens();
  
  return (
    <div 
      style={{ 
        padding: padding ? tokens.spacing[padding] : tokens.spacing.md 
      }}
      {...props}
    />
  );
}
```

## Next Steps

- [Tokens Overview](README.md) - Design tokens introduction
- [Components](../Components/README.md) - Building with tokens
- [Spacing](spacing.md) - Spacing token system
