# Resume Explorer (Magic MD Creator)

**Tech Stack**: `~/.claude/stacks/react-vite-supabase.md` (auto-loaded)
**Purpose**: AI Resume & Career Toolkit

---

## Quick Context

High-fidelity React application for managing job applications, tailoring resumes with AI, and tracking career progress.

---

## Tech Stack

- **Core**: Vite + React 18 + TypeScript
- **Styling**: Tailwind CSS + shadcn/ui (Radix UI)
- **Backend/Auth**: Supabase (@supabase/supabase-js)
- **State Management**: @tanstack/react-query
- **Icons**: Lucide React
- **Package Manager**: NPM
- **Testing**: Vitest (Unit) + Playwright (E2E)

---

## Commands

```bash
npm install         # Install dependencies
npm run dev         # Start development server
npm test            # Run unit tests (Vitest)
npm run build       # Build for production
npx playwright test # Run E2E tests
```

---

## Project Structure

- `src/pages/` - Application views (Dashboard, Jobs, Applications, Profile, etc.)
- `src/components/` - UI components (including `ui/` for shadcn primitives)
- `src/contexts/` - Context providers (e.g., `AuthContext`)
- `src/integrations/` - Third-party integrations (e.g., Supabase client)
- `src/hooks/` - Custom React hooks
- `supabase/` - Database migrations and Edge functions

---

## Features

- **Profile Completeness**: Track resume parsing and profile building progress
- **Job Snapshots**: Save and manage job descriptions for tailoring
- **Application Bundles**: Generate AI-tailored resumes and cover letters for specific jobs
- **General Resumes**: Create and manage master resumes
- **Onboarding Checklist**: Guide users through initial setup

---

## Current Status

**Stage**: Early scaffold
**Recent**: Lovable scaffolding complete, Index page placeholder
**Next**: Define feature scope and data model
