# Living PRD

> Module 4 · Production Specs. Refactor for readability; extract a living PRD that stays true as the build evolves.

## Problem

_What user problem does this solve? Tie to the validated hypothesis._

New accounts leave before the product ever proves itself.Roughly three quarters of new accounts never create a first task, and the invite action is buried under Settings → Team Members. Accounts that invite a teammate retain at 68% versus 22% for those that do not.The hypothesis under test, stated verbatim in the product:Guiding the first action and getting users to invite their team immediately will push week-one activation above 40% and drop 90-day churn.Kill switch:

If guiding the first action and inviting the team doesn't move activation, onboarding isn't the real problem — pivot.Honesty note: the hypothesis is not validated. In the prototype's own cohort table, the guided variant (Oct W2 Test A) sits at 38.4% week-one activation — trending up, but below the 40% threshold. The decision is deliberately left open. Every number on this page is mocked sample data, not measured production data.

## Users & jobs

- **Primary user:** Primary user — the new account owner, week one.
- **Job to be done:** Get one real piece of my work tracked, and get my team in here, before I decide whether this tool is worth keeping." They are time-poor, evaluating under pressure, and will abandon silently rather than complain.

## Scope

- **In:** Evidence view — hypothesis, kill switch, company context, four baseline metrics, exit-interview quotes, cohort signal queue.
Guided onboarding — four numbered steps: orientation, first task, teammate invite, completion summary.
Team workspace — task list across To do / In progress / Done, task creation, assignment, status moves, teammate invitation, All tasks / My tasks filter.
PM dashboard — activation funnel, activation trend, cohort comparison, invite-behaviour comparison, open decision status.
Cross-screen navigation, local persistence, and a full replay/reset of the flow.
Loading, empty, and error states on every screen
- **Out (explicitly):** Billing, settings, integrations, reporting builders, team administration, CRM, marketing pages, notifications, real email delivery, accounts and authentication, any backend or API, multi-device sync, and full-blown task management (labels, due-date pickers, comments, attachments).

## Requirements

| # | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| 1 | R1: Evidence view states the hypothesis and kill switch verbatim; R2: Evidence view shows baseline metrics, quotes, and cohort queue; R3: Entry point into the guided experience; R4: Four-step guided onboarding with numbered stepper; R5: First task creation with validation; R6: Teammate invite step with the retention case; R7: Invalid or duplicate email recovery; R8: Completion summary; R9: Workspace task list; R10: Create task in workspace; R11: Assignment and team visibility; R12: Move task between statuses; R13: Invite a teammate from the workspace; R14: PM dashboard analytics; R15: Navigation between all screens | Must | AC1: Both hypothesis and kill switch sentences appear exactly as written, with the kill switch visually distinct as a decision rule; AC2: Four metric cards show value plus target, two exit-interview quotes show role and churn day, and three cohort rows show volume, experience, TTFV, activation, and status; AC3: A primary "Preview Experience" action on the evidence view opens step 1 of onboarding; AC4: Steps display as numbered 1–4, completed steps are visibly distinct from current and upcoming, and layout holds at mobile width; AC5: Task title is required, empty title blocks progress and shows "Enter a task title." beside the field, details are optional, and the entered task is persisted and appears later in the workspace; AC6: The 68% vs 22% comparison is shown, emails are added as removable chips, Continue requires at least one invite, and "Skip for now" is available and recorded; AC7: Malformed, empty, and duplicate addresses show an in-app message tied to the field, preserve what was typed, return focus for correction, and native browser validation does not take over; AC8: Completion shows the created task name, team status, simulated one-day time to first value, an "Activated" label, suggested next action, and links into the workspace and to replay the flow; AC9: Tasks from onboarding and workspace creation appear in To do / In progress / Done columns with title, details, and assignee; AC10: A dialog accepts a required title, optional details, assignee, and starting status, and the new task appears immediately in the correct column; AC11: Each task shows its owner and an All tasks / My tasks filter switches between team-wide and personal views; AC12: Opening a task offers status moves and the board and stored state update immediately; AC13: When no teammate exists an invite action is offered, R7 validation applies, and successful invites update the roster and assignee options without leaving the screen; AC14: Funnel, trend with range toggle, cohort comparison, and invite-behaviour comparison render, and the decision is labelled as still open; AC15: Evidence, onboarding, workspace, and PM dashboard are reachable from one another without dead ends. |
| 2 | R16: Loading states; R17: Empty and error states; R18: Persistence and replay; R19: Responsive and keyboard behaviour; R20: Honest framing throughout; R21: Reduced-motion support; R22: Per-screen sharing metadata | Should | AC16: Every screen shows a layout-matched shimmer while local state restores and controls are not actionable until data is ready; AC17: No evidence, no analytics, no tasks, no teammates, and unreadable stored data each show a titled explanation plus one recovery action (create, retry, or replay); AC18: Progress survives a refresh and reset returns the flow to step 1 with default state; AC19: No horizontal overflow at 390px or 1280px, all controls are keyboard reachable with visible focus, and dialogs return focus on close; AC20: No screen claims the experiment succeeded, the guided cohort is shown below the 40% threshold, and the decision reads as open; AC21: Shimmer animation is disabled when the user prefers reduced motion; AC22: Each screen has its own title, description, and social preview tags. |

## Data & events

_What gets stored, what gets tracked._

What the prototype actually stores (real)
All state is local to the browser. There is no database, no API, and no account.

Where: a single localStorage entry, key retention-engine.onboarding.v1.
What:
step — current onboarding step index.
taskTitle, taskDetails — the first task captured during onboarding.
tasks[] — the workspace task list; each task has id, title, details, assignee, status (todo | progress | done).
invites[] — email addresses entered during onboarding or from the workspace.
inviteSkipped — whether the user chose to skip inviting.
Migration: older saved state holding only a single task is converted into the task list on load; unreadable state falls back to defaults rather than crashing.
Clearing: the reset/replay action removes the entry. What is mocked
Every displayed metric is static sample data held in the source, not measured: the four baseline metrics, both exit-interview quotes, all three cohort rows, the funnel, the trend series, the cohort comparison, the invite-behaviour comparison, and the one-day time to first value shown at completion. The "Activated" label is a presentational outcome of finishing the flow, not a computed activation event. No invitation email is ever sent; invited addresses exist only in the local task assignee list.Events the real product would need (not implemented)
To actually test the hypothesis, instrumentation would need:

Event	Key properties
signup_completed	account_id, cohort, variant
onboarding_step_viewed	step_index, variant
onboarding_step_completed	step_index, duration
first_task_created	account_id, seconds_since_signup
invite_sent	account_id, invite_count, surface
invite_skipped	account_id, step
activation_reached	account_id, definition_version, seconds_since_signup
teammate_accepted_invite	account_id, invited_at, accepted_at
account_retained_day_90	account_id, activated_week_one (bool), invited_week_one (bool)
Derived measures: week-one activation rate, median time to first value, week-one invite rate, and 90-day churn split by activation and invite behaviour

## Open questions

What exactly counts as activation? Currently "finished the guided flow." A defensible definition is likely first task created and at least one teammate accepted — those produce different numbers and a different verdict.
Should skipping the invite be allowed? Skipping protects the funnel but weakens the intervention being tested; a forced invite would make the result cleaner but may cost completions.
Sent invite versus accepted invite. The 68% vs 22% retention figure needs to be pinned to one of these; the prototype does not distinguish them.
Duplicate, personal-domain, and bulk invites. Should invites be limited to the company domain, capped in number, or deduplicated across the team?
What happens after day 7? The workspace shows a "Due day 7" framing but no behaviour exists past it — reminders, nudges, or nothing?
Multi-user reality. Tasks, assignees, and rosters are single-browser here; real assignment needs accounts, permissions, and shared state.
What closes the decision? The guided cohort is at 38.4% against a 40% bar. Required sample size, run length, and the significance rule are undefined — without them the kill switch cannot be applied fairly.
Does activation actually cause retention? The experiment currently measures activation as a proxy; the churn effect at day 90 has not been observed.
