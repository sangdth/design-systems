# Layout Components

Layout components provide structure and spacing for content organization. They establish visual hierarchy and relationships between elements.

## Container

Wraps content with max-width and centering.

```tsx
interface ContainerProps {
  size?: 'sm' | 'md' | 'lg' | 'xl' | 'full';
  children: React.ReactNode;
}

const containerSizes = {
  sm: 'max-w-screen-sm',
  md: 'max-w-screen-md',
  lg: 'max-w-screen-lg',
  xl: 'max-w-screen-xl',
  full: 'max-w-full',
};

function Container({ size = 'md', children }: ContainerProps) {
  return (
    <div className={cn('mx-auto px-4', containerSizes[size])}>
      {children}
    </div>
  );
}
```

## Stack

Vertical stacking with consistent spacing.

```tsx
interface StackProps {
  spacing?: 'xs' | 'sm' | 'md' | 'lg' | 'xl';
  align?: 'start' | 'center' | 'end' | 'stretch';
  children: React.ReactNode;
}

const spacingMap = {
  xs: 'space-y-1',
  sm: 'space-y-2',
  md: 'space-y-4',
  lg: 'space-y-6',
  xl: 'space-y-8',
};

function Stack({
  spacing = 'md',
  align = 'stretch',
  children,
}: StackProps) {
  return (
    <div
      className={cn(
        spacingMap[spacing],
        `items-${align}`
      )}
    >
      {children}
    </div>
  );
}
```

## Flex

Flexbox layout with alignment options.

```tsx
interface FlexProps {
  direction?: 'row' | 'column';
  wrap?: 'wrap' | 'nowrap';
  justify?: 'start' | 'center' | 'end' | 'space-between' | 'space-around';
  align?: 'start' | 'center' | 'end' | 'stretch' | 'baseline';
  gap?: string;
  children: React.ReactNode;
}

function Flex({
  direction = 'row',
  wrap = 'nowrap',
  justify = 'start',
  align = 'start',
  gap = '0',
  children,
  ...props
}: FlexProps) {
  return (
    <div
      style={{
        display: 'flex',
        flexDirection: direction,
        flexWrap: wrap,
        justifyContent: justify,
        alignItems: align,
        gap,
      }}
      {...props}
    >
      {children}
    </div>
  );
}
```

## Grid

CSS Grid layout system.

```tsx
interface GridProps {
  columns?: number | string;
  gap?: string;
  children: React.ReactNode;
}

function Grid({ columns = 12, gap = '1rem', children }: GridProps) {
  return (
    <div
      style={{
        display: 'grid',
        gridTemplateColumns: typeof columns === 'number'
          ? `repeat(${columns}, 1fr)`
          : columns,
        gap,
      }}
    >
      {children}
    </div>
  );
}

// Usage
<Grid columns={3} gap="24px">
  <Card>Item 1</Card>
  <Card>Item 2</Card>
  <Card>Item 3</Card>
</Grid>
```

## GridItem

Individual grid item with span controls.

```tsx
interface GridItemProps {
  column?: number | string;
  row?: number | string;
  children: React.ReactNode;
}

function GridItem({
  column,
  row,
  children,
  ...props
}: GridItemProps) {
  return (
    <div
      style={{
        gridColumn: column,
        gridRow: row,
      }}
      {...props}
    >
      {children}
    </div>
  );
}
```

## Simple Grid Example

```tsx
<Grid columns="repeat(auto-fit, minmax(250px, 1fr))" gap="20px">
  <Card>Card 1</Card>
  <Card>Card 2</Card>
  <Card>Card 3</Card>
</Grid>
```

## Spacer

Adds flexible space between elements.

```tsx
interface SpacerProps {
  size?: number | string;
}

function Spacer({ size = 'auto' }: SpacerProps) {
  return <div style={{ flex: typeof size === 'number' ? size : size }} />;
}

// Usage
<Flex>
  <Button>Left</Button>
  <Spacer />
  <Button>Right</Button>
</Flex>
```

## Box

Generic container with style props.

```tsx
interface BoxProps {
  padding?: string;
  margin?: string;
  width?: string;
  height?: string;
  backgroundColor?: string;
  children: React.ReactNode;
}

function Box({
  padding,
  margin,
  width,
  height,
  backgroundColor,
  children,
  ...props
}: BoxProps) {
  return (
    <div
      style={{
        padding,
        margin,
        width,
        height,
        backgroundColor,
      }}
      {...props}
    >
      {children}
    </div>
  );
}
```

## Responsive Layout

### Breakpoint-Based Layouts

```tsx
interface ResponsiveLayoutProps {
  columns: number | { sm?: number; md?: number; lg?: number };
  gap?: string;
  children: React.ReactNode;
}

function ResponsiveLayout({ columns, gap = '1rem', children }: ResponsiveLayoutProps) {
  const gridTemplateColumns = typeof columns === 'number'
    ? `repeat(${columns}, 1fr)`
    : `repeat(${columns.sm || 1}, 1fr)`;
  
  return (
    <div
      className="responsive-grid"
      style={{
        display: 'grid',
        gridTemplateColumns,
        gap,
      }}
    >
      {children}
    </div>
  );
}
```

### Media Query Approach

```tsx
function ResponsiveStack({ children }: { children: React.ReactNode }) {
  return (
    <div className="responsive-stack">
      {children}
    </div>
  );
}
```

```css
.responsive-stack {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

@media (min-width: 768px) {
  .responsive-stack {
    flex-direction: row;
  }
}
```

## Split Screen

Two-column layout pattern.

```tsx
interface SplitScreenProps {
  leftWeight?: number;
  rightWeight?: number;
  left: React.ReactNode;
  right: React.ReactNode;
}

function SplitScreen({
  leftWeight = 1,
  rightWeight = 1,
  left,
  right,
}: SplitScreenProps) {
  const totalWeight = leftWeight + rightWeight;
  
  return (
    <Flex>
      <div style={{ flex: leftWeight / totalWeight }}>
        {left}
      </div>
      <div style={{ flex: rightWeight / totalWeight }}>
        {right}
      </div>
    </Flex>
  );
}

// Usage
<SplitScreen leftWeight={1} rightWeight={2}>
  <Navigation />
  <MainContent />
</SplitScreen>
```

## Aspect Ratio Container

Maintains consistent aspect ratios for media.

```tsx
interface AspectRatioProps {
  ratio?: string; // e.g., "16/9", "4/3", "1/1"
  children: React.ReactNode;
}

function AspectRatio({ ratio = '16/9', children }: AspectRatioProps) {
  const [width, height] = ratio.split('/').map(Number);
  const aspectRatio = width / height;
  
  return (
    <div
      style={{
        position: 'relative',
        paddingTop: `${(1 / aspectRatio) * 100}%`,
      }}
    >
      <div
        style={{
          position: 'absolute',
          top: 0,
          left: 0,
          right: 0,
          bottom: 0,
        }}
      >
        {children}
      </div>
    </div>
  );
}
```

## Center

Horizontally and vertically centers content.

```tsx
interface CenterProps {
  children: React.ReactNode;
}

function Center({ children }: CenterProps) {
  return (
    <div
      style={{
        display: 'flex',
        alignItems: 'center',
        justifyContent: 'center',
        minHeight: '100%',
      }}
    >
      {children}
    </div>
  );
}
```

## Full-Height Layout

Classic app layout structure.

```tsx
function AppLayout({
  header,
  sidebar,
  main,
  footer,
}: AppLayoutProps) {
  return (
    <div className="app-layout">
      <header>{header}</header>
      <div className="app-body">
        {sidebar && <aside>{sidebar}</aside>}
        <main>{main}</main>
      </div>
      {footer && <footer>{footer}</footer>}
    </div>
  );
}
```

```css
.app-layout {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.app-body {
  display: flex;
  flex: 1;
  gap: 2rem;
}

.app-body main {
  flex: 1;
}

.app-body aside {
  width: 250px;
}
```

## Best Practices

1. **Consistent spacing** - Use spacing tokens
2. **Responsive by default** - Mobile-first approach
3. **Semantic HTML** - Use proper HTML5 elements
4. **Accessible structure** - Maintain logical order
5. **Performance** - Minimize layout shifts
6. **Flexibility** - Allow customization through props

## Layout Composition Example

```tsx
function PageLayout() {
  return (
    <Container size="lg">
      <Stack spacing="xl">
        <Header />
        
        <Flex justify="space-between" gap="2rem">
          <main style={{ flex: 2 }}>
            <article>
              <h1>Article Title</h1>
              <p>Article content...</p>
            </article>
          </main>
          
          <aside style={{ flex: 1 }}>
            <Sidebar />
          </aside>
        </Flex>
        
        <Footer />
      </Stack>
    </Container>
  );
}
```

## Accessibility References

### ARIA Design Patterns

- [Landmarks Pattern - W3C ARIA APG](https://www.w3.org/WAI/ARIA/apg/patterns/landmarks/)
- [Navigation Pattern - W3C ARIA APG](https://www.w3.org/WAI/ARIA/apg/patterns/navigation/)
- [Grid Pattern - W3C ARIA APG](https://www.w3.org/WAI/ARIA/apg/patterns/grid/)

### Web Standards

- [MDN: HTML5 Semantic Elements](https://developer.mozilla.org/en-US/docs/Glossary/Semantics#semantic_elements)
- [MDN: main role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/main_role)
- [MDN: navigation role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/navigation_role)
- [MDN: complementary role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/complementary_role)

## Next Steps

- [Components Overview](README.md) - All components
- [Polymorphic Components](Polymorphic%20component.md) - Dynamic rendering
- [Accessibility Guide](../Accessibility/README.md) - Layout accessibility

