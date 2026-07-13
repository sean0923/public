# Frontend

## Stack

- TanStack Start (https://tanstack.com/start/latest)
- TanStack Router — routing (file-based, in `src/routes/`)
- TanStack Query — server state
- React 19
- Vite
- TypeScript
- Tailwind CSS v4
- Mantine — component library
- Zustand — global state (only when needed)
- Zod — schema validation
- pnpm — package manager

## Formatting

- Prettier + prettier-plugin-tailwindcss (auto class sorting)

## Components

- Keep components small; extract sub-components aggressively. Extract logic to utils only when complex (see Folder structure).
- Common/shared: `src/components/`

## Folder structure

Routing is file-based (TanStack Router), but the feature/component tree is kept
separate so we can still nest sub-components aggressively.

- **Routes** live in `src/routes/`. Keep each route file THIN — it holds only what
  needs the Route (path, `loader`, `validateSearch`, `params`, `head`/meta) and
  renders a single page component imported from `src/pages/`. No feature logic or
  markup in route files.
- `src/routeTree.gen.ts` is generated — never edit it. Regenerate with `pnpm generate-routes`.
- **Pages/features** live in `src/pages/<feature>/`, colocated by feature tree.
- Component file: `*.comp.tsx`
- Util file: `*.utils.ts` <-- only for complex logic (pure, non-UI, and reused or worth unit-testing)
- Reusable utils to `src/utils/utils.ts`
- Route → page data flow: pass loader/param data from the thin route file down as
  props (keeps page components portable). Reach for `Route.useLoaderData()` /
  `Route.useParams()` only when prop-threading is impractical.

Example:

```
src/routes/index.tsx                         <-- thin route entry
src/pages/home/home.comp.tsx
src/pages/home/parent/parent.comp.tsx
src/pages/home/parent/child1/child1.comp.tsx
src/pages/home/parent/child2/child2.comp.tsx
src/pages/home/parent/child2/child2.utils.ts <-- only for complex logic
src/utils/utils.ts                           <-- for common utils
```

Thin route entry:

```tsx
// src/routes/index.tsx
import { createFileRoute } from "@tanstack/react-router";
import { Home } from "#/pages/home/home.comp";

export const Route = createFileRoute("/")({ component: Home });
```

## TanStack Start conventions

- **Server functions FIRST** — for ALL app-internal server calls, use
  `createServerFn` (typed input via `.inputValidator`, typed return — including
  streaming via `ReadableStream<T>`). Server fn files: `src/server/**/*.fn.ts`.
  ```tsx
  import { createServerFn } from '@tanstack/react-start'
  const getData = createServerFn({ method: 'POST' })
    .inputValidator((data) => /* validate, return typed */)
    .handler(async ({ data }) => { /* ... */ })
  ```
- **API routes (route `server.handlers`) ONLY for external consumers** —
  webhooks, non-Start clients, endpoints needing raw HTTP semantics (status
  codes, EventSource). Never for calls made by our own components.
  ```tsx
  import { json } from "@tanstack/react-start";
  export const Route = createFileRoute("/api/hello")({
    server: { handlers: { GET: () => json({ ok: true }) } },
  });
  ```
- **Loaders vs TanStack Query**: use the route `loader` for route-entry data (runs
  before render, SSR-friendly). Use TanStack Query for client-side caching, mutations,
  and dependent/refetching fetches. Default to the `loader` for a route's primary data.

## Conventions

- Validate external data (API responses, forms) with Zod
- Data fetching via TanStack Query / route loaders, not raw effects
- Arrow functions only (`const fn = () => {}`) everywhere — components, handlers, callbacks, utils. No `function` declarations.
- No `useReducer` — use `useState` (functional updates `set(prev => ...)` for complex state); reach for Zustand if state must be global.

## Performance / memoization

- Do NOT reach for useMemo/useCallback by default. Prefer plain calculation during render.
- Only use useMemo when EITHER:
  - the computation is measurably expensive (profile first), OR
  - the value is a dep of another hook (useEffect/useMemo) or a prop to a
    memoized (React.memo) child, where reference stability matters.
- Only use useCallback when the function is passed to a memoized child or used
  in a hook dep array.
- Never memoize to "avoid recalculating" cheap array ops (filter/map/sort on
  small lists) — the memo costs more than it saves.
- Prefer fixing the root cause (lift state, split components, pass primitives)
  over memoizing to patch re-renders.
