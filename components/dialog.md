# Dialog Component

Dialogs (modals) overlay content and require user interaction. They focus attention on critical information or actions.

## Native HTML Dialog Element

The `<dialog>` element provides built-in modal functionality with accessibility features.

```html
<dialog>
  <p>This is a dialog</p>
  <form method="dialog">
    <button>Close</button>
  </form>
</dialog>
```

### Using Native Dialog

```tsx
function NativeDialog() {
  const dialogRef = useRef<HTMLDialogElement>(null);
  
  const openDialog = () => dialogRef.current?.showModal();
  const closeDialog = () => dialogRef.current?.close();
  
  return (
    <>
      <button onClick={openDialog}>Open Dialog</button>
      
      <dialog ref={dialogRef}>
        <h2>Dialog Title</h2>
        <p>Dialog content goes here.</p>
        <button onClick={closeDialog}>Close</button>
      </dialog>
    </>
  );
}
```

### Built-in Features

- `showModal()` - Opens as modal (with backdrop, focus trap)
- `show()` - Opens non-modal
- `close()` - Closes dialog
- Returns `returnValue` to opener
- Automatic focus management
- `aria-modal="true"` is implicit when using `showModal()`

## Custom Dialog Implementation

Build custom dialogs with full control.

```tsx
interface DialogProps {
  open: boolean;
  onClose: () => void;
  title?: string;
  children: React.ReactNode;
}

function Dialog({ open, onClose, title, children }: DialogProps) {
  const dialogRef = useRef<HTMLDivElement>(null);
  
  // Focus management
  useEffect(() => {
    if (open && dialogRef.current) {
      dialogRef.current.focus();
    }
  }, [open]);
  
  // Escape key handling
  useEffect(() => {
    const handleEscape = (e: KeyboardEvent) => {
      if (e.key === 'Escape') onClose();
    };
    
    if (open) {
      document.addEventListener('keydown', handleEscape);
      return () => document.removeEventListener('keydown', handleEscape);
    }
  }, [open, onClose]);
  
  // Focus trap
  useEffect(() => {
    if (!open) return;
    
    const focusableElements = dialogRef.current?.querySelectorAll(
      'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
    );
    
    const firstElement = focusableElements?.[0] as HTMLElement;
    const lastElement = focusableElements?.[focusableElements.length - 1] as HTMLElement;
    
    const handleTab = (e: KeyboardEvent) => {
      if (e.key !== 'Tab') return;
      
      if (e.shiftKey) {
        if (document.activeElement === firstElement) {
          lastElement?.focus();
          e.preventDefault();
        }
      } else {
        if (document.activeElement === lastElement) {
          firstElement?.focus();
          e.preventDefault();
        }
      }
    };
    
    document.addEventListener('keydown', handleTab);
    firstElement?.focus();
    
    return () => document.removeEventListener('keydown', handleTab);
  }, [open]);
  
  if (!open) return null;
  
  return (
    <div
      role="dialog"
      aria-modal="true"
      aria-labelledby={title ? 'dialog-title' : undefined}
      ref={dialogRef}
      tabIndex={-1}
    >
      {/* Backdrop */}
      <div onClick={onClose} />
      
      {/* Dialog Content */}
      <div>
        {title && <h2 id="dialog-title">{title}</h2>}
        {children}
        <button onClick={onClose}>Close</button>
      </div>
    </div>
  );
}
```

## Dialog Variants

### Alert Dialog

Confirms critical actions.

```tsx
interface AlertDialogProps {
  open: boolean;
  onConfirm: () => void;
  onCancel: () => void;
  title: string;
  message: string;
  confirmText?: string;
  cancelText?: string;
}

function AlertDialog({
  open,
  onConfirm,
  onCancel,
  title,
  message,
  confirmText = 'Confirm',
  cancelText = 'Cancel',
}: AlertDialogProps) {
  return (
    <Dialog open={open} onClose={onCancel} title={title}>
      <p>{message}</p>
      <div>
        <button onClick={onCancel}>{cancelText}</button>
        <button onClick={onConfirm}>{confirmText}</button>
      </div>
    </Dialog>
  );
}
```

### Form Dialog

Contains a form.

```tsx
function FormDialog({ open, onClose, onSubmit }: FormDialogProps) {
  return (
    <Dialog open={open} onClose={onClose} title="Create User">
      <form onSubmit={onSubmit}>
        <input name="name" placeholder="Name" required />
        <input name="email" type="email" placeholder="Email" required />
        <div>
          <button type="button" onClick={onClose}>Cancel</button>
          <button type="submit">Create</button>
        </div>
      </form>
    </Dialog>
  );
}
```

### Scrollable Content Dialog

Handles long content.

```tsx
function ScrollableDialog({ open, onClose, title, children }: DialogProps) {
  return (
    <Dialog open={open} onClose={onClose} title={title}>
      <div style={{ maxHeight: '70vh', overflowY: 'auto' }}>
        {children}
      </div>
    </Dialog>
  );
}
```

## Accessibility Requirements

### ARIA Attributes

```tsx
<div
  role="dialog"
  aria-modal="true"
  aria-labelledby="dialog-title"
  aria-describedby="dialog-description"
>
```

### Focus Management

1. **On Open**: Focus first focusable element
2. **Focus Trap**: Keep focus within dialog
3. **On Close**: Return focus to trigger element

### Keyboard Navigation

- **Escape**: Close dialog
- **Tab**: Navigate within (with wrapping)
- **Shift+Tab**: Navigate backwards (with wrapping)

### Screen Reader Announcing

```tsx
function useDialogAnnouncement(message: string) {
  const [announcement, setAnnouncement] = useState('');
  
  useEffect(() => {
    const timer = setTimeout(() => setAnnouncement(message), 100);
    return () => clearTimeout(timer);
  }, [message]);
  
  return (
    <div role="status" aria-live="polite" className="sr-only">
      {announcement}
    </div>
  );
}
```

## Backdrop Behavior

### Click Outside to Close

```tsx
function Backdrop({ onClick }: { onClick: () => void }) {
  return <div onClick={onClick} />;
}
```

### Prevent Closing

Some dialogs shouldn't close on backdrop click (e.g., critical confirmations).

```tsx
<Dialog open={open} onClose={onClose} closeOnBackdropClick={false}>
```

## Animations

### Fade In/Out

```css
.dialog-backdrop {
  animation: fadeIn 0.2s ease-out;
}

.dialog-content {
  animation: slideUp 0.3s ease-out;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

### Respect Reduced Motion

```css
@media (prefers-reduced-motion: reduce) {
  .dialog-backdrop,
  .dialog-content {
    animation: none;
  }
}
```

## Best Practices

1. **Keep content focused** - Don't overload dialogs
2. **Clear actions** - Make confirmation buttons obvious
3. **Proper labels** - Title dialogs clearly
4. **Size appropriately** - Don't make dialogs too large
5. **Stacking** - Be careful with nested dialogs
6. **Loading states** - Show loading during async operations
7. **Error handling** - Display errors within dialog

## Examples

### Confirmation Dialog

```tsx
function DeleteConfirmation({ 
  open, 
  onConfirm, 
  onCancel 
}: ConfirmationDialogProps) {
  return (
    <AlertDialog
      open={open}
      onConfirm={onConfirm}
      onCancel={onCancel}
      title="Delete Item"
      message="Are you sure you want to delete this item? This action cannot be undone."
      confirmText="Delete"
      cancelText="Cancel"
    />
  );
}
```

### Multi-Step Dialog

```tsx
function MultiStepDialog() {
  const [step, setStep] = useState(1);
  
  return (
    <Dialog open={open} onClose={onClose}>
      {step === 1 && <StepOne onNext={() => setStep(2)} />}
      {step === 2 && (
        <>
          <StepTwo onNext={() => setStep(3)} onBack={() => setStep(1)} />
        </>
      )}
      {step === 3 && <StepThree onBack={() => setStep(2)} />}
    </Dialog>
  );
}
```

## Accessibility References

### ARIA Design Patterns

- [Dialog (Modal) Pattern - W3C ARIA APG](https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/)
- [Alertdialog Pattern - W3C ARIA APG](https://www.w3.org/WAI/ARIA/apg/patterns/alertdialog/)

### Web Standards

- [MDN: HTML dialog element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog)
- [MDN: dialog role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/dialog_role)
- [MDN: alertdialog role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/alertdialog_role)
- [MDN: Keyboard navigation and focus](https://developer.mozilla.org/en-US/docs/Web/Accessibility/Keyboard-navigable_JavaScript_widgets)

## Next Steps

- [Button Component](button.md) - Button interactions
- [Accessibility Guide](../Accessibility/README.md) - Accessibility patterns
- [Components Overview](README.md) - All components
