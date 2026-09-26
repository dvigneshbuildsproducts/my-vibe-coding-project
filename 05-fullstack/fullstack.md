# Full-Stack: Data, Access Rules, Edge Cases, Deploy

> Module 5 · Full-Stack. Add data schemas, access rules, and edge cases; stress-test and deploy.

## Deployed link

_The working, shareable link that survives real users._

https://stick-around-star.lovable.app

## Data schema

| Entity | Key fields | Notes |
|---|---|---|
| user roles , experiments, baselinemetrics | _____ | separate table with an admin / user role type and a role-check function (never on a profile).. separate table with an admin / user role type and a role-check function (never on a profile). |
| onboarding events | _____ | user, event name (step viewed/completed, first task created, invite sent, invite skipped, activation reached), step, timestamp. Feeds funnel and real TTFV. |

## Access rules

_Who can see / do what? Where are the auth boundaries?_

Existing three tables: unchanged — owner only, all four actions.



onboarding events: owner may insert and read own; no update/delete; admins read all.



experiments, baseline metrics, cohorts, quotes, company context: public read (so Evidence View and PM Dashboard stay shareable); insert/update/delete only for admins via the role-check function.



user roles: signed-in users read their own row; only admins/back-office change roles.



Aggregates for the dashboards come from a security-definer view/function returning counts only — never individual users' rows.

Every new table: grants in the same migration, RLS enabled, no anon write.

## Edge cases hardened

| Case | Before | After |
|---|---|---|
| Empty / first-run state | Save fails or goes offline mid-flow — visible message, screen reverts, retry. | This was fixed with a banner indicating the internet is not available |
| Bad / malicious input | _____ | _____ |
| Failure / offline | _____ | _____ |

## Stress test results

_What you threw at it, and what held / broke._

The No internet was not woking and I had to prompt to add a banner when internet is not available.
