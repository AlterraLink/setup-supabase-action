# Setup Supabase Action

[![GitHub release](https://img.shields.io/github/release/AlterraLink/setup-supabase-action.svg)](https://github.com/AlterraLink/setup-supabase-action/releases)
[![GitHub marketplace](https://img.shields.io/badge/marketplace-setup--supabase-blue?logo=github)](https://github.com/marketplace/actions/setup-supabase)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A GitHub Action that sets up a complete Supabase development environment with intelligent caching. Supports **npm**, **yarn**, **pnpm**, and **bun**!

## 🤝 Built on the Official Supabase CLI Action

This action uses the official [`supabase/setup-cli`](https://github.com/supabase/setup-cli) action under the hood, and extends it with a complete development environment setup. Think of it as a convenience wrapper that handles all the boilerplate so you can focus on your Supabase workflows.

```
┌─────────────────────────────────────────────────────────┐
│ AlterraLink/setup-supabase-action                       │
│                                                          │
│  ┌────────────────────┐   ┌─────────────────────────┐  │
│  │ Node.js Setup      │   │ Package Manager Setup   │  │
│  │ (actions/setup-    │   │ (npm/yarn/pnpm/bun)     │  │
│  │  node@v4)          │   │                         │  │
│  └────────────────────┘   └─────────────────────────┘  │
│                                                          │
│  ┌────────────────────┐   ┌─────────────────────────┐  │
│  │ Dependency Cache   │   │ Install Dependencies    │  │
│  │ (actions/cache@v4) │   │ (npm ci/pnpm install/   │  │
│  │                    │   │  yarn install/etc)      │  │
│  └────────────────────┘   └─────────────────────────┘  │
│                                                          │
│  ┌──────────────────────────────────────────────────┐  │
│  │ Official Supabase CLI                            │  │
│  │ (supabase/setup-cli@v1) ✅                       │  │
│  └──────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

### What's the difference?

| Feature              | `supabase/setup-cli` | `AlterraLink/setup-supabase-action` |
| -------------------- | -------------------- | ----------------------------------- |
| Supabase CLI         | ✅                   | ✅                                  |
| Node.js setup        | ❌ You handle it     | ✅ Handled for you                  |
| Package manager      | ❌ You handle it     | ✅ npm/yarn/pnpm/bun                |
| Dependency caching   | ❌ You handle it     | ✅ Automatic                        |
| Install dependencies | ❌ You handle it     | ✅ Optional                         |
| Lines of code        | ~3 + your setup      | 1-3 lines total                     |

**Before** (using just `supabase/setup-cli`):

```yaml
- uses: actions/setup-node@v4
  with:
      node-version: 22
- uses: pnpm/action-setup@v4
  with:
      version: 10.15.0
- run: pnpm install --frozen-lockfile
- uses: supabase/setup-cli@v1
# ~15-30 lines of boilerplate
```

**After** (using this action):

```yaml
- uses: AlterraLink/setup-supabase-action@v1
  with:
      package-manager: pnpm
# Just 3 lines! 🎉
```

## ✨ Features

- 🚀 **Complete Setup** - Node.js, package manager, dependencies, and Supabase CLI in one step
- 📦 **Multiple Package Managers** - Support for npm, yarn, pnpm, and bun
- 💾 **Intelligent Caching** - Automatic dependency caching for faster subsequent runs
- ⚙️ **Highly Configurable** - Customize all versions and behaviors
- 🎯 **Flexible** - Optional dependency installation and working directory support
- 🔄 **Keep It DRY** - Define setup once, reuse across all workflows
- ✅ **Battle-Tested** - Used in production workflows
- 🤝 **Official Foundation** - Built on top of the official Supabase CLI action

## 🎯 When to Use This Action

**Use this action when you want:**

- ✅ Complete development environment setup in one step
- ✅ Automatic dependency caching (saves 1-3 minutes per run)
- ✅ Support for multiple package managers
- ✅ Less boilerplate in your workflows (~85% reduction)
- ✅ Consistent setup across all your Supabase workflows

**Use the official `supabase/setup-cli` directly when you:**

- Only need the Supabase CLI (no Node.js or dependencies)
- Already have your own package manager setup
- Want the minimal, official-only solution

Most teams running Supabase workflows will benefit from this action's convenience and time savings!

> 📖 **Want more details?** See our comprehensive [comparison with the official action](docs/COMPARISON.md) for architecture diagrams, side-by-side examples, and migration guides.

## 📦 Usage

### Basic Examples

**With npm (default):**

```yaml
- uses: AlterraLink/setup-supabase-action@v1
- run: npm run supabase:start
```

**With pnpm:**

```yaml
- uses: AlterraLink/setup-supabase-action@v1
  with:
      package-manager: pnpm
- run: pnpm supabase start
```

**With yarn:**

```yaml
- uses: AlterraLink/setup-supabase-action@v1
  with:
      package-manager: yarn
- run: yarn supabase start
```

**With bun:**

```yaml
- uses: AlterraLink/setup-supabase-action@v1
  with:
      package-manager: bun
- run: bun run supabase start
```

### Complete Workflow Example

```yaml
name: Validate Schema

on: [pull_request]

jobs:
    validate:
        runs-on: ubuntu-latest
        steps:
            - uses: actions/checkout@v4

            - name: Setup Supabase
              uses: AlterraLink/setup-supabase-action@v1
              with:
                  package-manager: pnpm # or npm, yarn, bun

            - name: Start Supabase
              run: pnpm supabase start

            - name: Run migrations
              run: pnpm supabase db reset

            - name: Check for drift
              run: pnpm supabase db diff

            - name: Stop Supabase
              if: always()
              run: pnpm supabase stop
```

### Advanced Configuration

```yaml
- uses: AlterraLink/setup-supabase-action@v1
  with:
      node-version: "22" # Node.js version
      package-manager: "pnpm" # npm, yarn, pnpm, or bun
      package-manager-version: "9.0.0" # Specific package manager version
      supabase-version: "1.150.0" # Specific Supabase CLI version
      install-dependencies: "true" # Whether to run install
      working-directory: "./apps/backend" # Working directory
```

## 📥 Inputs

| Input                     | Description                            | Required | Default  |
| ------------------------- | -------------------------------------- | -------- | -------- |
| `node-version`            | Node.js version to install             | No       | `22`     |
| `package-manager`         | Package manager (npm, yarn, pnpm, bun) | No       | `npm`    |
| `package-manager-version` | Package manager version (not for npm)  | No       | `''`     |
| `supabase-version`        | Supabase CLI version                   | No       | `latest` |
| `install-dependencies`    | Whether to install dependencies        | No       | `true`   |
| `working-directory`       | Working directory                      | No       | `.`      |

> **Note:** The default Node.js version was bumped from `20` to `22` to support modern package manager versions (e.g. pnpm 11+, which requires Node ≥22.13). If you need an older Node version, pass `node-version` explicitly.

## 📤 Outputs

| Output            | Description                                           |
| ----------------- | ----------------------------------------------------- |
| `cache-hit`       | Whether the dependency cache was hit (`true`/`false`) |
| `package-manager` | The package manager that was configured               |

## 🔧 Common Use Cases

### Schema Validation

```yaml
- uses: AlterraLink/setup-supabase-action@v1
  with:
      package-manager: pnpm
- run: pnpm supabase start
- run: pnpm supabase db reset
- run: pnpm supabase db diff
```

### Type Generation

```yaml
- uses: AlterraLink/setup-supabase-action@v1
  with:
      package-manager: yarn
- run: yarn supabase start
- run: yarn supabase gen types typescript --local
```

### Multi-Database Testing

```yaml
strategy:
    matrix:
        database: [main, tenant]
        pm: [npm, pnpm, yarn]
steps:
    - uses: AlterraLink/setup-supabase-action@v1
      with:
          package-manager: ${{ matrix.pm }}
    - run: ${{ matrix.pm }} run ${{ matrix.database }}:validate
```

### Skip Dependencies

```yaml
- uses: AlterraLink/setup-supabase-action@v1
  with:
      package-manager: pnpm
      install-dependencies: "false"
- run: pnpm install --filter @myapp/backend
```

### Monorepo Support

```yaml
- uses: AlterraLink/setup-supabase-action@v1
  with:
      package-manager: pnpm
      working-directory: "./packages/api"
```

## 🎓 Real-World Example

```yaml
name: Supabase Validation

on:
    pull_request:
        paths:
            - "supabase/**"
            - "package.json"

permissions:
    contents: read
    pull-requests: write

jobs:
    validate:
        runs-on: ubuntu-latest
        strategy:
            matrix:
                include:
                    - name: main
                      start: pnpm supabase start
                      reset: pnpm supabase db reset
                      diff: pnpm supabase db diff
                    - name: tenant
                      start: pnpm tenant:start
                      reset: pnpm tenant:reset
                      diff: pnpm tenant:diff

        name: Validate ${{ matrix.name }} database

        steps:
            - uses: actions/checkout@v4

            - name: Setup Supabase environment
              uses: AlterraLink/setup-supabase-action@v1
              with:
                  package-manager: pnpm
                  package-manager-version: "10.15.0"

            - name: Start database
              run: ${{ matrix.start }}

            - name: Apply migrations
              run: ${{ matrix.reset }}

            - name: Check for schema drift
              run: ${{ matrix.diff }}

            - name: Stop database
              if: always()
              run: pnpm supabase stop
```

## 🤝 Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for details.

## ❓ FAQ

### Is this action official?

No, this is a community action built by [AlterraLink](https://github.com/AlterraLink). However, it uses the official [`supabase/setup-cli`](https://github.com/supabase/setup-cli) action under the hood for installing the Supabase CLI.

### Can I use this alongside the official action?

You don't need to! This action already includes the official Supabase CLI setup. Using both would be redundant:

```yaml
# ❌ Don't do this (redundant)
- uses: AlterraLink/setup-supabase-action@v1
- uses: supabase/setup-cli@v1 # Already included above!

# ✅ Do this instead
- uses: AlterraLink/setup-supabase-action@v1
```

### What if I only need the Supabase CLI?

If you don't need Node.js setup, package manager configuration, or dependency installation, use the official [`supabase/setup-cli`](https://github.com/marketplace/actions/supabase-cli-action) action directly.

### Does this support all the same features as the official action?

Yes! All `supabase/setup-cli` features are available through the `supabase-version` input. We pass it through directly to the official action.

### Will updates to the official action be available?

Yes! Since we use the official action internally, updates are automatic. We pin to `@v1` which gets the latest compatible version.

### Which Node.js version is used by default?

Node.js `22` (the current LTS). This default was chosen to support modern pnpm and yarn releases that require Node ≥22.13. To pin a different version, pass `node-version` explicitly.

## 📝 Changelog

See [CHANGELOG.md](CHANGELOG.md) for a list of changes.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 💬 Support

- 📖 [Documentation](https://github.com/AlterraLink/setup-supabase-action)
- 🐛 [Issue Tracker](https://github.com/AlterraLink/setup-supabase-action/issues)
- 💡 [Discussions](https://github.com/AlterraLink/setup-supabase-action/discussions)

## ⭐ Show Your Support

If you find this action helpful, please consider giving it a star! It helps others discover it.

---

Made with ❤️ by [AlterraLink](https://github.com/AlterraLink) for the Supabase community
