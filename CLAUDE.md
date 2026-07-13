# Frontend

## Stack

- pnpm
- Vite (latest)
- React (latest)
- Mantine (latest)
- Zustand if global state needed (latest)
- TypeScript
- Tailwind CSS v4
- TanStack Query (latest) — server state
- React Router — routing
- Zod — schema validation
- pnpm — package manager

## Formatting

- Prettier + prettier-plugin-tailwindcss (auto class sorting)

## Components

- Keep components small; extract sub-components aggressively. Extract logic to utils only when complex (see Folder structure).
- Common/shared: `src/components/`

## Folder structure

- Per-page, colocated by feature tree
- Component file: `*.comp.tsx`
- Util file: `*.utils.ts` <-- only for complex logic (pure, non-UI, and reused or worth unit-testing)
- Reusable utils to src/utils/utils.ts

Example:
src/pages/home/home.comp.tsx
src/pages/home/parent/parent.comp.tsx
src/pages/home/parent/child1/child1.comp.tsx
src/pages/home/parent/child2/child2.comp.tsx
src/pages/home/parent/child2/child2.utils.ts <-- only for complex logic
src/utils/utils.ts <-- for common utils

## Conventions

- Validate external data (API responses, forms) with Zod
- Data fetching via TanStack Query, not raw effects
- Arrow functions only (`const fn = () => {}`) everywhere — components, handlers, callbacks, utils. No `function` declarations.

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
