---
name: nextjs-performance-audit
description: Diagnose and fix performance in Next.js 16 apps: Core Web Vitals, streaming, caching, client bundle size, slow builds. Use when a page is slow, Lighthouse scores drop, or JS bundles grow.
---

# Next.js Performance Audit

Measure first, change second, re-measure third. Never optimize on intuition alone.

## 1. Baseline

- Pin down the exact route/interaction and the device/network profile (e.g. mid-range mobile, Slow 4G).
- Metrics and targets: **LCP** <= 2.5s, **INP** <= 200ms, **CLS** <= 0.1; also TTFB, total client JS, and long tasks.
- Prefer field data (RUM, CrUX, Vercel Speed Insights) when available; use Lighthouse and DevTools Performance traces for lab diagnosis. Test a **production build** (`next build && next start`), never `next dev`.
- Record numbers before changing anything.

## 2. Diagnose by symptom

| Symptom              | Check first                                                                                                                                                                     |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Slow LCP             | LCP element (usually an image or heading); `next/image` `priority` and `sizes`; slow TTFB from uncached dynamic rendering; data waterfalls; render-blocking third-party scripts |
| Poor INP             | Long tasks on interaction; big Client Component subtrees re-rendering; heavy handlers; unvirtualized long lists                                                                 |
| High CLS             | Images without dimensions, fonts not via `next/font`, late-injected banners/embeds, layout-property animations                                                                  |
| Large client JS      | Too many `"use client"` boundaries; big libraries shipped to the client; no dynamic imports; duplicated dependencies                                                            |
| Slow server response | Sequential awaits, uncached repeated fetches, N+1 queries, everything dynamic that could be cached                                                                              |
| Slow builds/dev      | Check Turbopack is in use; huge barrel files; oversized `node_modules` imports; type-checking time                                                                              |

## 3. Fixes, cheapest-first

**Rendering and caching**

- Move work to the server: keep components as Server Components by default so their JS never ships.
- Use Cache Components (`"use cache"`, `cacheLife`, `cacheTag`) to cache expensive server work; keep request-time reads (`cookies()`, `headers()`) out of cached scopes and wrap truly dynamic parts in `<Suspense>` so the static shell streams immediately (partial prerendering).
- Parallelize independent fetches with `Promise.all`; start fetches early and await late to remove waterfalls.
- Add `loading.tsx` / Suspense skeletons so slow data does not block first paint.

**Client JavaScript**

- Shrink `"use client"` boundaries; hoist static markup out of client components.
- `next/dynamic` (or `React.lazy`) for heavy, below-the-fold or interaction-gated components (charts, editors, maps, modals).
- Inspect with `@next/bundle-analyzer` (or the Turbopack bundle analysis tooling the installed version provides). Replace heavy libraries (moment, lodash whole-import, large icon packs) with lighter or per-module imports; use `optimizePackageImports` for known barrel-heavy packages.
- Defer third-party scripts with `next/script` (`strategy="lazyOnload"` or `afterInteractive`); audit and remove unneeded analytics/chat widgets.
- If the React Compiler is enabled, do **not** add manual `useMemo`/`useCallback`/`memo`. Fix re-render problems structurally: move state down, split context, avoid prop churn. Only add manual memoization when a profiler trace proves the compiler missed it.
- Virtualize or paginate long lists (TanStack Virtual); use `useTransition` / `useDeferredValue` to keep input responsive under heavy renders.

**Assets**

- `next/image` with accurate `sizes`, modern formats, `priority` (or `fetchPriority="high"`) on the LCP image only, lazy loading elsewhere.
- `next/font` with subsets and `display: swap`; limit font weights.
- Tailwind v4 already tree-shakes CSS; avoid dynamically built class names that force safelisting, and avoid large global CSS imports.

**Network and delivery**

- Immutable caching for hashed assets, CDN in front, correct `Cache-Control` for route handlers, prefetch via `next/link`.
- Keep `proxy.ts` light; it runs on every matched request.
- Colocate region of compute with the database; avoid cross-region round trips.

## 4. Verify and report

- Re-run the identical measurement; report before/after for each metric touched.
- Add guards: bundle-size budget or Lighthouse CI threshold in CI.
- Revert any change that does not move a metric. Complexity without measured gain is a cost.

## Output format

Lead with the top three findings ranked by impact. For each: evidence (metric or trace), root cause, fix, expected gain. Put lower-impact items in a short secondary list.
