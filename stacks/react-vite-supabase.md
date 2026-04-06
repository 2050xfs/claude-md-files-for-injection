# React + Vite + Supabase Stack

**Loaded from**: `~/.claude/stacks/react-vite-supabase.md`
**When to use**: Web apps with real-time features, auth, database, and file storage

---

## Tech Stack

**Frontend:**
- React 18 + TypeScript
- Vite (build tool and dev server)
- Tailwind CSS for styling
- shadcn/ui components (Radix UI primitives)

**Backend/Services:**
- Supabase (PostgreSQL database)
- Supabase Auth (magic link, OAuth, JWT)
- Supabase Storage (file uploads)
- Supabase Edge Functions (Deno-based serverless)
- Supabase Realtime (WebSocket subscriptions)

**State Management:**
- TanStack React Query (server state, caching, mutations)
- React Context (client state, minimal use)

**Icons & UI:**
- Lucide React (icon library)
- Radix UI primitives via shadcn/ui

---

## Project Structure

```
src/
├── components/
│   ├── ui/              # shadcn/ui components
│   └── [feature]/       # Feature-specific components
├── pages/               # Page components (routing)
├── contexts/            # React Context providers
├── hooks/               # Custom React hooks
├── integrations/
│   └── supabase/        # Supabase client setup
├── utils/               # Helper functions
└── types/               # TypeScript types

supabase/
├── functions/           # Edge Functions
├── migrations/          # Database migrations
└── config.toml          # Supabase configuration
```

---

## Common Commands

```bash
# Development
npm install              # Install dependencies
npm run dev             # Start dev server (usually port 8080 or 5173)

# Build & Preview
npm run build           # Production build
npm run preview         # Preview production build locally

# Code Quality
npm run lint            # Run ESLint
npm run type-check      # TypeScript type checking

# Supabase (if using local dev)
supabase start          # Start local Supabase
supabase db reset       # Reset database to migrations
supabase functions serve # Serve edge functions locally
```

---

## Key Conventions

**Path Aliases:**
- `@/` maps to `src/` directory
- Configured in `vite.config.ts` and `tsconfig.json`

**shadcn/ui Components:**
- All UI primitives in `src/components/ui/`
- Import with `@/components/ui/[component]`
- Add new components: `npx shadcn-ui@latest add [component-name]`

**Supabase Client:**
- Client instance in `src/integrations/supabase/client.ts`
- Always use the singleton client, never create multiple instances

**Edge Functions:**
- Deno runtime (not Node.js)
- CORS must be handled manually in each function
- Use Supabase service role key for admin operations
- Standard pattern:
  ```typescript
  import { serve } from "https://deno.land/std@0.168.0/http/server.ts"
  import { createClient } from "https://esm.sh/@supabase/supabase-js@2"

  serve(async (req) => {
    // CORS preflight
    if (req.method === 'OPTIONS') {
      return new Response('ok', { headers: corsHeaders })
    }

    // Function logic
    return new Response(JSON.stringify(data), {
      headers: { ...corsHeaders, 'Content-Type': 'application/json' }
    })
  })
  ```

**React Query Patterns:**
- Queries for fetching: `useQuery(['key'], fetchFn)`
- Mutations for updates: `useMutation(mutateFn)`
- Invalidate queries after mutations to refetch data
- Example:
  ```typescript
  const queryClient = useQueryClient()
  const mutation = useMutation({
    mutationFn: updateItem,
    onSuccess: () => {
      queryClient.invalidateQueries(['items'])
    }
  })
  ```

---

## Environment Variables

**Frontend (`.env`):**
```
VITE_SUPABASE_URL=your_supabase_project_url
VITE_SUPABASE_ANON_KEY=your_anon_public_key
```

**Edge Functions (set in Supabase dashboard):**
```
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key
```

---

## Database & Migrations

**Creating Migrations:**
```bash
supabase migration new migration_name
```

**Migration Files:**
- Located in `supabase/migrations/`
- Timestamped file names (e.g., `20240101000000_create_users.sql`)
- Include both `up` (create) and `down` (rollback) logic when possible

**Row Level Security (RLS):**
- Always enable RLS on tables with sensitive data
- Define policies for select, insert, update, delete
- Use `auth.uid()` to scope queries to current user
- Example:
  ```sql
  ALTER TABLE posts ENABLE ROW LEVEL SECURITY;

  CREATE POLICY "Users can read own posts"
    ON posts FOR SELECT
    USING (auth.uid() = user_id);
  ```

---

## Authentication Patterns

**Magic Link:**
```typescript
await supabase.auth.signInWithOtp({ email })
```

**OAuth (Google, GitHub, etc.):**
```typescript
await supabase.auth.signInWithOAuth({ provider: 'google' })
```

**Session Handling:**
```typescript
// Get current session
const { data: { session } } = await supabase.auth.getSession()

// Listen for auth state changes
supabase.auth.onAuthStateChange((event, session) => {
  // Update app state
})
```

**Protected Routes:**
Use auth context to check if user is logged in, redirect to `/auth` if not.

---

## Realtime Subscriptions

**Subscribe to table changes:**
```typescript
const subscription = supabase
  .channel('table-changes')
  .on('postgres_changes', {
    event: '*',
    schema: 'public',
    table: 'table_name'
  }, (payload) => {
    console.log('Change received:', payload)
  })
  .subscribe()

// Cleanup
subscription.unsubscribe()
```

**Use with React Query:**
Combine subscriptions with `queryClient.invalidateQueries()` to trigger refetches.

---

## File Storage

**Upload File:**
```typescript
const { data, error } = await supabase.storage
  .from('bucket-name')
  .upload('path/to/file.png', file)
```

**Get Public URL:**
```typescript
const { data } = supabase.storage
  .from('bucket-name')
  .getPublicUrl('path/to/file.png')

const publicURL = data.publicUrl
```

**Storage Policies:**
Configure bucket permissions in Supabase dashboard (public vs authenticated access).

---

## Common Patterns

**Fetch Data on Mount:**
```typescript
const { data, isLoading, error } = useQuery(['items'], async () => {
  const { data, error } = await supabase.from('items').select('*')
  if (error) throw error
  return data
})
```

**Mutation with Optimistic Update:**
```typescript
const mutation = useMutation({
  mutationFn: async (newItem) => {
    const { data, error } = await supabase.from('items').insert([newItem])
    if (error) throw error
    return data
  },
  onMutate: async (newItem) => {
    await queryClient.cancelQueries(['items'])
    const previous = queryClient.getQueryData(['items'])
    queryClient.setQueryData(['items'], (old) => [...old, newItem])
    return { previous }
  },
  onError: (err, newItem, context) => {
    queryClient.setQueryData(['items'], context.previous)
  },
  onSettled: () => {
    queryClient.invalidateQueries(['items'])
  }
})
```

---

## Performance Tips

- Use React Query's `staleTime` and `cacheTime` to reduce unnecessary fetches
- Enable Supabase connection pooling for high-traffic apps
- Use `select()` to fetch only needed columns, not `select('*')`
- Index frequently queried columns in PostgreSQL
- Use Edge Functions for heavy computations (keep client bundle small)

---

## Deployment

**Frontend (Vercel/Netlify):**
- Build command: `npm run build`
- Output directory: `dist`
- Set environment variables in hosting dashboard

**Supabase:**
- Deploy Edge Functions: `supabase functions deploy function-name`
- Apply migrations: automatically applied via Supabase dashboard or CLI

---

**This stack is optimized for rapid prototyping with production-grade architecture.**
