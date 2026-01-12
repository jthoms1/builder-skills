# Monorepo agents.md Template

Use this template when the repository uses Turborepo, Nx, pnpm workspaces, or similar monorepo tooling.

## Additional Analysis Steps

Before generating, identify the monorepo structure:

**Check for monorepo tooling:**
- Look for `turbo.json`, `nx.json`, `pnpm-workspace.yaml`, or `lerna.json` at root
- Check `packages/`, `apps/`, `libs/` directories
- Examine root `package.json` for `workspaces` configuration

**Identify packages:**
- List all directories in `packages/`, `apps/`, `libs/`
- Read each package's `package.json` for name and purpose
- Note internal dependencies between packages

## Monorepo Section Template

Add this section to the agents.md for monorepo projects:

```markdown
## Monorepo Structure

This is a monorepo managed by [Turborepo/Nx/pnpm workspaces].

### Packages
| Package | Path | Purpose |
|---------|------|---------|
| `@acme/ui` | `packages/ui` | Design system components |
| `@acme/utils` | `packages/utils` | Shared utilities |
| `@acme/config` | `packages/config` | Shared configuration |
| `web` | `apps/web` | Main web application |
| `docs` | `apps/docs` | Documentation site |
| `api` | `apps/api` | Backend API server |

### Commands

```bash
# Install dependencies for all packages
pnpm install

# Run specific package
pnpm --filter web dev
pnpm --filter @acme/ui dev

# Build all packages
pnpm turbo run build

# Build specific package with dependencies
pnpm turbo run build --filter=web...

# Add dependency to specific package
pnpm --filter web add [package]

# Add shared dependency to root
pnpm add -w [package]

# Run tests across all packages
pnpm turbo run test

# Run linting across all packages
pnpm turbo run lint
```

### Cross-Package Imports
- Import from package name: `import { Button } from '@acme/ui'`
- Never use relative paths across packages (e.g., `../../packages/ui`)
- All internal packages should be listed in `dependencies`, not linked via relative paths

### Package Development

When creating new packages:
1. Create directory in `packages/` or `apps/`
2. Add `package.json` with correct `name` field
3. Add to `turbo.json` pipeline if needed
4. Export from package's `index.ts`

### Dependency Management

| Scope | Where to Install | Example |
|-------|-----------------|---------|
| Single package | Package directory | `pnpm --filter web add react-query` |
| Multiple packages | Each package | Add to each package.json |
| Dev tooling | Root | `pnpm add -wD typescript eslint` |
| Shared runtime | Shared package | Create `@acme/utils` |

### Build Order

Turborepo handles build order via `dependsOn` in `turbo.json`. Ensure:
- Shared packages build before apps
- `^build` means "build dependencies first"
- Don't create circular dependencies
```

## Nx-Specific Additions

For Nx monorepos, add:

```markdown
### Nx Commands

```bash
# Generate new library
nx g @nx/react:library my-lib

# Generate new application
nx g @nx/next:application my-app

# Run affected tests
nx affected:test

# Visualize dependency graph
nx graph

# Run task for specific project
nx run my-app:build
```

### Project Configuration

Each project has `project.json` defining:
- Build targets and executors
- Dependencies and tags
- Test configuration

### Module Boundaries

Enforce module boundaries via `@nx/enforce-module-boundaries`:
- Libraries tagged by scope (feature, ui, data-access, util)
- Apps can import from libraries, not other apps
- Shared libraries in `libs/shared/`
```

## Turborepo-Specific Additions

For Turborepo monorepos, add:

```markdown
### Turborepo Configuration

`turbo.json` defines the build pipeline:

```json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**", ".next/**"]
    },
    "test": {
      "dependsOn": ["build"]
    },
    "lint": {},
    "dev": {
      "cache": false,
      "persistent": true
    }
  }
}
```

### Caching

Turborepo caches build outputs. To force rebuild:
```bash
pnpm turbo run build --force
```

### Remote Caching

If using Vercel Remote Cache:
```bash
npx turbo login
npx turbo link
```
```

## Common Pitfalls for Monorepos

Add these to the Common Pitfalls section:

```markdown
### Monorepo-Specific Pitfalls

1. **Don't use relative imports across packages** - Use package names: `@acme/ui` not `../../packages/ui`
2. **Don't forget to build dependencies** - Run `turbo build` to build in correct order
3. **Don't install package-specific deps at root** - Use `--filter` or `-w` appropriately
4. **Don't create circular dependencies** - Use `nx graph` or manual review to check
5. **Don't bypass the build system** - Always use turbo/nx commands for consistency
6. **Don't duplicate shared code** - Extract to a shared package in `packages/`
```
