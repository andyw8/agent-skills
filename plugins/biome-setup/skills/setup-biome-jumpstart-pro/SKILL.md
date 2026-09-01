---
name: Setup Biome on Jumpstart Pro Rails
description: This skill should be used when the user asks to "setup Biome", "add Biome to Rails", "configure Biome for Jumpstart Pro", "add JavaScript linting to Rails", or "replace ESLint with Biome" on a Jumpstart Pro Rails application.
version: 0.1.0
---

# Setup Biome on Jumpstart Pro Rails

This skill configures Biome for JavaScript linting, formatting, and import organization on a Jumpstart Pro Rails application. Biome replaces ESLint and Prettier with a single, faster tool.

## Prerequisites

- Node.js available in the project
- Jumpstart Pro Rails application (uses importmap, Stimulus controllers, vendor/ and app/assets/tailwind/)

## Setup Steps

### Step 1: Install Biome

```bash
npm install --save-dev --save-exact @biomejs/biome
```

Using `--save-exact` pins the version to avoid unexpected upgrades.

### Step 2: Initialize Configuration

```bash
npx biome init --jsonc
```

This creates `biome.jsonc` with sensible defaults.

### Step 3: Configure biome.jsonc

Replace the generated config with the contents from `references/biome-config.jsonc`. Key settings:

- **VCS integration**: Enabled with gitignore support
- **Tab indentation**: Matches Jumpstart Pro conventions
- **Double quotes**: For JavaScript strings
- **Organize imports**: Enabled via assist
- **Ignore patterns**: `vendor` and `app/assets/tailwind` directories
- **Disabled rules**: Rules that produce false positives on Jumpstart Pro code:
  - `a11y/noSvgWithoutTitle`: Many decorative SVGs in Jumpstart Pro don't need titles
  - `suspicious/useIterableCallbackReturn`: Common pattern in Stimulus controller callbacks
  - `suspicious/useGetterReturn`: False positives with Stimulus controller value accessors

### Step 4: Run Biome Lint Auto-Fix

```bash
npx @biomejs/biome lint --write --unsafe
```

The `--unsafe` flag allows fixes that may change behavior. Review changes after running.

### Step 5: Apply Formatting

```bash
npx @biomejs/biome format --write
```

### Step 6: Organize Imports

```bash
npx @biomejs/biome check --write --unsafe
```

This sorts and organizes import statements across all JavaScript files.

### Step 7: Add CI Integration

Add Biome to the existing GitHub Actions CI workflow. In `.github/workflows/ci.yml`, add these steps to the `lint` job after the ERB lint step:

```yaml
      - name: Set up Biome
        uses: biomejs/setup-biome@v2

      - name: Run Biome
        run: biome ci
```

The `biome ci` command runs linting, formatting checks, and import organization checks in one step. It exits with a non-zero code if any issues are found.

Do NOT add Biome as a separate job — keep it in the existing `lint` job to avoid duplicating Ruby setup time.

## Verification

After setup, run `biome ci` to confirm everything passes:

```bash
npx @biomejs/biome ci
```

## Additional Resources

### Reference Files

- **`references/biome-config.jsonc`** — Complete biome.jsonc configuration for Jumpstart Pro
- **`references/ci-diff.yml`** — Example diff showing the CI workflow changes
