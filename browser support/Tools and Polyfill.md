# Browser Support Tools and Polyfills

For features that don't exist on old browser versions or different browsers, polyfills are needed to provide compatibility.

## Browserslist

[Browserslist](https://github.com/browserslist/browserslist) is a universal configuration for sharing target browsers between different front-end tools like Autoprefixer, Stylelint, Babel, and others.

### Configuration

Add to `package.json`:

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

Or create a `.browserslistrc` file:

```
# Browser usage in our project
> 0.5%
last 2 versions
not dead
not ie <= 11

# Mobile browsers
iOS >= 12
ChromeAndroid >= 64
```

### Query Examples

```javascript
// Specific browsers
"Chrome >= 60"
"Safari >= 11"
"Firefox >= 60"

// Market share
"> 1%"
"> 0.5% in US"

// Latest versions
"last 2 Chrome versions"
"last 2 major versions"

// Not included
"not dead"
"not ie <= 11"
```

### Usage with Tools

```javascript
// Autoprefixer (CSS)
module.exports = {
  plugins: [
    require('autoprefixer')({
      overrideBrowserslist: 'last 2 versions'
    })
  ]
};

// Babel (JavaScript)
module.exports = {
  presets: [
    ['@babel/preset-env', {
      useBuiltIns: 'usage',
      corejs: 3
    }]
  ]
};
```

## Polyfills

### What are Polyfills?

A polyfill is code that implements a feature on browsers that don't support it.

### When to Use Polyfills

- **CSS features** not supported in older browsers
- **JavaScript APIs** missing in legacy browsers
- **New HTML elements** in older browsers

### Popular Polyfills

#### core-js

Comprehensive polyfills for JavaScript features:

```bash
npm install core-js
```

```javascript
import 'core-js/stable';
import 'regenerator-runtime/runtime';
```

#### Custom Polyfill Setup

```javascript
// polyfills.js
if (typeof window !== 'undefined') {
  // Polyfill for Array.includes
  if (!Array.prototype.includes) {
    Array.prototype.includes = function(searchElement, fromIndex) {
      const O = Object(this);
      const len = parseInt(O.length) || 0;
      if (len === 0) return false;
      const n = parseInt(fromIndex) || 0;
      let k = n >= 0 ? n : Math.max(len + n, 0);
      
      function sameValueZero(x, y) {
        return x === y || (typeof x === 'number' && typeof y === 'number' && isNaN(x) && isNaN(y));
      }
      
      for (; k < len; k++) {
        if (sameValueZero(O[k], searchElement)) {
          return true;
        }
      }
      return false;
    };
  }

  // Polyfill for Promise
  if (!window.Promise) {
    // Load Promise polyfill
    import('./promise-polyfill');
  }
}
```

### CSS Polyfills

#### CSS Grid

```html
<!-- css-grid-polyfill -->
<link rel="stylesheet" href="css-grid.min.css">
<script src="css-grid.min.js"></script>
```

#### Modern CSS Features

```javascript
// Using postcss-preset-env
module.exports = {
  plugins: [
    require('postcss-preset-env')({
      stage: 2,
      features: {
        'nesting-rules': true
      }
    })
  ]
};
```

## Feature Detection

### Modernizr Alternative (Manual)

```javascript
function supportsFeature(feature, testFn) {
  try {
    return testFn();
  } catch (e) {
    return false;
  }
}

// Check for CSS Grid
const supportsGrid = supportsFeature('grid', () => {
  return CSS.supports('display', 'grid');
});

// Check for Intersection Observer
const supportsIntersectionObserver = 'IntersectionObserver' in window;

// Load polyfill if needed
if (!supportsGrid) {
  import('./css-grid-polyfill');
}
```

### Modern Feature Detection

```typescript
interface FeatureSupport {
  grid: boolean;
  flexbox: boolean;
  customProperties: boolean;
  fetch: boolean;
  promises: boolean;
  intersectionObserver: boolean;
}

function detectFeatures(): FeatureSupport {
  return {
    grid: CSS.supports('display', 'grid'),
    flexbox: CSS.supports('display', 'flex'),
    customProperties: CSS.supports('color', 'var(--test)'),
    fetch: 'fetch' in window,
    promises: 'Promise' in window,
    intersectionObserver: 'IntersectionObserver' in window,
  };
}

const features = detectFeatures();

if (!features.grid) {
  // Load CSS Grid polyfill
}
```

## Conditional Loading

### Dynamic Imports

```typescript
async function loadPolyfills() {
  const features = detectFeatures();
  
  const polyfills = [];
  
  if (!features.promises) {
    polyfills.push(import('es6-promise/auto'));
  }
  
  if (!features.fetch) {
    polyfills.push(import('whatwg-fetch'));
  }
  
  if (!features.intersectionObserver) {
    polyfills.push(import('intersection-observer'));
  }
  
  await Promise.all(polyfills);
}

// Load before app initialization
loadPolyfills().then(() => {
  // Start your application
  startApp();
});
```

### Service Worker for Older Browsers

```javascript
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw.js');
} else {
  // Use app shell for offline
  loadOfflineFallback();
}
```

## Autoprefixer

Automatically adds vendor prefixes:

```javascript
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          'css-loader',
          {
            loader: 'postcss-loader',
            options: {
              postcssOptions: {
                plugins: [
                  require('autoprefixer')({
                    overrideBrowserslist: [
                      'last 2 versions',
                      '> 1%'
                    ]
                  })
                ]
              }
            }
          }
        ]
      }
    ]
  }
};
```

## Babel Presets

### @babel/preset-env

Automatically determines which polyfills to load:

```javascript
module.exports = {
  presets: [
    ['@babel/preset-env', {
      useBuiltIns: 'usage', // Only include used polyfills
      corejs: 3,
      targets: {
        browsers: ['> 1%', 'last 2 versions', 'not dead']
      }
    }]
  ]
};
```

## Testing Polyfills

### Browser Testing

```javascript
// Test script to verify polyfills work
function testPolyfills() {
  const tests = {
    promises: typeof Promise !== 'undefined',
    fetch: typeof fetch !== 'undefined',
    includes: Array.prototype.includes !== undefined,
  };
  
  console.table(tests);
  
  return Object.values(tests).every(test => test === true);
}

if (!testPolyfills()) {
  console.warn('Some polyfills failed to load');
}
```

### Automated Testing

```javascript
// Test with different browser configurations
const browserConfigs = [
  { name: 'Chrome', version: 90 },
  { name: 'Safari', version: 14 },
  { name: 'IE', version: 11 },
];

browserConfigs.forEach(config => {
  describe(`${config.name} ${config.version}`, () => {
    it('should have required polyfills', () => {
      expect(testPolyfills()).toBe(true);
    });
  });
});
```

## Performance Considerations

### Bundle Size Impact

```javascript
// Use bundle analyzer
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer')
  .BundleAnalyzerPlugin;

module.exports = {
  plugins: [
    new BundleAnalyzerPlugin()
  ]
};
```

### Code Splitting for Polyfills

```javascript
function loadPolyfills() {
  return import(/* webpackChunkName: "polyfills" */ './polyfills')
    .then(() => {
      // Polyfills loaded
    });
}
```

### CDN for Common Polyfills

```html
<!-- Load from CDN for better caching -->
<script src="https://polyfill.io/v3/polyfill.min.js?features=default,es2015,es2016,es2017">
</script>
```

## Best Practices

1. **Target specific browsers** - Don't polyfill everything
2. **Test in real browsers** - Don't rely on feature detection alone
3. **Monitor bundle size** - Polyfills add weight
4. **Use conditional loading** - Only load what's needed
5. **Keep polyfills updated** - Security and bug fixes
6. **Document browser support** - Clear expectations

## Tools Reference

### Build-Time

- **Autoprefixer** - CSS vendor prefixes
- **postcss-preset-env** - Modern CSS features
- **@babel/preset-env** - JavaScript polyfills
- **core-js** - Comprehensive JS polyfills

### Runtime

- **Polyfill.io** - CDN polyfill service
- **Modernizr** - Feature detection
- **CSS Grid Polyfill** - Grid layout for older browsers

### Testing

- **BrowserStack** - Cross-browser testing
- **Sauce Labs** - Automated browser testing
- **Testling** - Browser testing automation

## Resources

- [browserslist](https://github.com/browserslist/browserslist)
- [Can I Use](https://caniuse.com/)
- [Polyfill.io](https://polyfill.io/)
- [core-js](https://github.com/zloirock/core-js)

## Next Steps

- [Browser Support Priority](Priority.md) - Support strategy
- [Build Tools](../Tools/Build and repo management.md) - Build configuration
