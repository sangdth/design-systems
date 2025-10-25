# Design System Methodology

Choosing the right methodology for organizing your design system impacts maintainability, scalability, and team collaboration.

## Atomic Design

Popularized by [Brad Frost](https://atomicdesign.bradfrost.com/chapter-2), Atomic Design breaks down interfaces into hierarchical levels.

### The Five Levels

#### 1. Atoms

The most fundamental building units that can't be broken down any further.

**Examples:**

- Form labels
- Inputs
- Buttons
- Icons
- Headings

**Code Example:**

```tsx
// Atom: Button
export const Button = ({ children, ...props }) => (
  <button {...props}>{children}</button>
);
```

#### 2. Molecules

Small UI elements that work together, formed from atoms.

**Examples:**

- Search form (input + button)
- Navigation item (icon + text)
- Form field (label + input + error)

**Code Example:**

```tsx
// Molecule: Search Form
export const SearchForm = () => (
  <form>
    <Input placeholder="Search..." />
    <Button>Search</Button>
  </form>
);
```

#### 3. Organisms

Complex UI components made up of groups of atoms and molecules.

**Examples:**

- Header (logo + navigation + search form)
- Form (multiple form fields + submit button)
- Card (image + title + description + actions)

**Code Example:**

```tsx
// Organism: Header
export const Header = () => (
  <header>
    <Logo />
    <Navigation items={navItems} />
    <SearchForm />
    <UserMenu />
  </header>
);
```

#### 4. Templates

Page-level layouts that combine organisms.

**Code Example:**

```tsx
// Template: Dashboard
export const DashboardTemplate = () => (
  <Layout>
    <Header />
    <Sidebar />
    <MainContent>
      <StatsGrid />
      <RecentActivity />
    </MainContent>
    <Footer />
  </Layout>
);
```

#### 5. Pages

Specific instances of templates with real content.

**Code Example:**

```tsx
// Page: User Dashboard
export const UserDashboardPage = () => (
  <DashboardTemplate 
    user={currentUser}
    stats={userStats}
    activity={recentActivity}
  />
);
```

### Benefits of Atomic Design

- **Clear Hierarchy**: Easy to understand component relationships
- **Reusability**: Build complex components from simple ones
- **Consistency**: Standardized components across the system
- **Collaboration**: Common language between designers and developers

## Alternative Methodologies

### CUBE CSS

Component Utility Block Element by Andy Bell.

**Principles:**

1. **Composition**: Build from utilities
2. **Utility**: Small, single-purpose classes
3. **Block**: Element blocks with specific purpose
4. **Exception**: Handle edge cases

**Example:**

```html
<div class="card card--featured card--dark">
  <h2 class="heading heading--large">Title</h2>
  <p class="text text--muted">Content</p>
</div>
```

### ITCSS (Inverted Triangle CSS)

Organizes CSS into layers from generic to specific.

**Layers:**

1. **Settings**: Variables, configuration
2. **Tools**: Mixins, functions
3. **Generic**: Normalize, reset
4. **Elements**: Base HTML elements
5. **Objects**: Layout patterns
6. **Components**: UI components
7. **Utilities**: Helper classes

**File Structure:**

```
styles/
├── 01-settings/
│   └── _colors.scss
├── 02-tools/
│   └── _mixins.scss
├── 03-generic/
│   └── _reset.scss
├── 04-elements/
│   └── _headings.scss
├── 05-objects/
│   └── _layout.scss
├── 06-components/
│   └── _button.scss
└── 07-utilities/
    └── _spacing.scss
```

### BEM (Block Element Modifier)

Naming convention for CSS classes.

**Syntax:**

```css
.block { }
.block__element { }
.block--modifier { }
.block__element--modifier { }
```

**Example:**

```css
.card { }
.card__header { }
.card--featured { }
.card__header--highlighted { }
```

```html
<div class="card card--featured">
  <div class="card__header card__header--highlighted">
    Featured Content
  </div>
</div>
```

## Choosing the Right Approach

### Use Atomic Design When

- Building from scratch
- Need clear component hierarchy
- Team values mental model over tooling
- Teaching design system concepts

### Use CUBE CSS When

- Prefer utility-first approach
- Want maximum flexibility
- Working with legacy code
- Need rapid prototyping

### Use ITCSS When

- Working with Sass
- Prefer explicit organization
- Managing large CSS codebase
- Need clear separation of concerns

### Use BEM When

- Need strict naming convention
- Want self-documenting CSS
- Team is new to CSS architectures
- Need to prevent CSS conflicts

## Hybrid Approaches

### Atomic Design + BEM

```tsx
// Atomic Design structure with BEM naming
export const Button = ({ variant, children }) => (
  <button className={`button button--${variant}`}>
    {children}
  </button>
);
```

### Component-Based Organization

```typescript
// Organize by component rather than level
components/
  Button/
    Button.tsx
    Button.test.tsx
    Button.stories.tsx
    index.ts
  Card/
    Card.tsx
    Card.test.tsx
    Card.stories.tsx
    index.ts
```

## Best Practices

### 1. Start Simple

Begin with basic atomic design and evolve as needed.

### 2. Document Decisions

Explain why you chose your methodology in your documentation.

### 3. Be Consistent

Stick to your chosen methodology throughout the system.

### 4. Review Regularly

Reassess your approach as the system grows.

### 5. Team Alignment

Ensure the entire team understands and uses the methodology.

## Resources

- [Atomic Design by Brad Frost](https://atomicdesign.bradfrost.com/)
- [CUBE CSS by Andy Bell](https://cube.fyi/)
- [ITCSS by Harry Roberts](https://itcss.io/)
- [BEM Methodology](http://getbem.com/)

## Next Steps

- [Components](Components/README.md) - Component organization
- [Style Compositions](Style%20compositions.md) - CSS organization strategies
