# Complete AGENTS.md Example

This is a fully-fleshed example of an AGENTS.md for a typical SaaS application. Use this as reference when generating AGENTS.md files for real projects.

---

```markdown
# AGENTS.md

## Project Overview

Acme Dashboard is an internal admin tool for managing customer accounts, viewing analytics, and handling support tickets. Built with Next.js 14 and our internal design system.

**Tech Stack:**
- Framework: Next.js 14 (App Router)
- Language: TypeScript (strict mode)
- Styling: Tailwind CSS + @acme/ui design system
- State: Zustand for global state, React Query for server state
- Testing: Vitest + Testing Library
- Package Manager: pnpm

---

## Dev Environment

### Setup
```bash
pnpm install
cp .env.example .env.local
# Add your ACME_API_KEY to .env.local
```

### Commands
| Command | Purpose |
|---------|---------|
| `pnpm dev` | Start on localhost:3000 |
| `pnpm build` | Production build |
| `pnpm test` | Run Vitest |
| `pnpm test:watch` | Vitest in watch mode |
| `pnpm lint` | ESLint + Prettier |
| `pnpm typecheck` | tsc --noEmit |

---

## Code Style

### General Rules
- Use functional components with hooks exclusively
- Prefer named exports: `export function MyComponent()`
- Use absolute imports: `@/components/...`, `@/lib/...`
- Props interfaces named `[Component]Props`
- No default exports except for pages and layouts

### Naming Conventions
| Type | Convention | Example |
|------|------------|---------|
| Components | PascalCase | `UserProfile.tsx` |
| Hooks | camelCase with use prefix | `useAuth.ts` |
| Utilities | camelCase | `formatDate.ts` |
| Constants | SCREAMING_SNAKE_CASE | `API_ENDPOINTS.ts` |
| Types/Interfaces | PascalCase | `User`, `ApiResponse` |
| Files | kebab-case or PascalCase | `user-profile.tsx` or `UserProfile.tsx` |

### Component Structure
```typescript
// components/features/UserCard/UserCard.tsx
import { Card, Avatar, Badge } from '@acme/ui';
import type { User } from '@/types/user';

interface UserCardProps {
  user: User;
  onSelect?: (userId: string) => void;
}

export function UserCard({ user, onSelect }: UserCardProps) {
  return (
    <Card onClick={() => onSelect?.(user.id)}>
      <Avatar src={user.avatar} alt={user.name} />
      <Badge variant={user.status}>{user.status}</Badge>
    </Card>
  );
}
```

### File Organization
```
src/
├── app/                    # Next.js App Router
│   ├── (dashboard)/        # Dashboard route group
│   │   ├── layout.tsx
│   │   ├── dashboard/
│   │   │   └── page.tsx
│   │   └── settings/
│   │       └── page.tsx
│   ├── (auth)/             # Auth route group
│   │   ├── login/
│   │   └── signup/
│   ├── api/                # API routes
│   └── layout.tsx          # Root layout
├── components/
│   ├── ui/                 # Base components (from @acme/ui)
│   └── features/           # Feature-specific components
│       ├── UserCard/
│       │   ├── UserCard.tsx
│       │   └── UserCard.test.tsx
│       └── Dashboard/
├── hooks/
│   ├── api/                # React Query hooks
│   │   ├── useUsers.ts
│   │   └── useAnalytics.ts
│   └── useAuth.ts
├── lib/
│   ├── api.ts              # API client
│   ├── utils.ts            # Utility functions
│   └── constants.ts
├── types/
│   ├── user.ts
│   └── api.ts
└── stores/
    └── app-store.ts        # Zustand store
```

---

## Design System

### Package: @acme/ui

Always use components from `@acme/ui`. Never create custom buttons, inputs, modals, or cards.

### Key Components
| Component | Variants | Notes |
|-----------|----------|-------|
| `Button` | primary, secondary, ghost, danger | Always use for actions |
| `Input` | text, email, password, search | Includes validation states, pair with `Label` |
| `Select` | default, multi | Use `SelectItem` for options |
| `Card` | default, bordered, elevated | Use for all content containers |
| `DataTable` | - | For all tabular data, includes sorting/filtering |
| `Modal` | default, confirm, alert | Uses Portal |
| `Toast` | success, error, warning, info | Use `useToast()` hook |
| `Badge` | status variants | For status indicators |
| `Avatar` | sizes: sm, md, lg | For user images |

### Usage
```typescript
// Correct - import from package
import { Button, Card, Input } from '@acme/ui';

// Wrong - never import internals
import { Button } from '@acme/ui/dist/components/Button';
```

### Tokens
```typescript
// Spacing: use Tailwind classes that map to our tokens
// p-2 = 8px, p-4 = 16px, p-6 = 24px

// Colors: use semantic classes
// text-primary, bg-surface, border-muted
// text-success, text-error, text-warning

// Never use arbitrary values like p-[13px] or text-[#ff0000]
```

### Do NOT:
- Create new Button, Input, Card, or Modal components
- Use inline color values or arbitrary Tailwind values
- Import from @acme/ui/dist/* (internal paths)
- Override design system styles with custom CSS

---

## Testing

### Requirements
- All new components must have corresponding test files
- Test user interactions, not implementation details
- Mock external APIs using msw
- Minimum 80% coverage for new code

### Pattern
```typescript
// UserCard.test.tsx
import { render, screen, userEvent } from '@/test/utils';
import { UserCard } from './UserCard';

const mockUser = {
  id: '1',
  name: 'John Doe',
  avatar: '/avatar.jpg',
  status: 'active' as const,
};

describe('UserCard', () => {
  it('renders user name', () => {
    render(<UserCard user={mockUser} />);
    expect(screen.getByText('John Doe')).toBeInTheDocument();
  });

  it('calls onSelect when clicked', async () => {
    const onSelect = vi.fn();
    render(<UserCard user={mockUser} onSelect={onSelect} />);

    await userEvent.click(screen.getByRole('article'));

    expect(onSelect).toHaveBeenCalledWith('1');
  });

  it('displays user status badge', () => {
    render(<UserCard user={mockUser} />);
    expect(screen.getByText('active')).toBeInTheDocument();
  });
});
```

### Test File Location
Co-locate tests with components:
```
UserCard/
├── UserCard.tsx
├── UserCard.test.tsx
└── index.ts
```

---

## Dependencies

### Approved Libraries
| Purpose | Library | Notes |
|---------|---------|-------|
| Data Fetching | `@tanstack/react-query` | Use for all API calls |
| Forms | `react-hook-form` + `zod` | Schema validation required |
| Date Handling | `date-fns` | Preferred date library |
| Icons | `lucide-react` | Consistent icon set |
| State | `zustand` | For global client state |
| Tables | `@tanstack/react-table` | For complex tables |
| Charts | `recharts` | For data visualization |

### Do NOT Install
| Avoid | Use Instead | Reason |
|-------|-------------|--------|
| `moment` | `date-fns` | Bundle size |
| `lodash` | Native JS / individual imports | Bundle size |
| `axios` | Native `fetch` with our wrapper | Unnecessary abstraction |
| `styled-components` | Tailwind CSS | Consistency |
| Any CSS framework | Tailwind + @acme/ui | Design system consistency |

---

## API Conventions

### React Query Hooks
```typescript
// hooks/api/useUsers.ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { api } from '@/lib/api';

export function useUsers() {
  return useQuery({
    queryKey: ['users'],
    queryFn: () => api.get('/users'),
  });
}

export function useCreateUser() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (data: CreateUserInput) => api.post('/users', data),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
    },
  });
}
```

### API Client
```typescript
// lib/api.ts
const API_BASE = process.env.NEXT_PUBLIC_API_URL;

export const api = {
  get: async (path: string) => {
    const res = await fetch(`${API_BASE}${path}`);
    if (!res.ok) throw new Error(res.statusText);
    return res.json();
  },
  // ... post, put, delete
};
```

---

## Common Pitfalls

### Avoid These Mistakes

1. **Don't use `any` type** - Define types in `@/types/`
2. **Don't fetch in components** - Create hooks in `@/hooks/api/`
3. **Don't use useState for server data** - Use React Query
4. **Don't hardcode colors** - Use Tailwind semantic classes
5. **Don't skip loading states** - Always handle loading/error/empty
6. **Don't use index as key** - Use stable unique identifiers
7. **Don't use useEffect for data fetching** - Use React Query or Server Components
8. **Don't hardcode API URLs** - Use environment variables
9. **Don't create custom form handling** - Use react-hook-form
10. **Don't skip error boundaries** - Wrap feature components with error.tsx

### Performance Considerations
- Use `React.memo()` for expensive list items
- Lazy load routes with `dynamic()` imports
- Images must use `next/image`
- Use React Query's staleTime to reduce refetches

---

## Git Workflow

### Branch Naming
- Feature: `feature/ACME-123-add-user-profile`
- Bugfix: `fix/ACME-456-login-redirect`
- Refactor: `refactor/extract-auth-logic`

### Commit Messages
Follow Conventional Commits:
```
feat: add user profile page
fix: resolve login redirect issue
refactor: extract auth logic to hook
docs: update README with setup steps
test: add UserCard component tests
chore: update dependencies
```

### PR Requirements
- All tests passing
- No TypeScript errors
- Linting clean
- At least 1 approval required
- Squash merge to main
```

---

## Notes for AI Generators

This example demonstrates:

1. **Specificity**: Real package names, file paths, and patterns
2. **Actionable rules**: Clear do's and don'ts with examples
3. **Consistent structure**: Each section follows a predictable format
4. **Practical examples**: Code snippets that can be copied and adapted
5. **Complete coverage**: All major development concerns addressed

When generating for a real project, replace:
- `@acme/ui` with the actual design system package
- File paths with actual project structure
- Commands with actual package.json scripts
- Types with actual project type definitions
