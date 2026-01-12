# Next.js App Router agents.md Template

Use this template for Next.js 13+ projects using the App Router (app directory).

## Additional Analysis Steps

Before generating, confirm App Router usage:

**Check for App Router:**
- Look for `app/` or `src/app/` directory
- Check Next.js version in package.json (should be 13+)
- Look for `page.tsx` and `layout.tsx` files in the app directory

**Identify patterns:**
- Check for route groups (directories in parentheses)
- Look for API routes in `app/api/`
- Identify Server vs Client component usage (`'use client'` directives)

## Next.js App Router Section Template

Add this section to the agents.md for Next.js App Router projects:

```markdown
## Next.js App Router Conventions

### File Conventions

| File | Purpose | Renders |
|------|---------|---------|
| `page.tsx` | Route UI | Required for route to be accessible |
| `layout.tsx` | Shared layout wrapper | Wraps page and nested layouts |
| `loading.tsx` | Loading UI (Suspense boundary) | Shown while page loads |
| `error.tsx` | Error boundary | Shown on errors, must be Client Component |
| `not-found.tsx` | 404 UI | Shown for `notFound()` or unknown routes |
| `route.ts` | API endpoint | GET, POST, etc. handlers |
| `template.tsx` | Re-rendered layout | Like layout but remounts on navigation |
| `default.tsx` | Parallel route fallback | For parallel routes |

### Server vs Client Components

**Default to Server Components.** Only add `'use client'` when you need:
- React hooks (`useState`, `useEffect`, etc.)
- Browser APIs (`window`, `localStorage`, etc.)
- Event handlers (`onClick`, `onChange`, etc.)
- Class components

```typescript
// Server Component (default) - can fetch data directly
export default async function Page() {
  const data = await fetchData(); // Direct async/await
  return <div>{data.title}</div>;
}

// Client Component - for interactivity
'use client';
export default function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

### Component Hierarchy Best Practice

```
layout.tsx (Server) - Fetch user session, shared data
├── page.tsx (Server) - Fetch page-specific data
│   ├── StaticContent (Server) - Render static parts
│   └── InteractiveWidget (Client) - Handle user interaction
```

Keep Client Components small and at the leaves of your component tree.

### Data Fetching

**In Server Components:**
```typescript
// Fetch directly - no useEffect needed
export default async function Page() {
  const data = await fetch('https://api.example.com/data', {
    next: { revalidate: 3600 } // Cache for 1 hour
  });
  return <DataDisplay data={data} />;
}
```

**Caching Options:**
```typescript
// Cache indefinitely (default)
fetch(url)

// Revalidate every hour
fetch(url, { next: { revalidate: 3600 } })

// No cache
fetch(url, { cache: 'no-store' })

// Cache with tags for on-demand revalidation
fetch(url, { next: { tags: ['posts'] } })
```

**Client-Side Data:**
Use React Query for client-side data needs that require interactivity.

### Route Groups

Use parentheses to organize routes without affecting URL:

```
app/
├── (marketing)/
│   ├── layout.tsx      # Marketing layout (header, footer)
│   ├── page.tsx        # Home page (/)
│   ├── about/page.tsx  # About page (/about)
│   └── pricing/page.tsx
├── (dashboard)/
│   ├── layout.tsx      # Dashboard layout (sidebar, auth)
│   ├── dashboard/page.tsx  # Dashboard (/dashboard)
│   └── settings/page.tsx
└── (auth)/
    ├── layout.tsx      # Minimal auth layout
    ├── login/page.tsx
    └── signup/page.tsx
```

### Dynamic Routes

| Pattern | Example | Matches |
|---------|---------|---------|
| `[slug]` | `app/blog/[slug]/page.tsx` | `/blog/hello`, `/blog/world` |
| `[...slug]` | `app/docs/[...slug]/page.tsx` | `/docs/a`, `/docs/a/b/c` |
| `[[...slug]]` | `app/[[...slug]]/page.tsx` | `/`, `/a`, `/a/b` |

```typescript
// app/blog/[slug]/page.tsx
export default function Page({ params }: { params: { slug: string } }) {
  return <div>Post: {params.slug}</div>;
}

// Generate static paths
export async function generateStaticParams() {
  const posts = await getPosts();
  return posts.map((post) => ({ slug: post.slug }));
}
```

### Parallel Routes & Intercepting Routes

**Parallel Routes** (`@folder`):
```
app/
├── @modal/
│   └── (.)photo/[id]/page.tsx  # Intercepted modal
├── @sidebar/
│   └── page.tsx
├── layout.tsx  # Receives { modal, sidebar } as props
└── page.tsx
```

**Intercepting Routes**:
- `(.)` - Same level
- `(..)` - One level up
- `(..)(..)` - Two levels up
- `(...)` - From root

### Metadata

```typescript
// Static metadata
export const metadata: Metadata = {
  title: 'Page Title',
  description: 'Page description',
};

// Dynamic metadata
export async function generateMetadata({ params }): Promise<Metadata> {
  const post = await getPost(params.slug);
  return { title: post.title };
}
```

### Server Actions

```typescript
// app/actions.ts
'use server';

export async function createPost(formData: FormData) {
  const title = formData.get('title');
  await db.posts.create({ title });
  revalidatePath('/posts');
}

// In component
<form action={createPost}>
  <input name="title" />
  <button type="submit">Create</button>
</form>
```
```

## Common Pitfalls for Next.js App Router

Add these to the Common Pitfalls section:

```markdown
### Next.js App Router Pitfalls

1. **Don't add 'use client' unnecessarily** - Server Components are the default and more performant
2. **Don't fetch in Client Components** - Fetch in Server Components or use React Query
3. **Don't use `useEffect` for initial data** - Use async Server Components instead
4. **Don't forget loading.tsx** - Add loading states for better UX
5. **Don't use `window` in Server Components** - Check for client-side before using browser APIs
6. **Don't import server-only code in Client Components** - Use `server-only` package to prevent
7. **Don't pass functions as props to Client Components** - Functions can't be serialized
8. **Don't forget error.tsx must be a Client Component** - Add `'use client'` directive
9. **Don't use `router.push` for forms** - Use Server Actions or form actions
10. **Don't forget to revalidate** - Use `revalidatePath()` or `revalidateTag()` after mutations
```

## Environment Variables

```markdown
### Environment Variables

| Prefix | Accessible In | Use Case |
|--------|---------------|----------|
| `NEXT_PUBLIC_` | Client + Server | Public API URLs, feature flags |
| None | Server only | API keys, database URLs, secrets |

```typescript
// Server Component - access any env var
const apiKey = process.env.API_KEY;

// Client Component - only NEXT_PUBLIC_ vars
const publicUrl = process.env.NEXT_PUBLIC_API_URL;
```

**Never expose secrets to the client.** If you need API keys on the client, proxy through a Route Handler.
```
