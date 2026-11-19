---
name: nextjs-component-engineering
description: Build, refactor, debug and review Next.js 16 App Router applications with TypeScript and Tailwind CSS v4, including Server/Client Components, Server Actions, caching, accessibility, pages, components and forms. Use automatically whenever a task involves creating, modifying, debugging or reviewing Next.js application code. Can also be invoked manually using /nextjs-component-engineering.
---

# Next.js Component Engineering (Next.js 16 + TypeScript + Tailwind v4)

Engineering rules for the App Router. For visual/aesthetic direction, defer to the `frontend-design` skill.

## Before writing code

1. Check `package.json` for the real versions of `next`, `react`, `tailwindcss`, `typescript`. If `next` is below 16 or `tailwindcss` below 4, say so and follow the older conventions already in the repo instead of these.
2. Read the project's conventions: folder layout (`app/`, `components/`, `lib/`), lint/format setup, UI library (e.g. shadcn/ui), existing `cn()` helper, auth and data-access patterns.
3. Find one or two similar existing components and match them. Reuse existing primitives and tokens before adding new ones or new dependencies.

## Server vs Client Components

- Default to **Server Components**. Add `"use client"` only for state, effects, event handlers, browser APIs or client-only libraries.
- Push `"use client"` to the smallest leaf. Pass Server Components into Client Components as `children`/props to keep the boundary small.
- Never import server-only code (DB clients, secrets) into a Client Component. Mark such modules with `import "server-only"`.
- Props crossing the server-to-client boundary must be serializable (no functions except Server Actions, no class instances).
- Fetch data in Server Components, close to where it is used. Avoid client-side `useEffect` fetching; if client fetching is needed, use TanStack Query or SWR.
- Run independent fetches in parallel (`Promise.all`) to avoid waterfalls; stream slow parts with `<Suspense>` and a skeleton fallback.

## Next.js 16 specifics

- `params`, `searchParams`, `cookies()` and `headers()` are **async**: `const { id } = await params`. Type them as `Promise<...>`.
- `middleware.ts` is replaced by `proxy.ts`. Keep it thin: redirects, rewrites, header and auth gating only. No heavy logic or data fetching.
- `next lint` is gone. Run ESLint (or Biome) directly via a package script.
- Caching is explicit with **Cache Components** (`cacheComponents: true` in `next.config`): mark cacheable work with the `"use cache"` directive, set lifetime with `cacheLife`, tag with `cacheTag`, and invalidate with `revalidateTag` / `updateTag`. Anything not cached is dynamic by default. Do not rely on the old implicit `fetch` caching. Confirm exact API signatures in the project's installed Next.js docs before using them.
- Do not read request-time data (`cookies()`, `headers()`, `searchParams`) inside a cached scope; read it outside and pass values in as arguments.
- If the React Compiler is enabled (`reactCompiler: true`), do not hand-write `useMemo`/`useCallback`/`memo` for performance. Write plain, pure components and follow the Rules of React.
- Do not remove existing memoization blindly. Follow the project's React Compiler configuration and existing performance requirements.
- Use `next/image` (set `sizes`, `priority` only for the LCP image), `next/font` (no layout shift), `next/link` (prefetch by default), and the Metadata API for SEO.

## Server Actions and forms

- Treat every Server Action as a public endpoint: authenticate, authorize, and validate input on the server with a schema (Zod or similar) even if the client validates too.
- Return typed results (`{ ok: true, data } | { ok: false, errors }`) instead of throwing for expected failures.
- Use `useActionState` and `useFormStatus` for pending and error UI; use `useOptimistic` for instant feedback. Keep forms working without JS where practical (progressive enhancement).
- Invalidate caches after mutations (`updateTag`/`revalidateTag`/`revalidatePath`) and `redirect()` after success.
- Use `error.tsx`, `not-found.tsx`, `loading.tsx` and `global-error.tsx` for route-level states.

## TypeScript

- `strict: true`. No `any`; use `unknown` and narrow. Prefer `satisfies` over type assertions.
- Derive types from schemas (`z.infer`) so runtime and compile-time types cannot drift.
- Use discriminated unions for state; make illegal states unrepresentable.
- Type route props explicitly (`Promise<{ id: string }>` for `params`). Avoid exporting types from `"use client"` files that Server Components need; put shared types in neutral modules.
- Do not use TS `enum`; use `as const` objects or unions.

## Tailwind CSS v4

- Configuration is CSS-first: `@import "tailwindcss";` and tokens in an `@theme { --color-brand: ...; --font-sans: ...; }` block. Do not create a `tailwind.config.js` unless the repo already uses `@config` for legacy plugins.
- Use `@tailwindcss/postcss` (Next.js) in the PostCSS config.
- Custom utilities via `@utility`, custom variants via `@custom-variant` (e.g. class-based dark mode: `@custom-variant dark (&:where(.dark, .dark *));`). Add extra scan paths with `@source`.
- Reference CSS variables with the parentheses shorthand: `bg-(--brand)`, and the important modifier as a suffix: `p-4!`.
- v4 renames to remember: `bg-gradient-to-r` is now `bg-linear-to-r`; `shadow-sm` is now `shadow-xs` (and `shadow` is `shadow-sm`); `outline-none` is now `outline-hidden`; default border color is `currentColor`, so set border colors explicitly.
- Compose conditional classes with a `cn()` helper (`clsx` + `tailwind-merge`). Define variants with `cva` or `tailwind-variants` rather than long ternaries.
- Build with mobile-first responsive prefixes, container queries (`@container`, `@md:`) for component-level responsiveness, and tokens rather than arbitrary values (`w-[317px]` is a smell).
- Do not construct class names dynamically (`` `text-${color}-500` ``); Tailwind only sees complete static strings. Map values to full class names.

## Accessibility (non-negotiable)

- Semantic HTML first; ARIA only when no native element fits. Keyboard operable, visible focus (`focus-visible:` utilities), accessible names on all controls.
- Labels linked to inputs; errors linked via `aria-describedby`; `aria-invalid` on failure.
- Dialogs/menus: focus trap and restore, Escape to close. Prefer headless primitives (Radix, React Aria, Base UI) over hand-rolled ones.
- Meet WCAG AA contrast; never rely on color alone; honor `motion-reduce:` for animation.
- `next/image` always gets meaningful `alt` (or `alt=""` if decorative).

## Every component handles all states

Loading (skeleton/Suspense), empty, error, long content, disabled, and narrow viewports.

## Done checklist

- [ ] `tsc --noEmit` and ESLint clean; `next build` succeeds
- [ ] `"use client"` only where required; no server-only imports in client code
- [ ] Server Actions authenticate, authorize and validate
- [ ] Caching and invalidation explicit; no request-time data in cached scopes
- [ ] Keyboard-only walkthrough works; contrast and labels verified
- [ ] No hydration or key warnings in the console
- [ ] Tests added per `nextjs-testing`

When finishing, summarize what changed and flag trade-offs or follow-ups; do not recap steps.