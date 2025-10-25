# Animations and Motion

Motion and animation bring life to interfaces, providing feedback, guiding attention, and creating delightful interactions.

## Design Philosophy

Think about animations at a higher level, not just isolated effects. Consider the entire interaction ecosystem.

### Key Considerations

#### Realistic vs Abstract

Should motion mimic reality (gravity, physics) or be abstract (smooth, minimal)?

- **Realistic**: Natural physics-based motion (bounces, springs)
  - Use for playful, engaging interactions
- **Abstract**: Smooth, predictable motion
  - Use for professional, trustworthy interfaces

```tsx
// Realistic: Spring-based animation
const springConfig = {
  type: "spring",
  stiffness: 300,
  damping: 30,
};

// Abstract: Easing function
const easeConfig = {
  duration: 300,
  easing: "ease-in-out",
};
```

#### Response Time

How quickly should the component respond after user action?

- **Instant**: Immediate feedback (0-150ms)
- **Quick**: Snappy response (150-300ms)
- **Standard**: Normal interaction (300-500ms)
- **Deliberate**: Slow, intentional (500ms+)

#### Effect Types

What animation effects do we support?

Common effects:

- **Fade**: Opacity transitions
- **Slide**: Position transforms
- **Scale**: Size changes
- **Rotate**: Rotation transforms
- **Spring**: Physics-based motion

## Animation Principles

### 1. Purposeful

Every animation should have a clear purpose:

- Show relationships
- Provide feedback
- Guide attention
- Improve understanding
- Delight users

### 2. Performance

Animations must be smooth and performant:

```css
/* ✅ Good: Use transform and opacity */
.element {
  transform: translateX(100px);
  opacity: 0;
  transition: transform 0.3s, opacity 0.3s;
}

/* ❌ Bad: Triggers layout recalculation */
.element {
  left: 100px;
  transition: left 0.3s;
}
```

### 3. Accessible

Respect user preferences:

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

## Common Animation Patterns

### Micro-interactions

Small animations for immediate feedback:

```tsx
function Button({ children, onClick }: ButtonProps) {
  return (
    <button
      onClick={onClick}
      className="transition-transform active:scale-95 hover:scale-105"
    >
      {children}
    </button>
  );
}
```

### Loading States

Feedback during async operations:

```tsx
function Spinner({ size = 'md' }: { size?: 'sm' | 'md' | 'lg' }) {
  return (
    <div
      className="spinner"
      role="status"
      aria-label="Loading"
    >
      <svg className={`animate-spin ${size}`}>
        {/* SVG content */}
      </svg>
    </div>
  );
}
```

### Enter/Exit Animations

Smooth transitions for mounting/unmounting:

```tsx
import { motion, AnimatePresence } from 'framer-motion';

function Modal({ isOpen, children }: ModalProps) {
  return (
    <AnimatePresence>
      {isOpen && (
        <motion.div
          initial={{ opacity: 0 }}
          animate={{ opacity: 1 }}
          exit={{ opacity: 0 }}
          transition={{ duration: 0.2 }}
        >
          {children}
        </motion.div>
      )}
    </AnimatePresence>
  );
}
```

### Status Changes

Visual feedback for state transitions:

```tsx
function StatusIndicator({ status }: { status: 'idle' | 'success' | 'error' }) {
  return (
    <motion.div
      className={status}
      animate={{
        scale: status === 'success' ? [1, 1.2, 1] : 1,
      }}
      transition={{ duration: 0.3 }}
    >
      {status === 'success' && '✓'}
    </motion.div>
  );
}
```

## Timing and Easing

### Duration Guidelines

- **Micro-interactions**: 100-200ms
- **Standard transitions**: 200-300ms
- **Complex animations**: 300-500ms
- **Page transitions**: 200-400ms

### Easing Functions

```css
/* Common easing functions */

/* Ease out - Best for entrances */
.ease-out {
  transition-timing-function: cubic-bezier(0.16, 1, 0.3, 1);
}

/* Ease in - Best for exits */
.ease-in {
  transition-timing-function: cubic-bezier(0.7, 0, 0.84, 0);
}

/* Ease in-out - Best for transitions */
.ease-in-out {
  transition-timing-function: cubic-bezier(0.87, 0, 0.13, 1);
}

/* Spring (using JavaScript) */
const springConfig = {
  type: 'spring',
  stiffness: 400,
  damping: 25,
};
```

## Implementation Strategies

### CSS Transitions

Simple state changes:

```css
.button {
  background-color: blue;
  transition: background-color 0.2s ease;
}

.button:hover {
  background-color: darkblue;
}
```

### CSS Animations

Complex, reusable animations:

```css
@keyframes slideIn {
  from {
    transform: translateX(-100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

.slide-in {
  animation: slideIn 0.3s ease-out;
}
```

### JavaScript Animations

For complex, interactive animations:

```tsx
import { useSpring, animated } from '@react-spring/web';

function AnimatedCard() {
  const [isHovered, setIsHovered] = useState(false);
  
  const props = useSpring({
    transform: isHovered ? 'scale(1.05)' : 'scale(1)',
    config: { tension: 300, friction: 25 },
  });
  
  return (
    <animated.div
      style={props}
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      Card Content
    </animated.div>
  );
}
```

## Motion Tokens

Define standard animation values:

```typescript
export const animations = {
  duration: {
    fast: '100ms',
    normal: '200ms',
    slow: '300ms',
    slower: '500ms',
  },
  easing: {
    easeOut: 'cubic-bezier(0.16, 1, 0.3, 1)',
    easeIn: 'cubic-bezier(0.7, 0, 0.84, 0)',
    easeInOut: 'cubic-bezier(0.87, 0, 0.13, 1)',
  },
  spring: {
    default: { tension: 400, friction: 25 },
    gentle: { tension: 200, friction: 30 },
    bouncy: { tension: 300, friction: 15 },
  },
};
```

## Best Practices

### 1. Start Subtle

Begin with subtle animations and increase only if needed.

### 2. Consistent Language

Use the same animation patterns across the system.

### 3. Performance First

Prefer CSS over JavaScript when possible. Use `transform` and `opacity`.

### 4. Accessibility Always

Always respect `prefers-reduced-motion`.

### 5. Test on Devices

Animations perform differently on various devices.

### 6. Document Decisions

Explain why specific animations are used.

## Common Patterns

### Dropdown Menus

```tsx
function Dropdown({ isOpen, children }: DropdownProps) {
  return (
    <motion.div
      initial="closed"
      animate={isOpen ? 'open' : 'closed'}
      variants={{
        open: { opacity: 1, y: 0 },
        closed: { opacity: 0, y: -10 },
      }}
    >
      {children}
    </motion.div>
  );
}
```

### Toast Notifications

```tsx
function Toast({ message }: { message: string }) {
  return (
    <motion.div
      initial={{ x: 300, opacity: 0 }}
      animate={{ x: 0, opacity: 1 }}
      exit={{ x: 300, opacity: 0 }}
    >
      {message}
    </motion.div>
  );
}
```

### Page Transitions

```tsx
const pageVariants = {
  initial: { opacity: 0 },
  in: { opacity: 1 },
  out: { opacity: 0 },
};

const pageTransition = {
  type: 'tween',
  ease: 'anticipate',
  duration: 0.5,
};
```

## Resources

- [Framer Motion](https://www.framer.com/motion/) - React animation library
- [React Spring](https://www.react-spring.dev/) - Spring-physics based animations
- [Motion Design Principles](https://material.io/design/motion/understanding-motion.html) - Material Design motion guide

## Next Steps

- [Components](Components/README.md) - Animated components
- [Assets](Assets/README.md) - Loading animations
- [Accessibility](Accessibility/README.md) - Respecting reduced motion
