# Engineering Handoff Note

> Module 4 · Production Specs. Open the black box, make the build legible to an engineer.

## What this is

_One paragraph an engineer can read in 60 seconds._

This is a clickable, front-end-only prototype testing one hypothesis: guiding a new team's first task and first teammate invite in week one lifts week-one activation above 40% and cuts 90-day churn. It has four screens — Evidence View (the experiment dashboard), Onboarding Preview (a four-step guided flow), Team Workspace (a task board the user lands in after onboarding), and PM Dashboard (operational experiment analytics). There is no backend, no auth, and no analytics: all interactive state (task, invites, workspace board) lives in browser localStorage, and every metric, cohort, chart, and quote on the dashboards is hardcoded sample data written to read as an in-flight experiment, not a proven result. An engineer taking this forward keeps the interaction model and the screen structure, and replaces the sample-data files and the localStorage store with a real event pipeline, a real backend, and auth.

## Architecture (plain language)

- **Frontend:** TanStack Start v1 (React 19, TypeScript, Vite 7), file-based routing under src/routes/. Each route file is a thin wrapper that imports a screen component from a feature folder:  src/routes/index.tsx          -> src/features/evidence-view/EvidenceView.tsx        (/) src/routes/onboarding.tsx     -> src/features/onboarding-preview/OnboardingPreview.tsx (/onboarding) src/routes/workspace.tsx      -> src/features/team-workspace/TeamWorkspace.tsx      (/workspace) src/routes/pm-dashboard.tsx   -> src/features/pm-dashboard/PmDashboard.tsx          (/pm-dashboard) Each feature folder pairs its screen component with a *.data.ts file holding that screen's copy and sample figures, so display and data are separated. Shared pieces live in src/features/shared/: AppShell.tsx (navigation chrome), AsyncStates.tsx (loading skeletons, empty/error states), and onboarding-store.ts (the persisted state). UI primitives are shadcn/ui (Radix) under src/components/ui/; styling is Tailwind CSS v4 with a custom oklch token theme in src/styles.css; charts use Recharts via ui/chart.tsx.
- **Backend / data:** There is deliberately none. The only persistence is onboarding-store.ts, a small typed store that reads/writes localStorage under the key retention-engine.onboarding.v1, validates email and task-title input with zod, and migrates older saved shapes forward. Everything analytical — churn percentages, cohort rows, funnel bars, trend chart, exit-interview quotes — is literal data in the feature *.data.ts files. Nothing is fetched; nothing is tracked.
- **Key flows:** Evidence View → "Preview Experience" → Onboarding steps 1–4 (welcome → create first task → invite teammates → completion). Task title is required; invites need at least one valid email or an explicit "Skip for now".
Onboarding completion → Team Workspace: the onboarding task appears as a card on a three-column board; users can create tasks, assign them to invited teammates (or "me"), move them between columns, and invite teammates late if they skipped.
Team Workspace → PM Dashboard (link in the header) and back. PM Dashboard also links to the Evidence View.
Reset/replay: clearing the store returns the flow to step one; refreshing any screen restores the saved state.

## What's solid vs. what's duct tape

| Area | State | Notes |
|---|---|---|
| Screen structure and navigation logic across the four routes. The onboarding interaction model: numbered stepper, required-title validation, email validation with inline recovery and focus return, skip path, completion summary. The workspace model: task create/assign/move, All/My filtering, late-invite dialog, empty states with recovery actions. State layer shape: a single typed store with versioning and migration — a reasonable template for real persistence. Presentation quality: loading skeletons, empty/error states, reduced-motion support, responsive at desktop and mobile widths, keyboard focus handling, per-screen head metadata. | solid | _____ |
| All metrics, cohorts, quotes, and charts are hardcoded sample data; they will not change no matter what a user does. "Simulated 1-day TTFV" and the "Activated" badge are scripted outcomes, not computed results. localStorage is per-browser and per-device: state does not sync, multi-user is faked (invited "teammates" are strings, never real accounts), and there is no identity at all. No event instrumentation exists — none of the events a real experiment needs (first_task_created, invite_sent, activation_reached, etc.) are emitted anywhere. No automated tests; verification has been manual/Playwright-driven per change. | rough | _____ |

## Risks & assumptions for the team

Hypothesis is unvalidated. Test A reads 38.4% vs. a 40% threshold; the decision is deliberately open and the kill switch applies. Do not present the dashboards as results.
Sample data must not leak into production. The *.data.ts files look realistic; anything shipped from them without a real source is a lie with good typography.
Activation is undefined. The prototype treats "task created + at least one invite (or explicit skip)" as activation, but no instrumentation defines or measures it. This definition must be agreed before building the real thing.
Invites are not real. No emails are sent, no acceptance is tracked; the 68%-vs-22% retention claim on screen is cited evidence, not something this app measures.
localStorage limits. Clearing browser data loses all progress; there is no cross-device continuity, which a real week-one activation flow needs.
Accessibility is improved, not audited. Focus states and reduced motion are handled, but no formal a11y review has been done.
Single happy-path team size. The workspace assumes a tiny team; nothing handles scale, permissions, or roles.

## How to run it

```
Prerequisites: Node.js 18+ and bun (or npm).

bun install        # or: npm install
bun run dev        # starts Vite dev server (default: http://localhost:8080)
Other scripts:

bun run build      # production build
bun run preview    # serve the production build locally
bun run lint       # eslint
There is no environment configuration, no .env, and no external service to set up. To reset the demo to a clean slate, clear localStorage (or use the replay/reset control in the app) — the key is retention-engine.onboarding.v1.
```
