# Setup Supabase Action

[![GitHub release](https://img.shields.io/github/release/AlterraLink/setup-supabase-action.svg)](https://github.com/AlterraLink/setup-supabase-action/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A GitHub Action that sets up a complete Supabase development environment with intelligent caching. Supports **npm**, **yarn**, **pnpm**, and **bun**!

## Quick Start

```yaml
- uses: AlterraLink/setup-supabase-action@v1
  with:
    package-manager: pnpm  # or npm, yarn, bun
```

## Full Documentation

For complete documentation, examples, and configuration options, see our [detailed README](https://github.com/AlterraLink/setup-supabase-action).

## Example

```yaml
name: Validate Schema
on: [pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: AlterraLink/setup-supabase-action@v1
        with:
          package-manager: pnpm
      - run: pnpm supabase start
      - run: pnpm supabase db reset
      - run: pnpm supabase db diff
```

## License

MIT © AlterraLink
