name: meta-weaver
version: 2.4.1
description: AI-powered design system weaver for automated UI component generation and design token extraction from Figma/Sketch files
author: Design Systems Team <design-systems@company.com>
tags: [design, ai, automation]
dependencies:
  - python>=3.9
  - figma-api>=2.0.0
  - pillow>=9.0.0
  - svgwrite>=1.4.0
  - openai>=4.0.0
  - watchdog>=3.0.0
entrypoint: metaweaver
minimum_claw_version: "1.8.0"
capabilities:
  - component-generation
  - token-extraction
  - layout-automation
  - accessibility-validation
  - design-system-sync
```

# Meta Weaver

AI-powered design system weaver that automates UI component generation, extracts design tokens, and synchronizes design systems between Figma, code repositories, and design documentation.

## Purpose

Meta Weaver serves as an intelligent bridge between design tools and codebases. Real use cases include:

- **Automated component generation**: Convert Figma components into production-ready React/Vue components with TypeScript types, styled-components, and accessibility attributes
- **Design token extraction**: Parse Figma styles (colors, typography, spacing, shadows) and generate CSS custom properties, SCSS maps, or JavaScript objects for design systems
- **Responsive layout synthesis**: Generate responsive CSS layout code from design frames with breakpoint-specific adjustments
- **Dark mode automation**: Create automatic dark mode variants based on light mode designs using AI-powered color transformations
- **Accessibility auditing**: Validate contrast ratios, semantic HTML structure, and ARIA attributes against WCAG 2.1 standards
- **Design system synchronization**: Two-way sync between Figma libraries and code component libraries with conflict detection and resolution
- **Component variant generation**: Automatically generate all state variants (hover, active, disabled, focus) from base component designs
- **Spacing scale enforcement**: Extract consistent spacing values and generate utility classes (margin/padding) following 4px or 8px grids

## Scope

Meta Weaver provides these core commands:

### Component Generation

- `metaweaver generate component <figma-file-key> <node-id> --output <path> --framework <react|vue|svelte|angular> --style <css|styled-components|emotion|tailwind> --typescript --include-tests --accessibility-audit`
- `metaweaver generate variants <component-name> --count <n> --pattern <seed> --output <dir>`
- `metaweaver generate layout <frame-id> --breakpoints <xs,sm,md,lg,xl> --container-queries --output <css-file>`
- `metaweaver generate iconset <icon-library-key> --format <svg|font|react-component> --output <dir> --optimize`

### Design Token Extraction

- `metaweaver extract tokens <figma-file-key> --type <colors|typography|spacing|shadows|all> --format <css-variables|scss-maps|json|tokens-json> --output <dir> --prefix <string>`
- `metaweaver analyze-styles <file-path> --detect-overrides --report <format>`
- `metaweaver validate-tokens <token-file> --against <spec-file> --strict`

### Design System Sync

- `metaweaver sync figma-to-code <figma-team> <project-key> --branch <branch-name> --commit-msg <message> --dry-run`
- `metaweaver sync code-to-figma <component-dir> --team-id <id> --library-name <name> --publish`
- `metaweaver diff <source> <target> --format <json|markdown> --output <diff-file>`
- `metaweaver resolve-conflicts <conflict-file> --strategy <prefer-source|prefer-target|merge> --interactive`

### Image & Asset Processing

- `metaweaver optimize-images <asset-dir> --formats <webp,avif> --quality <0-100> --sizes <1x,2x> --output <optimized-dir>`
- `metaweaver generate-sprite <svg-dir> --output <sprite.svg> --symbol-prefix <icon->`
- `metaweaver convert-svg-to-component <svg-file> --framework <react|vue> --props <size,color> --output <component-file>`

### Validation & QA

- `metaweaver audit <design-file> --checks <contrast,keyboard-nav,semantic-html> --severity <warning|error> --output <report>`
- `metaweaver check-consistency <component-dir> --against <token-file> --fix`
- `metaweaver lint <style-file> --rules <naming,ordering,complexity>`

### Preview & Development

- `metaweaver preview <component-name> --port <port> --hot-reload --tokens <token-file>`
- `metaweaver serve-docs <component-dir> --output <docs-dir> --theme <light|dark>`
- `metaweaver storybook-generate <component-dir> --framework <react|vue> --output <.storybook>`

## Work Process

### Typical Component Generation Workflow

1. **Authentication & Setup**
   ```
   export FIGMA_API_TOKEN="your_figma_token"
   export OPENAI_API_KEY="your_openai_key"  # optional, for AI enhancements
   metaweaver auth figma --token $FIGMA_API_TOKEN
   ```

2. **Token Extraction** (if using design tokens)
   ```
   metaweaver extract tokens "FigmaFileKey" --type all --format css-variables --output ./styles/tokens --prefix --ds
   ```
   AI analyzes color relationships to suggest semantic names (e.g., `--ds-color-primary-500` instead of `--ds-color-blue-500`).

3. **Component Generation**
   ```
   metaweaver generate component "FigmaFileKey" "nodeId:123:456" \
     --output ./src/components/Button \
     --framework react \
     --style styled-components \
     --typescript \
     --include-tests \
     --accessibility-audit \
     --use-tokens ./styles/tokens/colors.css
   ```
   - Fetches component tree from Figma
   - Extracts layout, colors, typography
   - Maps to CSS/styled-components
   - Generates TypeScript interfaces
   - Creates test files with RTL/Jest
   - Runs accessibility validation

4. **Validation**
   ```
   metaweaver audit ./src/components/Button/Button.tsx --checks contrast,semantic-html --output ./reports/button-audit.json
   ```
   Checks color contrast ratios, verifies button uses `<button>` element with proper aria attributes.

5. **Commit & Sync**
   ```
   metaweaver sync code-to-figma ./src/components --team-id "team_123" --library-name "DS-Components" --publish
   ```
   Uploads generated components as documentation to Figma, linking code to design.

### Token Extraction Process

1. Fetch Figma file styles via API
2. Parse color, typography, spacing, shadow definitions
3. AI clustering: group similar colors, detect primary/secondary/neutral scales
4. Generate output in specified format:
   - CSS Variables: `--ds-color-primary-500: #3B82F6;`
   - SCSS Maps: `$colors: ("primary": ("500": #3B82F6));`
   - Tokens JSON: `{"color": {"primary": {"500": {"value": "#3B82F6"}}}}`
5. Write to files, maintain index.json

### Design System Sync Process

1. `metaweaver diff figma.code --format json` generates diff report
2. Detects:
   - New components in Figma not in code
   - Deprecated components in code not in Figma
   - Property mismatches (prop types, default values)
   - Token references out of sync
3. Interactive conflict resolution or auto-merge based on `--strategy`
4. Generate updated components and commit changes

## Golden Rules

1. **Never modify generated files manually**: All generated components must be marked with `<!-- Generated by Meta Weaver -->` comment. Manual changes will be overwritten on next generation.

2. **Use semantic token references**: Always reference design tokens (`var(--ds-color-primary-500)`) instead of hard-coded values. The system enforces this via `metaweaver check-consistency`.

3. **Component isolation**: Each generated component must be self-contained with its own styles, types, and tests. No implicit dependencies on global styles.

4. **Accessibility first**: Generated components must pass WCAG AA minimum. The `--accessibility-audit` flag is mandatory for component generation. Fail builds on violations.

5. **Version pinning**: Figma file versions must be pinned. Use `--figma-version <node-version>` to lock to specific design version. Do not generate from "latest" without version constraint in production.

6. **Token hierarchy**: Design tokens follow strict hierarchy: `category` > `subcategory` > `property` > `value`. Example: `color.brand.primary.500`. Never flatten.

7. **Framework consistency**: All components in a project must use the same framework and styling approach. Set in `metaweaver.config.js` and enforce via pre-commit hook.

8. **Preserve customizations**: If a component file contains a `<!-- METAWEAVER_CUSTOM -->` region, preserve that region during regeneration. Use for custom logic that cannot be auto-generated.

9. **Always generate tests**: The `--include-tests` flag is default in CI mode. Generate tests for interactions, prop variations, and accessibility.

10. **Git integration**: Generated changes must be committed atomically. Use `metaweaver generate ... && git add . && git commit -m "Generate: <component-name>"`. Never commit partial generations.

## Examples

### Example 1: Generate a Button component from Figma

```bash
# User runs:
metaweaver generate component "FigmaFileKey" "nodeId:123:456:789" \
  --output src/components/Button \
  --framework react \
  --style styled-components \
  --typescript \
  --include-tests \
  --accessibility-audit \
  --props "size:sm|md|lg,variant:primary|secondary|tertiary"

# Output structure:
src/components/Button/
├── Button.tsx              # Main component
├── Button.test.tsx         # RTL tests
├── Button.stories.tsx      # Storybook stories
├── Button.module.css       # If using CSS modules
├── Button.types.ts         # TypeScript interfaces
├── Button.accessibility.ts # Accessibility utilities
└── index.ts               # Barrel export

# Button.tsx snippet:
import styled from 'styled-components';
import { useButton, UseButtonProps } from './Button.accessibility';

export interface ButtonProps extends UseButtonProps {
  size?: 'sm' | 'md' | 'lg';
  variant?: 'primary' | 'secondary' | 'tertiary';
  children: React.ReactNode;
}

const StyledButton = styled.button<{ $size: string; $variant: string }>`
  padding: var(--ds-space-${$size === 'sm' ? '2' : $size === 'md' ? '3' : '4'});
  background: var(--ds-color-${$variant === 'primary' ? 'primary' : 'neutral'}-500);
  /* ... more styles */
`;

export const Button = ({ size = 'md', variant = 'primary', children, ...rest }: ButtonProps) => {
  const buttonProps = useButton(rest);
  return (
    <StyledButton $size={size} $variant={variant} {...buttonProps}>
      {children}
    </StyledButton>
  );
};
```

### Example 2: Extract design tokens from Figma and generate CSS variables

```bash
metaweaver extract tokens "FigmaFileKey" \
  --type colors,typography,spacing \
  --format css-variables \
  --output ./src/styles/design-tokens \
  --prefix --ds

# Generated files:
./src/styles/design-tokens/
├── colors.css           # Color tokens
├── typography.css       # Font sizes, weights, line heights
├── spacing.css          # Spacing scale (4px grid)
├── shadows.css          # Box shadow tokens
└── index.css            # Import all

# colors.css content:
:root {
  /* Semantic colors derived from Figma styles */
  --ds-color-primary-500: #3B82F6;
  --ds-color-primary-600: #2563EB;
  --ds-color-neutral-50: #F9FAFB;
  --ds-color-neutral-100: #F3F4F6;
  /* ... */
}
```

### Example 3: Validate accessibility of a design file

```bash
metaweaver audit "FigmaFileKey" "nodeId:123:456" \
  --checks contrast,keyboard-nav,semantic-html \
  --severity error \
  --output ./reports/accessibility.json

# Report output:
{
  "nodeId": "123:456",
  "name": "Submit Button",
  "violations": [
    {
      "type": "contrast",
      "severity": "error",
      "message": "Color contrast ratio 3.2:1 fails WCAG AA (minimum 4.5:1)",
      "expected": "4.5:1",
      "actual": "3.2:1",
      "fix": "Use background #1D4ED8 instead of #3B82F6"
    }
  ]
}
```

### Example 4: Optimize SVG icons and generate React components

```bash
metaweaver convert-svg-to-component ./assets/icons/*.svg \
  --framework react \
  --props "size,color,filled" \
  --output ./src/components/Icons

# For arrow-left.svg generates:
# IconArrowLeft.tsx with props interface:
interface IconArrowLeftProps {
  size?: number | 'sm' | 'md' | 'lg';
  color?: string;
  filled?: boolean;
}

export const IconArrowLeft = ({ size = 24, color = 'currentColor', filled = false }: IconArrowLeftProps) => (
  <svg width={typeof size === 'number' ? size : sizeMap[size]} height={...} fill={filled ? color : 'none'} stroke={color}>
    <path d="M15 19l-7-7 7-7" />
  </svg>
);
```

### Example 5: Synchronize design system between Figma and code

```bash
# Dry run first to see what would change
metaweaver sync figma-to-code "team_123" "project_key" \
  --branch feature/sync-design \
  --commit-msg "chore(design): sync components from Figma" \
  --dry-run

# Output:
# Components to generate: Button, Input, Modal, Card
# Components to deprecate: OldButton
# Tokens to update: colors (12 changes), spacing (4 changes)
# Conflicts detected: 2 (Modal.modalProps - resolve manually)

# After resolving conflicts, run actual sync:
metaweaver sync figma-to-code "team_123" "project_key" \
  --branch feature/sync-design \
  --commit-msg "chore(design): sync components from Figma" \
  --interactive-conflicts

# Generates components, updates token files, creates commit with all changes.
```

### Example 6: Generate responsive layout CSS from Figma frame

```bash
metaweaver generate layout "nodeId:123:789" \
  --breakpoints xs,sm,md,lg,xl \
  --container-queries \
  --output ./src/styles/layouts/grid.css

# Output includes:
# - Mobile-first CSS with clamp() for fluid spacing
# - Container query support for @container
# - CSS custom properties for layout tokens
# - Breakpoint overrides with min-width media queries
```

## Rollback Commands

Meta Weaver provides specific rollback operations:

### Rollback component generation

```bash
# Before generating, Meta Weaver creates backup in .metaweaver/backups/
metaweaver generate component "figma-key" "node-id" --output ./src/components

# If generation produces broken code:
metaweaver rollback component ./src/components/Button --to .metaweaver/backups/2026-03-05T14:30:00Z

# Or restore from last automatic backup:
metaweaver restore-backup ./src/components/Button
```

### Rollback design token changes

```bash
# List available token snapshots:
metaweaver token-snapshots list --output json

# Restore tokens to previous state:
metaweaver token-snapshots restore --date "2026-03-04" --type colors,spacing
```

### Undo sync operations

```bash
metaweaver sync figma-to-code team project --dry-run
# Realizes it's too many changes

# Abort the sync (only works before commit):
metaweaver sync cancel

# If already committed, revert git commit created by Meta Weaver:
git revert HEAD --no-edit
# Meta Weaver tag ensures revert only affects its changes:
# git revert --grep "chore(design): sync components from Figma"
```

### Reset local state

```bash
# Clear Meta Weaver cache and temporary files:
metaweaver cache clear --all

# Re-authenticate if tokens corrupted:
metaweaver auth logout figma && metaweaver auth figma --token $NEW_TOKEN
```

### Conflict resolution rollback

```bash
# If conflict resolution produced bad merge:
metaweaver conflicts rollback --strategy restore-backup --backup .metaweaver/conflict-backup-2026-03-05T14:25:00Z
```

## Dependencies & Requirements

- **Figma API Token**: Required for accessing Figma files. Set via `FIGMA_API_TOKEN` environment variable or `metaweaver auth figma`.
- **Node.js 18+**: For running generated component code and Storybook preview.
- **Python 3.9+**: Required for Meta Weaver CLI itself.
- **Optional - OpenAI API**: For AI-powered token naming and design pattern detection. Set `OPENAI_API_KEY`.
- **Git**: Required for sync operations and rollback tracking.

### System Dependencies (install via `metaweaver doctor`)

```bash
# Check all dependencies:
metaweaver doctor

# Fix common issues:
metaweaver doctor --fix
```

### Memory Requirements

- Minimum 4GB RAM for token extraction on large files
- 8GB+ recommended for component generation with AI enhancements
- 512MB disk space minimum for caches and backups

## Verification Steps

After any generation or sync operation, run:

```bash
# 1. Type checking (if TypeScript)
metaweaver typecheck ./src/components/GeneratedComponent

# 2. Accessibility validation
metaweaver audit ./src/components/GeneratedComponent --severity error

# 3. Consistency check against tokens
metaweaver check-consistency ./src/components --against ./src/styles/design-tokens --fix

# 4. Run component tests
npm test -- GeneratedComponent

# 5. Visual regression test (optional)
if [ -f ".metaweaver/config.yml" ] && grep -q "visualTesting: true" .metaweaver/config.yml; then
  metaweaver visual-test ./src/components/GeneratedComponent --baseline ./baseline-screenshots
fi
```

All checks must pass before committing generated code. CI gate:

```yaml
# .github/workflows/design-sync.yml
- name: Verify generated components
  run: |
    metaweaver typecheck ./src/components
    metaweaver audit ./src/components --severity error --output ./audit-report.json
    test -s ./audit-report.json && echo "No violations found" || (cat ./audit-report.json && exit 1)
```

## Troubleshooting

### Figma API rate limits
```
Error: FIGMA_API_RATE_LIMIT_EXCEEDED
```
Fix: Add `--rate-limit 60` to generate at most 60 requests/minute, or split large file into smaller batches.

### Token extraction fails on custom color styles
```
Error: Unsupported color format: "gradient-linear-90"
```
Fix: Figma gradients not supported for tokens. Either convert to solid colors or exclude via `--skip-types gradients`.

### Generated components have wrong spacing
```
Symptom: Tokens not applied, hardcoded pixel values appear
```
Cause: `use-tokens` flag missing or token file path incorrect.
Fix: Ensure token file is imported in component and values use `var(--ds-space-*)` syntax.

### AI enhancement timeouts
```
Error: OPENAI_TIMEOUT after 30000ms
```
Fix: Set `OPENAI_TIMEOUT=60000` or disable AI with `--no-ai` for faster generation without semantic naming.

### Sync conflicts in 3-Way merge
```
Error: Conflict in Button.tsx: PROPERTIES_MISMATCH
```
Fix: Use `metaweaver resolve-conflicts ./conflict.json --interactive` to manually resolve each conflict, choosing one side or merging.

### Preview server fails to start
```
Error: Port 3000 already in use
```
Fix: Specify different port: `metaweaver preview Button --port 3001`

### Generated component fails accessibility audit
```
Violation: Button element missing accessible name
```
Fix: Ensure `aria-label` or visible text content. If icon-only button, add `--include-label "Submit"` to generate command.

### Performance: Slow token extraction on large files
Fix: Use incremental extraction: `metaweaver extract tokens --modified-since "2026-03-01"` or target specific style types: `--type colors,typography`.

### Windows path issues
```
Error: Path too long for Windows
```
Fix: Enable long paths in Windows registry or output to shorter paths. Use forward slashes: `--output ./c/design-system`.

### Git merge conflicts in generated files
```
<<<<<<< HEAD
// Generated by Meta Weaver v2.4.1
=======
// Generated by Meta Weaver v2.4.2
>>>>>>> branch
```
Fix: Always regenerate component instead of manual merge. Run `metaweaver generate component ...` to overwrite both sides with latest version.

```