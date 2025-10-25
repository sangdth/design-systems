# Papercut Workflow

Papercuts are small problems, issues, and bugs that are too small to warrant their own campaign or dedicated team, but are annoying and sometimes block development when the time comes. Our job is to eliminate as many as possible, as quickly as possible.

## What Are Papercuts?

### Characteristics

- **Small**: Can be fixed in < 1 hour
- **Annoying**: Friction that slows down developers daily
- **Risky**: Can compound into bigger problems
- **Unprioritized**: Too small for normal roadmap

### Examples

- Inconsistent prop names across components
- Missing TypeScript types
- Outdated documentation examples
- Inconsistent error messages
- Poor console warnings
- Missing accessibility attributes
- Inconsistent component patterns
- Outdated dependencies
- Inefficient build scripts

## Why Papercuts Matter

### Compound Effect

One papercut might save 30 seconds per developer per day:

- 10 developers × 30 seconds × 200 workdays = 16.6 hours saved per year
- Multiply by the number of papercuts

### Developer Morale

Small frustrations add up and impact:

- Productivity
- Code quality
- Team satisfaction
- Learning curve for new developers

### Quality Bar

Consistently addressing papercuts maintains high quality standards.

## Workflow Process

### 1. Identify Papercuts

**Sources:**

```typescript
// During code review
// "This prop name is inconsistent with other components"
<Button variant="primary" />  // ✅ Good
<Card type="primary" />       // ❌ Inconsistent naming

// User feedback
"Getting this TypeScript error frequently..."
Error: Property 'variant' does not exist on type...

// Developer feedback
"Would be nice if this was typed correctly"
```

**Tracking:**

```markdown
## Papercut #47: Inconsistent prop naming

**Issue:** Card uses `type` while Button uses `variant`
**Impact:** Developer confusion, inconsistent API
**Effort:** 15 minutes
**Priority:** Medium
```

### 2. Schedule Cleaning Time

**Dedicated Time Blocks:**

```markdown
## Papercut Cleanup Schedule

**Weekly:** 2 hours for papercut fixes
**Monthly:** Half-day for larger papercut projects
**Quarterly:** Review and prioritize papercut backlog
```

**When to Schedule:**

- End of sprint (low-stress time)
- Friday afternoons
- Slow periods between features

### 3. Prioritization

**Criteria:**

1. **Impact**: How many developers does this affect?
2. **Frequency**: How often is it encountered?
3. **Effort**: How long to fix?
4. **Risk**: Could this cause bigger issues?

**Scoring:**

```
Priority Score = (Impact × Frequency) / Effort

High Priority: Score > 10
Medium Priority: Score 5-10
Low Priority: Score < 5
```

### 4. Do It When You See It

**Rule:** If you see a papercut and can fix it in < 15 minutes, do it immediately.

```typescript
// Before committing a feature
// Notice: Missing export
export function Button() { }

// Fix immediately while context is fresh
export { Button }; // Add missing export
```

## Papercut Categories

### Code Quality

```typescript
// Missing types
const handleClick = (e) => { }
// Should be:
const handleClick = (e: React.MouseEvent) => { }

// Console errors not cleaned
console.log('Debug value:', value); // Should be removed or use proper logger

// Inconsistent patterns
// Some components use forwardRef, others don't
```

### Documentation

```markdown
<!-- Outdated examples -->
<Button variant="primary">Old Pattern</Button>
<!-- Should show current pattern -->
```

### Developer Experience

```typescript
// Poor error messages
throw new Error('Bad input'); 
// Should be:
throw new Error('Invalid variant: expected "primary" | "secondary", got "' + variant + '"');

// Missing console warnings
if (deprecatedProp) {
  console.warn('Prop "oldName" is deprecated, use "newName" instead');
}
```

## Papercut Workflow Example

### Week 1: Identify

```markdown
## Papercut Backlog

1. Missing TypeScript types in Button props
2. Inconsistent error handling
3. Outdated Storybook examples
4. Missing accessibility tests
5. Inconsistent component exports
```

### Week 2: Prioritize

```markdown
## Prioritized Papercuts

**High:**
- #1 Missing types (affects everyone daily)
- #4 Missing a11y tests (risk)

**Medium:**
- #3 Outdated examples (confusion)
- #5 Inconsistent exports (friction)

**Low:**
- #2 Inconsistent errors (rare)
```

### Week 3: Fix

```bash
# Allocate 2 hours Friday afternoon
- [ ] Add TypeScript types to Button (30 min)
- [ ] Add accessibility tests (45 min)
- [ ] Update Storybook examples (45 min)

# Pick off easy wins throughout week
- [x] Fixed inconsistent exports (2 min)
```

## Benefits of Tracking Papercuts

### Metrics

```typescript
// Track over time
const papercutMetrics = {
  identified: 50,
  fixed: 35,
  inProgress: 10,
  backlog: 5,
  avgTimeToFix: 45, // minutes
};
```

### Trend Analysis

- Are we creating more papercuts than we fix?
- Are similar papercuts recurring? (pattern issue)
- Is average time increasing? (complexity increase)

## Collaboration

### Shared Responsibility

```markdown
## Papercut Rotation

Each team member takes one week per month:
- Monitor papercut backlog
- Fix 2-3 papercuts
- Document pattern improvements
```

### Community Contribution

```markdown
## How to Contribute Papercuts

1. Identify small issue (< 1 hour to fix)
2. Create issue with "papercut" label
3. Include: Description, Impact, Suggested Fix
4. Community or core team fixes within 1 week
```

## Automation

### Automated Detection

```typescript
// Linting rules to catch common papercuts
eslint: {
  rules: {
    '@typescript-eslint/no-explicit-any': 'error',
    'react/no-unused-prop-types': 'warn',
    'jsx-a11y/alt-text': 'error',
  }
}

// Automated papercut scanning
npm run check:papercuts
// Reports: missing types, inconsistencies, outdated deps
```

## Budget Allocation

### Time Budget

```markdown
## Papercut Budget

**Weekly:** 2-4 hours (5-10% of developer time)
**Monthly:** 1 day (papercut cleanup sprint)
**Quarterly:** 2 days (papercut audit and improvements)

**Maximum:** 10% of total development time
```

### When to Stop

If papercut cleanup exceeds 10% of development time, there may be:

- Deeper system issues that need fixing
- Too much technical debt
- Need for refactoring phase

## Examples

### Papercut #1: Inconsistent Imports

```typescript
// Some files use
import { Button } from '@design-system/components';
// Others use
import Button from '@design-system/components/Button';

// Fix: Add export barrel
export { Button } from './Button';
export { Card } from './Card';
```

### Papercut #2: Missing Prop Validation

```typescript
// No validation
const Button = ({ variant }) => { }

// Add validation (papercut fix)
const Button = ({ variant }: { variant: 'primary' | 'secondary' }) => {
  if (!variant) throw new Error('Button requires variant prop');
}
```

## Prevention

### Code Review Process

```markdown
## Papercut Checklist in PR Template

- [ ] Consistent naming conventions
- [ ] TypeScript types included
- [ ] Accessibility attributes
- [ ] Updated documentation
- [ ] No console.log/debug code
```

### Automated Checks

```json
{
  "scripts": {
    "check:papercuts": "tsc --noEmit && eslint . && tests:accessibility"
  }
}
```

## Success Metrics

```typescript
// Track over quarter
const metrics = {
  papercutsIdentified: 120,
  papercutsFixed: 110,
  avgTimeToFix: 35, // minutes
  developerSatisfaction: 4.5, // out of 5
  codeQuality: 'A', // Before: C
};
```

## Resources

- [Technical Debt Management](../Refactoring/Opportunistic%20Refactoring.md)
- [Common Mistakes](Mistakes%20to%20avoid.md)
- [Contributing Guide](../contribution-guide.md)

## Next Steps

- Establish your own papercut workflow
- Start tracking papercuts this week
- Schedule first cleanup session
- Celebrate small wins!

Remember: Cleaning papercuts should be scheduled, should have dedicated time and resources, and should be done when you see it, when you can!
