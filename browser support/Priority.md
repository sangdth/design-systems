# Browser Support Priority

Determining browser support strategy is a critical decision that impacts development time, user experience, and business metrics.

## Priority Groups

### Higher Priority Browsers

Typically the majority browsers in the market with higher market share:

- **Chrome/Edge** - Largest desktop market share
- **Firefox** - Strong developer and privacy-focused user base
- **Safari** - Dominant on macOS and iOS
- **Mobile Chrome/Samsung Internet** - Primary mobile browsers

**Business-Specific Considerations:**

Your priority should align with your business requirements:

- If your company is in China → QQ Browser and UC Browser should have higher priority
- If your main users are on iOS → Safari should be highest priority
- If your target market is enterprise → IE11 (legacy) or specific enterprise browsers
- If your product is mobile-first → Mobile browsers get highest priority

### Lower Priority Browsers

Alternative browsers that users can download:

- Opera
- Brave
- Vivaldi
- Other niche browsers
- Less popular mobile browsers

Some browsers are not allowed to download in certain countries, which may influence your priority.

## Support Levels

### Tier 1: Full Support

- Fully tested
- All features work
- Regular QA testing

**Example:** Chrome (latest 2 versions), Safari (latest 2 versions), Firefox (latest 2 versions)

### Tier 2: Core Support

- Core functionality works
- Some advanced features may have degraded experience
- Tested less frequently

**Example:** Opera, Edge (legacy)

### Tier 3: Best Effort

- May work but not guaranteed
- No regular testing
- Fix critical issues only

**Example:** IE11, older mobile browsers

### Tier 4: Not Supported

- Known to have issues
- No effort to support
- Official non-support notice

## Decision Framework

### Step 1: Analyze User Data

```typescript
// Use analytics to determine actual browser usage
interface BrowserAnalytics {
  browser: string;
  version: string;
  percentage: number;
  platform: string;
}

// Prioritize based on actual usage
const browserSupport: BrowserSupportConfig = {
  chrome: { minVersion: 90, priority: 'tier1' },
  safari: { minVersion: 14, priority: 'tier1' },
  firefox: { minVersion: 88, priority: 'tier1' },
  ie: { minVersion: 11, priority: 'tier3' },
};
```

### Step 2: Calculate Coverage

```typescript
function calculateCoverage(browsers: BrowserAnalytics[], config: BrowserSupportConfig) {
  return browsers
    .filter(browser => isSupported(browser, config))
    .reduce((sum, browser) => sum + browser.percentage, 0);
}

// Target: 95%+ coverage for tier 1 browsers
```

### Step 3: Consider Business Impact

- **Revenue Impact**: Do high-value users use specific browsers?
- **Development Cost**: Is supporting a browser worth the effort?
- **Market Position**: Competitive features may require broader support

### Step 4: Set Version Policy

```typescript
const browserPolicy = {
  tier1: 'latest 2-3 versions',
  tier2: 'latest 1-2 versions',
  tier3: 'current version only',
};
```

## Version Strategy

### N-2 Strategy (Recommended)

Support the current version and the previous 2 major versions:

- Chrome: Latest, N-1, N-2
- Safari: Latest, N-1, N-2
- Firefox: Latest, N-1, N-2

### Not-Dead Browsers

Use browserslist's "not dead" query to exclude browsers that haven't been updated in 24 months:

```json
{
  "browserslist": [
    "> 0.5%",
    "last 2 versions",
    "not dead"
  ]
}
```

## Testing Strategy

### Prioritize Testing

1. **Tier 1 browsers** - Automated + manual testing on each release
2. **Tier 2 browsers** - Automated testing, manual spot checks
3. **Tier 3 browsers** - Automated testing only

### Automated Testing

```javascript
// BrowserStack or similar
const testBrowsers = [
  { os: 'Windows', browser: 'Chrome', version: 'latest' },
  { os: 'Windows', browser: 'Chrome', version: 'latest-1' },
  { os: 'macOS', browser: 'Safari', version: 'latest' },
  { os: 'iOS', browser: 'Safari', version: 'latest' },
];
```

## Progressive Enhancement

Build for modern browsers, enhance for older ones:

```typescript
// Core functionality works everywhere
function submitForm() {
  // Basic submission
}

// Enhanced experience for modern browsers
if (supportsFetchAPI()) {
  submitForm = async () => {
    const response = await fetch('/api/submit', {
      method: 'POST',
      body: JSON.stringify(data),
    });
    // ...
  };
}
```

## Documentation Requirements

### Browser Support Matrix

| Browser | Version | Support | Notes |
|---------|---------|---------|-------|
| Chrome | 90+ | ✅ Full | Primary desktop browser |
| Safari | 14+ | ✅ Full | macOS & iOS |
| Firefox | 88+ | ✅ Full | Desktop focus |
| Edge | 90+ | ✅ Full | Chromium-based |
| IE11 | 11 | ⚠️ Degraded | Legacy support, basic features |
| Opera | Latest | ✅ Core | Limited testing |

### Feature Support Matrix

| Feature | Chrome 90 | Safari 14 | Firefox 88 | IE11 |
|---------|-----------|-----------|------------|------|
| CSS Grid | ✅ | ✅ | ✅ | ❌ |
| Flexbox | ✅ | ✅ | ✅ | ⚠️ Partial |
| CSS Variables | ✅ | ✅ | ✅ | ❌ |
| ES6 Classes | ✅ | ✅ | ✅ | ⚠️ Polyfill |

## Implementation

### Browserslist Configuration

```json
{
  "browserslist": [
    "> 0.5%",
    "last 2 versions",
    "not dead",
    "not ie <= 11"
  ]
}
```

### Feature Detection

```typescript
function isBrowserSupported() {
  // Check for critical features
  return (
    'fetch' in window &&
    'Promise' in window &&
    CSS.supports('display', 'grid')
  );
}

if (!isBrowserSupported()) {
  showBrowserWarning();
}
```

### Graceful Degradation

```typescript
// Detect and handle unsupported features
if (!CSS.supports('display', 'grid')) {
  import('css-grid-polyfill').then(gridPolyfill => {
    gridPolyfill.enable();
  });
}
```

## Best Practices

1. **Use real data** - Base decisions on actual user analytics
2. **Regular review** - Update browser support quarterly
3. **Clear documentation** - Public browser support policy
4. **Progressive enhancement** - Build up, not down
5. **Set expectations** - Communicate support levels to users
6. **Monitor adoption** - Track new browser versions

## Communication

### User-Facing Messages

```html
<!-- Browser upgrade prompt -->
<div class="browser-warning">
  <p>You're using an outdated browser. For the best experience, please <a href="https://www.google.com/chrome/">upgrade</a>.</p>
</div>
```

### Developer Documentation

```markdown
## Browser Support

- **Tier 1 (Full)**: Latest 2 versions of Chrome, Safari, Firefox, Edge
- **Tier 2 (Core)**: Current version of Opera and other modern browsers
- **Tier 3 (Best Effort)**: IE11 with limited features

See full matrix in [docs/browser-support.md](../../docs/browser-support.md)
```

## Resources

- [browserslist.dev](https://browserslist.dev/) - Shareable configuration
- [Can I Use](https://caniuse.com/) - Browser feature support
- [BrowserStack](https://www.browserstack.com/) - Cross-browser testing

## Next Steps

- [Browser Support Tools](Tools and Polyfill.md) - Polyfills and tools
- [Build Tools](../Tools/Build and repo management.md) - Build configuration
