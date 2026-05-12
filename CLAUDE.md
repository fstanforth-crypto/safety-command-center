@AGENTS.md

# safety-command-center

A Next.js App Router project. Currently a fresh `create-next-app` scaffold — the application code at `app/page.tsx` is still the template landing page. Treat this as a greenfield codebase; conventions below describe the tooling and constraints in place, not existing product features.

## Critical version notes

The pinned versions below are **newer than common training data**. Do not write code from memory — verify against the installed version's behavior:

- **Next.js `16.2.4`** — App Router. APIs, file conventions, and defaults may differ from Next 13/14/15. Per `AGENTS.md`, read `node_modules/next/dist/docs/` before writing Next-specific code, and heed deprecation notices. (If `node_modules` isn't installed yet, run `npm install` first.)
- **React `19.2.4`** — Server Components by default in `app/`. Use `"use client"` only where required.
- **Tailwind CSS `^4`** — uses the new `@import "tailwindcss";` entrypoint and `@theme inline { … }` token block in `app/globals.css`. There is **no `tailwind.config.js`** — theme is configured in CSS. Don't reach for v3-style config files or `@tailwind base/components/utilities` directives.
- **ESLint `^9`** with flat config (`eslint.config.mjs`) composing `eslint-config-next/core-web-vitals` and `eslint-config-next/typescript`.
- **TypeScript `^5`**, `strict: true`, path alias `@/* → ./*`.

## Commands

```bash
npm run dev     # next dev — local dev server on http://localhost:3000
npm run build   # next build — production build
npm run start   # next start — serve production build
npm run lint    # eslint (flat config)
```

There is no test runner configured. There is no typecheck script — use `npx tsc --noEmit` if you need one ad hoc.

## Layout

```
app/
  layout.tsx       # Root layout. Loads Geist + Geist_Mono via next/font/google,
                   # sets html/body classes, applies the geist CSS variables.
  page.tsx         # Landing page (still the create-next-app template).
  globals.css      # Tailwind v4 entrypoint + @theme tokens + body defaults.
  favicon.ico
public/            # Static assets served at /
next.config.ts     # Empty config object — add Next options here.
eslint.config.mjs  # Flat config; overrides default ignores from eslint-config-next.
postcss.config.mjs # @tailwindcss/postcss plugin only.
tsconfig.json      # strict; @/* path alias; includes .next/types & .next/dev/types.
```

## Conventions

- **Routing & files** live under `app/`. New routes are folders with `page.tsx` / `layout.tsx` / `route.ts` per App Router conventions for this Next version — verify the exact filenames against `node_modules/next/dist/docs/` rather than assuming.
- **Server vs Client components**: default to Server Components. Add `"use client"` at the top of a file only when you need browser APIs, state, or effects.
- **Styling**: Tailwind v4 utility classes inline in JSX. Design tokens go in the `@theme inline { … }` block in `app/globals.css` (CSS variables, then mapped to Tailwind via `--color-*`, `--font-*`, etc.). Dark mode is currently driven by `prefers-color-scheme` via CSS variables, not a `dark:` class strategy config — `dark:` variants in JSX still work but the variable swap is the source of truth.
- **Fonts**: loaded via `next/font/google` in `app/layout.tsx` and exposed as the `--font-geist-sans` / `--font-geist-mono` CSS variables. Reuse those variables; don't re-import the fonts elsewhere.
- **Imports**: prefer the `@/` alias for project-internal paths (e.g. `@/app/lib/foo`).
- **Images**: use `next/image` (`Image`) for static assets in `public/`.

## Tailwind v4 gotchas

- No `tailwind.config.{js,ts}`. Configure in CSS via `@theme`.
- The entrypoint is a single `@import "tailwindcss";`, not the three v3 `@tailwind` directives.
- PostCSS uses `@tailwindcss/postcss` (see `postcss.config.mjs`), not the v3 `tailwindcss` plugin.
- Plugins, content paths, and theme extensions move to CSS — check the Tailwind v4 docs before porting v3 patterns.

## Git / workflow

- Default branch: `main`. Development branch for this work: `claude/add-claude-documentation-vaX39`.
- Single commit so far (`initial commit`) — no established commit-message style yet; use clear, descriptive messages.
- `.gitignore` already excludes `node_modules`, `.next`, `out`, `build`, `.env*`, `next-env.d.ts`. Don't commit any of those.

## When extending the project

1. If `node_modules` is missing, run `npm install` first — `AGENTS.md` directs you to consult `node_modules/next/dist/docs/` for current Next.js guidance, and the docs aren't available until install.
2. Before introducing patterns from memory (route handlers, metadata API, caching, `fetch` semantics, server actions, params/searchParams shapes), confirm them against the bundled Next 16 docs or the installed types.
3. Run `npm run lint` before considering work done. There's no test suite to run.
