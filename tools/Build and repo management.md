# Build and Repository Management

Choosing the right build tools and repository structure is fundamental to maintaining a scalable design system.

## Build Tools Comparison

### Webpack

Oldest name, minimum at core, huge ecosystem and plugins.

**Strengths:**

- Mature and battle-tested
- Extensive plugin ecosystem
- Strong code splitting
- Great for complex applications

**Weaknesses:**

- Complex configuration
- Slower build times for large projects
- Steeper learning curve

**Use When:**

- Building complex applications with many dependencies
- Need extensive plugin ecosystem
- Legacy projects already using Webpack

**Example Configuration:**

```javascript
// webpack.config.js
module.exports = {
  entry: './src/index.ts',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html',
    }),
  ],
};
```

### Rollup

Minimal config, fast.

**Strengths:**

- Fast builds
- Better tree-shaking (dead code elimination)
- Smaller bundle sizes
- Cleaner output
- Excellent for libraries

**Weaknesses:**

- Less plugin ecosystem
- Code splitting limitations
- Better for libraries than apps

**Use When:**

- Building design system libraries
- Need optimal bundle sizes
- Tree-shaking is critical
- Distributing npm packages

**Example Configuration:**

```javascript
// rollup.config.js
import typescript from '@rollup/plugin-typescript';
import { terser } from 'rollup-plugin-terser';

export default {
  input: 'src/index.ts',
  output: [
    {
      file: 'dist/index.cjs.js',
      format: 'cjs',
    },
    {
      file: 'dist/index.esm.js',
      format: 'esm',
    },
  ],
  plugins: [
    typescript(),
    terser(),
  ],
};
```

### Turborepo

New kid in the town, maintained by Vercel, blazing fast for monorepos.

**Strengths:**

- Extremely fast monorepo builds
- Intelligent caching
- Task orchestration
- Works with any build tool
- Remote caching

**Weaknesses:**

- Monorepo-specific tool
- Smaller community than Webpack
- Requires monorepo setup

**Use When:**

- Managing monorepos
- Need fast builds across packages
- Want intelligent caching
- Multiple related projects

**Example Configuration:**

```json
// turbo.json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"]
    },
    "test": {
      "dependsOn": ["build"],
      "cache": false
    }
  }
}
```

## Monorepo Strategies

### Repository Structure

```
design-system/
├── packages/
│   ├── tokens/
│   │   └── package.json
│   ├── components/
│   │   └── package.json
│   ├── utils/
│   │   └── package.json
│   └── docs/
│       └── package.json
├── apps/
│   ├── storybook/
│   └── playground/
├── turbo.json
└── package.json
```

### Workspace Configuration

```json
// package.json (root)
{
  "name": "design-system",
  "private": true,
  "workspaces": [
    "packages/*",
    "apps/*"
  ],
  "scripts": {
    "build": "turbo run build",
    "test": "turbo run test",
    "dev": "turbo run dev"
  }
}
```

### Package Dependencies

```json
// packages/components/package.json
{
  "name": "@design-system/components",
  "version": "1.0.0",
  "dependencies": {
    "@design-system/tokens": "workspace:*",
    "@design-system/utils": "workspace:*",
    "react": "^18.0.0"
  },
  "peerDependencies": {
    "react": "^18.0.0"
  }
}
```

## Build Optimization

### Tree Shaking

```javascript
// rollup.config.js
export default {
  // ... other config
  treeshake: {
    moduleSideEffects: false,
    propertyReadSideEffects: false,
  },
};
```

### Code Splitting

```javascript
// webpack.config.js
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          chunks: 'all',
        },
      },
    },
  },
};
```

### Minification

```javascript
// Terser for JavaScript
import { terser } from 'rollup-plugin-terser';

export default {
  plugins: [
    terser({
      compress: {
        drop_console: true,
      },
    }),
  ],
};
```

## Versioning Strategies

### Semantic Versioning

```json
{
  "version": "1.2.3"
}
```

- **1**: Major (breaking changes)
- **2**: Minor (new features, backward compatible)
- **3**: Patch (bug fixes)

### Automated Versioning

```bash
# Using changesets
npx changeset

# Or semantic-release
npm install -D semantic-release
```

### Pre-release Versions

```json
{
  "version": "1.2.3-beta.1"
}
```

## Publishing

### Automated Publishing

```yaml
# .github/workflows/publish.yml
name: Publish

on:
  push:
    branches:
      - main

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: pnpm/action-setup@v2
      - run: pnpm install
      - run: pnpm build
      - run: pnpm changeset publish
```

### Multi-Package Publishing

```json
{
  "scripts": {
    "version:pre": "changeset pre enter beta",
    "version": "changeset version",
    "publish": "changeset publish"
  }
}
```

## Development Workflow

### Local Development

```json
{
  "scripts": {
    "dev": "turbo run dev --parallel",
    "build": "turbo run build",
    "test": "turbo run test",
    "lint": "turbo run lint",
    "type-check": "turbo run type-check"
  }
}
```

### Dependency Management

```bash
# Install in workspace
pnpm add react -w

# Add to specific package
pnpm add react --filter @design-system/components
```

## CI/CD Pipeline

### Build Testing

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: pnpm/action-setup@v2
      - run: pnpm install
      - run: pnpm build
      - run: pnpm test
```

### Caching

```yaml
- uses: actions/cache@v3
  with:
    path: node_modules
    key: ${{ runner.os }}-node-${{ hashFiles('**/pnpm-lock.yaml') }}
```

## Best Practices

1. **Use proper tool for the job** - Rollup for libraries, Webpack for apps
2. **Monorepo for related packages** - Use Turborepo or Lerna
3. **Automate versioning** - Use changesets or semantic-release
4. **Cache aggressively** - Use Turbo cache and GitHub Actions cache
5. **Parallel builds** - Leverage Turborepo's parallel execution
6. **Type safety** - TypeScript for all packages

## Tools Comparison Matrix

| Feature | Webpack | Rollup | Turborepo |
|---------|---------|--------|-----------|
| Bundle Size | Medium | Excellent | N/A |
| Build Speed | Slow | Fast | Very Fast |
| Config Complexity | High | Low | Low |
| Code Splitting | Excellent | Limited | N/A |
| Library Support | Good | Excellent | Works with Both |
| Ecosystem | Huge | Medium | Growing |

## Resources

- [Rollup Documentation](https://rollupjs.org/)
- [Turborepo Documentation](https://turborepo.org/)
- [pnpm Workspaces](https://pnpm.io/workspaces)
- [Changesets](https://github.com/changesets/changesets)

## Next Steps

- [Figma Integration](Figma.md) - Design-to-code workflow
- [Testing](testing.md) - Component testing strategies
