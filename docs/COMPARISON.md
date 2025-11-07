# Action Comparison & Relationship

> This document explains how this action relates to and builds upon the official Supabase CLI action.

## 🏗️ Architecture

```
                Your Workflow
                     |
                     |
    ┌────────────────┴────────────────┐
    │                                 │
    │  AlterraLink/setup-supabase     │
    │  -action@v1                     │
    │                                 │
    │  ┌─────────────────────────┐   │
    │  │ 1. Setup Node.js        │   │
    │  │    (actions/setup-node) │   │
    │  └─────────────────────────┘   │
    │                                 │
    │  ┌─────────────────────────┐   │
    │  │ 2. Setup Package Mgr    │   │
    │  │    (npm/yarn/pnpm/bun)  │   │
    │  └─────────────────────────┘   │
    │                                 │
    │  ┌─────────────────────────┐   │
    │  │ 3. Setup Caching        │   │
    │  │    (actions/cache)      │   │
    │  └─────────────────────────┘   │
    │                                 │
    │  ┌─────────────────────────┐   │
    │  │ 4. Install Dependencies │   │
    │  │    (npm ci/pnpm install)│   │
    │  └─────────────────────────┘   │
    │                                 │
    │  ┌─────────────────────────┐   │
    │  │ 5. Setup Supabase CLI   │   │
    │  │    ┌─────────────────┐  │   │
    │  │    │ supabase/setup- │  │   │
    │  │    │ cli@v1 ✅       │  │   │
    │  │    └─────────────────┘  │   │
    │  └─────────────────────────┘   │
    │                                 │
    └─────────────────────────────────┘
                     |
                     v
          Your Supabase Commands
          (supabase start, etc.)
```

## 📊 Side-by-Side Comparison

### Scenario 1: Basic Setup

#### Using Official Action Only

```yaml
steps:
    # Step 1: Setup Node.js (you do this)
    - uses: actions/setup-node@v4
      with:
          node-version: 20

    # Step 2: Setup pnpm (you do this)
    - uses: pnpm/action-setup@v4
      with:
          version: 10.15.0

    # Step 3: Get cache dir (you do this)
    - id: pnpm-cache
      run: echo "dir=$(pnpm store path)" >> $GITHUB_OUTPUT

    # Step 4: Setup cache (you do this)
    - uses: actions/cache@v4
      with:
          path: ${{ steps.pnpm-cache.outputs.dir }}
          key: ${{ runner.os }}-pnpm-${{ hashFiles('**/pnpm-lock.yaml') }}

    # Step 5: Install deps (you do this)
    - run: pnpm install --frozen-lockfile

    # Step 6: Finally, Supabase CLI
    - uses: supabase/setup-cli@v1

    # Step 7: Your commands
    - run: pnpm supabase start
# Total: ~20 lines, 6 separate steps
```

#### Using AlterraLink Action

```yaml
steps:
    # Step 1: Everything in one step
    - uses: AlterraLink/setup-supabase-action@v1
      with:
          package-manager: pnpm

    # Step 2: Your commands
    - run: pnpm supabase start
# Total: 3 lines, 1 step
```

**Savings: 85% less code, 5 fewer steps**

---

### Scenario 2: Multi-Database Validation

#### Using Official Action Only

```yaml
strategy:
    matrix:
        database: [main, tenant]
steps:
    - uses: actions/checkout@v4

    # Repeated for each matrix job
    - uses: actions/setup-node@v4
      with:
          node-version: 20
    - uses: pnpm/action-setup@v4
      with:
          version: 10.15.0
    - id: pnpm-cache
      run: echo "dir=$(pnpm store path)" >> $GITHUB_OUTPUT
    - uses: actions/cache@v4
      with:
          path: ${{ steps.pnpm-cache.outputs.dir }}
          key: ${{ runner.os }}-pnpm-${{ hashFiles('**/pnpm-lock.yaml') }}
    - run: pnpm install --frozen-lockfile
    - uses: supabase/setup-cli@v1

    - run: pnpm ${{ matrix.database }}:start
    - run: pnpm ${{ matrix.database }}:validate
# ~25 lines per matrix job = 50 lines total for 2 databases
```

#### Using AlterraLink Action

```yaml
strategy:
    matrix:
        database: [main, tenant]
steps:
    - uses: actions/checkout@v4

    - uses: AlterraLink/setup-supabase-action@v1
      with:
          package-manager: pnpm

    - run: pnpm ${{ matrix.database }}:start
    - run: pnpm ${{ matrix.database }}:validate
# ~8 lines for any number of databases
```

**Savings: 84% less code, scales beautifully**

---

## 🎯 When Each Makes Sense

### Use `supabase/setup-cli` Directly When:

✅ You **only** need the Supabase CLI

```yaml
# Example: Deploying edge functions
- uses: supabase/setup-cli@v1
- run: supabase functions deploy
```

✅ You already have sophisticated setup logic

```yaml
# Example: Custom caching strategy
- uses: your-org/custom-node-setup@v1
- uses: your-org/custom-cache@v1
- uses: supabase/setup-cli@v1
```

✅ You want the absolute minimal, official-only solution

---

### Use `AlterraLink/setup-supabase-action` When:

✅ You're building/testing/validating with Node.js dependencies

```yaml
# Schema validation, type generation, testing, etc.
- uses: AlterraLink/setup-supabase-action@v1
  with:
      package-manager: pnpm
- run: pnpm supabase db reset
- run: pnpm supabase db diff
- run: pnpm test
```

✅ You want automatic caching (saves 1-3 min per run)

✅ You support multiple package managers

✅ You want consistent setup across all workflows

✅ You want less boilerplate and maintenance

---

## 💡 Key Insights

### They're Not Competing

-   The AlterraLink action **uses** the official action internally
-   Think of it as: `Official CLI Action + Dev Environment = AlterraLink Action`
-   Updates to the official action automatically benefit users

### Choose Based on Needs

```
┌─────────────────────────────────────────────────┐
│                                                 │
│  CLI Only?  ──> Use supabase/setup-cli         │
│                                                 │
│  Full Dev Environment?  ──> Use AlterraLink    │
│                                                 │
└─────────────────────────────────────────────────┘
```

### Time Savings

-   **Manual setup**: 30 seconds to write, runs every time
-   **Official action**: 20 seconds to write, runs every time
-   **AlterraLink action**: 5 seconds to write, cached after first run
    -   First run: ~2-3 minutes (downloading deps)
    -   Subsequent runs: ~10-30 seconds (cache hit)
    -   **Savings per run: 1-3 minutes** 🚀

### Maintenance Burden

| Aspect            | Manual               | Official Only        | AlterraLink    |
| ----------------- | -------------------- | -------------------- | -------------- |
| Lines to maintain | 15-30                | 15-25                | 3-5            |
| Update Node.js    | Update all workflows | Update all workflows | Change 1 input |
| Update PM version | Update all workflows | Update all workflows | Change 1 input |
| Add new workflow  | Copy-paste 20 lines  | Copy-paste 20 lines  | Copy 3 lines   |
| Consistency       | Manual enforcement   | Manual enforcement   | Automatic      |

---

## 🚀 Migration Guide

### From Manual Setup → AlterraLink Action

**Before:**

```yaml
- uses: actions/setup-node@v4
  with:
      node-version: 20
- uses: pnpm/action-setup@v4
  with:
      version: 10.15.0
- run: pnpm install
- uses: supabase/setup-cli@v1
```

**After:**

```yaml
- uses: AlterraLink/setup-supabase-action@v1
  with:
      package-manager: pnpm
      node-version: 20
      package-manager-version: 10.15.0
```

### From Official Action Only → AlterraLink Action

**Before:**

```yaml
- uses: actions/setup-node@v4
- run: npm ci
- uses: supabase/setup-cli@v1
```

**After:**

```yaml
- uses: AlterraLink/setup-supabase-action@v1
  # npm is the default package manager
```

---

## 🙏 Credits

AlterraLink's Setup Supabase Action is built on top of:

-   [`supabase/setup-cli`](https://github.com/supabase/setup-cli) - Official Supabase CLI action
-   [`actions/setup-node`](https://github.com/actions/setup-node) - Official Node.js setup
-   [`actions/cache`](https://github.com/actions/cache) - Official caching action
-   [`pnpm/action-setup`](https://github.com/pnpm/action-setup) - pnpm setup action
-   [`oven-sh/setup-bun`](https://github.com/oven-sh/setup-bun) - Bun setup action

Thank you to all these projects and maintainers! 🙌
