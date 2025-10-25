# Figma Integration

Bridging the gap between design and code is critical for maintaining consistency in design systems. Figma integration tools help automate this process.

## Design-to-Code Workflow

### The Problem

- Manual translation leads to inconsistencies
- Time-consuming handoff process
- Design and code drift over time
- Updates require coordination

### The Solution

Automated tools that sync design tokens and components from Figma to code.

## Figma Plugins for Design Systems

### Figmagic

[Figmagic](https://github.com/mikaelvesavuori/figmagic) - Automated design token extraction.

**Features:**

- Extract design tokens from Figma
- Generate code for multiple frameworks
- CSS, JavaScript, TypeScript exports
- Component code generation
- Storybook stories

**Installation:**

```bash
npm install -D figmagic
```

**Configuration:**

```json
{
  "figmagic": {
    "figmaData": "figma.json",
    "figmaAccessToken": "YOUR_TOKEN",
    "figmaId": "YOUR_FILE_ID",
    "outputDirTokens": "./tokens",
    "outputDirElements": "./components"
  }
}
```

**Usage:**

```bash
npx figmagic
```

### Design Tokens

[design-tokens](https://github.com/lukasoppermann/design-tokens) - Figma plugin for design tokens.

**Features:**

- Visual token editor in Figma
- Export to multiple formats
- JSON, CSS, SCSS exports
- Real-time sync
- Version control integration

**Export Format Example:**

```json
{
  "color": {
    "primary": {
      "value": "#007AFF",
      "type": "color"
    }
  },
  "spacing": {
    "sm": {
      "value": "8px",
      "type": "dimension"
    }
  }
}
```

### figma-export

[figma-export](https://github.com/RedMadRobot/figma-export) - Export Figma assets and styles.

**Features:**

- Export SVGs
- Export images at multiple scales
- Extract design tokens
- Component specs
- Style guide generation

**Installation:**

```bash
npm install -g @figma-export/cli
```

**Configuration:**

```javascript
// figma-export.config.js
module.exports = {
  fileId: 'YOUR_FILE_ID',
  personalAccessToken: 'YOUR_TOKEN',
  outputters: [
    require('@figma-export/styles-outputter-as-css')({
      output: './output/styles.css'
    }),
    require('@figma-export/component-outputter-as-svg')({
      output: './output/svgs'
    })
  ]
};
```

## Automated Token Sync

### Setup Workflow

```javascript
// scripts/sync-figma-tokens.js
const figmagic = require('figmagic');
const path = require('path');

async function syncTokens() {
  try {
    await figmagic({
      figmaData: './figma.json',
      figmaAccessToken: process.env.FIGMA_TOKEN,
      figmaId: process.env.FIGMA_FILE_ID,
      outputDirTokens: './src/tokens/figma',
      outputDirElements: './src/components/figma',
      outputFormat: 'typescript',
    });
    
    console.log('✅ Tokens synced from Figma');
  } catch (error) {
    console.error('❌ Failed to sync tokens:', error);
    process.exit(1);
  }
}

syncTokens();
```

### CI/CD Integration

```yaml
# .github/workflows/sync-figma.yml
name: Sync Figma Tokens

on:
  schedule:
    - cron: '0 0 * * *' # Daily at midnight
  workflow_dispatch:

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Setup Node
        uses: actions/setup-node@v2
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm install
      
      - name: Sync from Figma
        env:
          FIGMA_TOKEN: ${{ secrets.FIGMA_TOKEN }}
          FIGMA_FILE_ID: ${{ secrets.FIGMA_FILE_ID }}
        run: npm run sync:figma
      
      - name: Commit changes
        run: |
          git config user.name "github-actions"
          git config user.email "actions@github.com"
          git add .
          git diff --quiet && git diff --staged --quiet || git commit -m "chore: sync design tokens from Figma"
          git push
```

## Component Sync

### Export Component Specs

```typescript
// scripts/export-components.ts
import * as FigmaExport from 'figma-export';

async function exportComponents() {
  await FigmaExport({
    fileId: process.env.FIGMA_FILE_ID,
    personalAccessToken: process.env.FIGMA_TOKEN,
    outputters: [
      FigmaExport.outputters.SVGComponents({
        output: './src/components/icons',
      }),
    ],
  });
}

exportComponents();
```

### Code Generation

```javascript
// Use Framer Code Component or similar
// Generates React components from Figma designs

function FigmaComponent({ nodeId }) {
  // Auto-generated from Figma
  return <div>...</div>;
}
```

## Icon Sync

### Automated Icon Export

```bash
# Export icons as SVGs from Figma
npx @figma-export/cli icons \
  --file-id YOUR_FILE_ID \
  --token YOUR_TOKEN \
  --output ./icons
```

### Icon Component Generation

```typescript
// Generate icon components from SVGs
import { generateIcons } from './icon-generator';

generateIcons('./icons', './src/components/Icon');
```

## Design API

### Figma API Integration

```typescript
import { Client } from '@figma/rest-api-spec';

const figma = new Client({ accessToken: process.env.FIGMA_TOKEN });

async function fetchDesignTokens() {
  const file = await figma.getFile(process.env.FIGMA_FILE_ID);
  
  // Extract design tokens from Figma components
  const tokens = extractTokens(file);
  
  return tokens;
}

function extractTokens(file: any) {
  const tokens = {
    colors: {},
    spacing: {},
    typography: {},
  };
  
  // Traverse Figma file to extract tokens
  traverseNodes(file.document.children, tokens);
  
  return tokens;
}
```

## Best Practices

### 1. Naming Conventions

Establish naming conventions in Figma that match your code structure:

```typescript
// Figma layer name: Color/Primary/500
// Generated token name: color.primary[500]
```

### 2. Version Control

Track design changes alongside code:

```bash
# Include Figma file ID in changelog
- Updated button styles (Figma: abc123)
```

### 3. Automated Testing

Test that design tokens match Figma:

```typescript
test('tokens match Figma', async () => {
  const figmaTokens = await fetchFigmaTokens();
  const codeTokens = require('./tokens');
  
  expect(codeTokens).toMatchObject(figmaTokens);
});
```

### 4. Documentation Sync

Keep documentation in sync with design:

```markdown
<!-- Auto-generated from Figma -->
## Button

![Button in Figma](./figma/button.png)
```

## Workflow

### Daily Sync

```bash
# Run daily to catch design updates
npm run sync:figma
```

### Pre-Publish

```bash
# Before releasing, sync latest design
npm run sync:figma && npm test
```

### Review Process

1. Designer updates Figma
2. Automated sync runs nightly
3. Developers review changes in PR
4. Approve and merge if tests pass

## Troubleshooting

### Token Mismatches

```bash
# Compare tokens between Figma and code
npm run compare:tokens
```

### Missing Tokens

```bash
# Check which tokens are in Figma but not in code
npm run check:missing
```

## Resources

- [Figmagic](https://github.com/mikaelvesavuori/figmagic)
- [design-tokens Plugin](https://github.com/lukasoppermann/design-tokens)
- [figma-export](https://github.com/RedMadRobot/figma-export)
- [Figma API](https://www.figma.com/developers/api)

## Next Steps

- [Build Tools](Build and repo management.md) - Repository structure
- [Testing](testing.md) - Visual regression testing
- [Documentation](../../Documentation.md) - Component documentation
