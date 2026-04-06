# Next.js + Tailwind CSS Stack

**Loaded from**: `~/.claude/stacks/nextjs-tailwind.md`
**When to use**: Production web apps, SEO-critical sites, full-stack applications

---

## Tech Stack

**Framework:**
- Next.js (App Router or Pages Router - check project setup)
- React + TypeScript
- Tailwind CSS v4 (no config file)

**Common Additions:**
- Vercel for deployment
- shadcn/ui for components
- next-auth for authentication
- Prisma or Drizzle for database ORM

---

## Project Structure

**App Router (modern):**
```
app/
├── (routes)/
│   ├── page.tsx          # Home page
│   ├── about/
│   │   └── page.tsx      # /about route
│   └── api/
│       └── [...]/
│           └── route.ts  # API routes
├── layout.tsx            # Root layout
├── loading.tsx           # Loading UI
├── error.tsx             # Error UI
└── not-found.tsx         # 404 page

components/
├── ui/                   # shadcn/ui components
└── [feature]/            # Feature components

lib/                      # Utilities and configs
public/                   # Static assets
```

**Pages Router (legacy):**
```
pages/
├── index.tsx             # Home page
├── about.tsx             # /about route
├── api/                  # API routes
│   └── [...].ts
└── _app.tsx              # App wrapper

components/               # Same as App Router
lib/                      # Same as App Router
public/                   # Same as App Router
```

---

## Common Commands

```bash
# Development
npm install              # Install dependencies
npm run dev             # Start dev server (port 3000)

# Build & Deploy
npm run build           # Production build
npm start               # Start production server
npm run lint            # Run ESLint

# Type Check
npx tsc --noEmit        # TypeScript type checking
```

---

## Key Conventions

**Routing:**
- App Router: File-based routing in `app/` directory
- Pages Router: File-based routing in `pages/` directory
- Dynamic routes: `[id]/page.tsx` or `[id].tsx`
- Catch-all routes: `[...slug]/page.tsx` or `[...slug].tsx`

**Tailwind CSS v4:**
- **No `tailwind.config.js` file** — v4 doesn't use it
- Use built-in utility classes only
- Custom colors via Tailwind's built-in palette (e.g., `stone-500`, `lime-400`)
- No custom color definitions
- Responsive: `sm:`, `md:`, `lg:`, `xl:`, `2xl:` prefixes
- Dark mode: `dark:` prefix

**Path Aliases:**
- `@/` maps to root directory
- Configured in `tsconfig.json`:
  ```json
  {
    "compilerOptions": {
      "paths": {
        "@/*": ["./*"]
      }
    }
  }
  ```

---

## Data Fetching Patterns

**App Router (Server Components by default):**

```typescript
// Server Component (async, fetch on server)
export default async function Page() {
  const data = await fetch('https://api.example.com/data').then(r => r.json())
  return <div>{data.title}</div>
}

// Client Component (use 'use client' directive)
'use client'
export default function ClientPage() {
  const [data, setData] = useState(null)
  useEffect(() => {
    fetch('/api/data').then(r => r.json()).then(setData)
  }, [])
  return <div>{data?.title}</div>
}
```

**Pages Router (getServerSideProps, getStaticProps):**

```typescript
// Server-side rendering
export async function getServerSideProps() {
  const data = await fetch('https://api.example.com/data').then(r => r.json())
  return { props: { data } }
}

export default function Page({ data }) {
  return <div>{data.title}</div>
}

// Static generation
export async function getStaticProps() {
  const data = await fetch('https://api.example.com/data').then(r => r.json())
  return { props: { data }, revalidate: 60 } // ISR
}
```

---

## API Routes

**App Router:**
```typescript
// app/api/hello/route.ts
import { NextResponse } from 'next/server'

export async function GET(request: Request) {
  return NextResponse.json({ message: 'Hello' })
}

export async function POST(request: Request) {
  const body = await request.json()
  return NextResponse.json({ received: body })
}
```

**Pages Router:**
```typescript
// pages/api/hello.ts
import type { NextApiRequest, NextApiResponse } from 'next'

export default function handler(req: NextApiRequest, res: NextApiResponse) {
  if (req.method === 'GET') {
    res.status(200).json({ message: 'Hello' })
  } else if (req.method === 'POST') {
    res.status(200).json({ received: req.body })
  }
}
```

---

## Environment Variables

**Local (`.env.local`):**
```
DATABASE_URL=postgresql://...
NEXTAUTH_SECRET=your-secret
NEXTAUTH_URL=http://localhost:3000
```

**Access in Code:**
- Server-side: `process.env.VARIABLE_NAME`
- Client-side: `process.env.NEXT_PUBLIC_VARIABLE_NAME` (must have `NEXT_PUBLIC_` prefix)

---

## Authentication (next-auth)

**Setup:**
```typescript
// app/api/auth/[...nextauth]/route.ts (App Router)
// or pages/api/auth/[...nextauth].ts (Pages Router)
import NextAuth from 'next-auth'
import GoogleProvider from 'next-auth/providers/google'

export const authOptions = {
  providers: [
    GoogleProvider({
      clientId: process.env.GOOGLE_CLIENT_ID,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET
    })
  ]
}

export default NextAuth(authOptions)
```

**Check Session:**
```typescript
import { useSession } from 'next-auth/react'

export default function Component() {
  const { data: session, status } = useSession()
  if (status === 'loading') return <div>Loading...</div>
  if (!session) return <div>Not logged in</div>
  return <div>Welcome, {session.user.name}</div>
}
```

---

## shadcn/ui Integration

**Install Components:**
```bash
npx shadcn-ui@latest add button
npx shadcn-ui@latest add card
npx shadcn-ui@latest add dialog
```

**Components go in:**
- `components/ui/` directory
- Import with `@/components/ui/[component]`

**Configuration:**
Check `components.json` for project-specific settings.

---

## Image Optimization

**Next.js Image Component:**
```typescript
import Image from 'next/image'

<Image
  src="/photo.jpg"
  alt="Photo"
  width={500}
  height={300}
  priority // Above fold images
/>
```

**Remote Images:**
Add domains to `next.config.js`:
```javascript
module.exports = {
  images: {
    domains: ['example.com']
  }
}
```

---

## Metadata & SEO

**App Router:**
```typescript
// app/page.tsx
export const metadata = {
  title: 'Page Title',
  description: 'Page description',
  openGraph: {
    title: 'OG Title',
    description: 'OG Description',
    images: ['/og-image.jpg']
  }
}
```

**Pages Router:**
```typescript
import Head from 'next/head'

<Head>
  <title>Page Title</title>
  <meta name="description" content="Page description" />
</Head>
```

---

## Performance Tips

- Use `next/image` for automatic image optimization
- Enable `output: 'standalone'` in `next.config.js` for Docker deployments
- Use dynamic imports for code splitting:
  ```typescript
  const Component = dynamic(() => import('@/components/Heavy'), {
    loading: () => <div>Loading...</div>
  })
  ```
- Avoid large client bundles — keep heavy logic server-side (App Router)
- Use Incremental Static Regeneration (ISR) for semi-dynamic content

---

## Deployment (Vercel)

**Automatic:**
- Connect GitHub repo to Vercel
- Auto-deploy on push to main branch

**Manual:**
```bash
vercel --prod
```

**Environment Variables:**
Set in Vercel dashboard (Settings → Environment Variables)

---

## Common Issues

**"use client" Directive:**
- Required for any component using hooks (`useState`, `useEffect`, etc.) in App Router
- Place at the top of the file

**Hydration Errors:**
- Server and client HTML must match
- Avoid random values, dates, or conditional rendering that differs between server/client

**API Route CORS:**
Add CORS headers manually:
```typescript
export async function GET(request: Request) {
  return NextResponse.json(data, {
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Methods': 'GET, POST, OPTIONS'
    }
  })
}
```

---

**This stack is production-ready and optimized for Vercel deployment.**
