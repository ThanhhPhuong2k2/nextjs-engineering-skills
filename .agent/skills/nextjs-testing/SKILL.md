---
name: nextjs-testing
description: Write and maintain tests for Next.js 16 + TypeScript apps with Vitest, Testing Library, MSW and Playwright, including Server Components, Server Actions and accessibility. Use when adding or fixing tests.
---

# Next.js Testing (Vitest + Testing Library + MSW + Playwright)

Test behavior the user can observe, not implementation details. Fewer, meaningful tests beat exhaustive brittle ones.

## Detect the stack first

Check `package.json` and config files for the runner and libraries already in use (Vitest or Jest, Testing Library, MSW, Playwright or Cypress). Use what exists; do not add a second framework. Default to Vitest + `@testing-library/react` + `@testing-library/user-event` + MSW + Playwright for new setups.

## Choose the level by what the code is

| Code under test | Level | Tool |
|---|---|---|
| Pure functions, schemas (Zod), formatters, reducers, `cn`/utility logic | Unit | Vitest |
| Custom hooks with logic | Unit | Vitest + `renderHook` |
| **Client Components** (`"use client"`) | Component | Vitest + Testing Library |
| **Async Server Components**, layouts, route-level data flows, `proxy.ts` redirects, caching behavior | **E2E** | Playwright against a production build |
| **Server Actions** | Unit the logic (validation, auth, data access functions) + e2e the form wiring | Vitest + Playwright |
| Route Handlers (`route.ts`) | Unit/integration: call the exported `GET`/`POST` with a `Request` and assert on the `Response` | Vitest |

Key constraint: Testing Library and Vitest do not reliably render **async Server Components**. Do not fight this. Extract logic into plain functions and test those, keep Server Components thin, and cover them with Playwright. A synchronous Server Component that takes props and renders markup can be rendered in tests, but prefer e2e for anything involving data fetching.

Keep the pyramid: many unit/component tests, a few integration tests, very few e2e tests covering critical journeys (sign-in, checkout, core CRUD).

## Component test rules

- Query like a user: `getByRole` (with accessible name) first, then `getByLabelText`, `getByText`. `data-testid` is a last resort.
- Use `userEvent` (call `userEvent.setup()` per test), not `fireEvent`. Await async UI with `findBy*`/`waitFor`; never use arbitrary sleeps.
- Assert on visible outcomes (text, roles, aria state, navigation calls), not component state or internal calls.
- No whole-tree snapshots. Use small, targeted assertions.
- Mock at the network boundary with **MSW**, not by stubbing your own modules. Reset handlers between tests.
- Mock Next internals narrowly: `vi.mock("next/navigation", ...)` for `useRouter`, `usePathname`, `useSearchParams`; mock `next/image` only if the test env requires it.
- Test Server Action-driven client forms by passing a mocked action function and asserting pending, success and error UI (`useActionState`, `useFormStatus`).
- Each test is independent: no shared mutable state, no order dependence. Freeze time (`vi.useFakeTimers`, `vi.setSystemTime`) for date-dependent output.

## What to cover for a component

1. Default render with typical props
2. Each meaningful interaction (click, type, keyboard, submit)
3. Loading, empty and error states
4. Edge inputs: long text, zero/one/many items, invalid values
5. Accessibility: correct roles and names; add `vitest-axe` (or `jest-axe`) for key components and `@axe-core/playwright` for key pages

## Server Action and validation tests

- Test the validation schema directly with valid, invalid and boundary inputs.
- Test the action's core function with auth mocked: unauthenticated, unauthorized, invalid input, success.
- Assert it returns the typed error shape for expected failures, and that cache invalidation is triggered where intended (mock `revalidateTag`/`revalidatePath` from `next/cache`).

## Playwright rules

- Run against `next build && next start` in CI (use the `webServer` option), not the dev server, so caching and streaming behave as in production.
- Use role/label locators and web-first assertions (`expect(locator).toBeVisible()`); rely on auto-waiting, no fixed timeouts.
- Seed state through API or fixtures and reuse authenticated `storageState`; do not log in through the UI in every test.
- Mock third-party services with `page.route`. Keep each test to one journey.
- Keep traces, screenshots and video on failure; shard in CI if the suite grows.

## Flaky test protocol

1. Reproduce by running the test repeatedly (20-50x) under CI conditions (`--repeat-each` in Playwright).
2. Find the cause: unawaited async work, timers, animation, shared state, network race, time/locale dependence, dev-server vs production differences.
3. Fix the cause (await the real condition, fake timers, freeze the clock). Do not add retries or longer timeouts as the fix.

## Done checklist

- [ ] Tests fail when the behavior is deliberately broken
- [ ] Server Components covered by e2e, not forced into unit tests
- [ ] No `act` warnings or console errors
- [ ] Names describe behavior ("shows an error when the email is invalid")
- [ ] Fast and deterministic locally and in CI
- [ ] `tsc --noEmit` passes on test files too