# React 19 + Vite SPA Project

## Tech Stack
- React 19 + TypeScript, built with Vite 6
- React Router v7 for routing
- Zustand for state management (Jotai for lightweight cases)
- Tailwind CSS 4 for styling
- Vitest + React Testing Library for testing

## Project Structure
```
src/
  components/    # Reusable UI components (presentational only, no business logic)
  pages/         # Page-level components mapped to routes
  hooks/         # Custom hooks
  stores/        # Zustand store definitions
  lib/           # Utility functions, API clients
  types/         # TypeScript type definitions
```

## Development Guidelines
- Write components as functions with TypeScript; define Props using `interface`
- Prefer React 19 features: `use()` hook for Promises/Context, `useOptimistic` for optimistic updates
- Be aware of Server Components concepts, but this is a pure SPA — all components are Client Components
- Use `React.lazy()` + `Suspense` for route-level code splitting
- State management principles: `useState` for component-local state, Zustand for cross-component sharing, TanStack Query for server state

## Routing
- React Router v7 uses the `createBrowserRouter` + `RouterProvider` pattern
- Load data with `loader`, handle form submissions with `action`
- All route definitions live in `src/router.tsx`

## Styling
- Prefer Tailwind CSS utility classes; manage conditional/variant styles with `clsx` or `cva`

## Testing
- Run tests: `npx vitest` or `npx vitest run`
- Single file: `npx vitest src/components/Button.test.tsx`
- Component tests use `render` + `screen` + `userEvent`; query the DOM by role/text first
- For async assertions, use `waitFor` or `findBy*` queries

## Common Commands
```bash
npm run dev          # Start dev server (default port 5173)
npm run build        # Production build, outputs to dist/
npm run preview      # Preview production build
npm run lint         # Run ESLint
npm run test         # Run tests
```

## Common Pitfalls
- Tailwind v4 uses CSS-first configuration; `tailwind.config.js` is no longer needed
- When a route `loader` returns data, the component accesses it via `useLoaderData()`
