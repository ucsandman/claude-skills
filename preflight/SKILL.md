---
name: preflight
description: "Run a product's final prelaunch readiness check; /preflight."
user-invocable: true
argument-hint: "[optional focus: env | design | verify]"
---

# Preflight — the last pass before a repo goes public

One run per repo. The pass is **read-only on infra**: it reports platform problems; fixing them (set env var, Stripe config, DNS, deploy) is a CLAUDE.md Hard Stop. Collect every needed infra fix into ONE batched go-ahead list at the end — never apply them mid-pass, never ask one at a time.

Two rules that override everything below:

- **Env vars are checked by NAME only.** Never pull, print, or diff env VALUES — not in chat, not in a file. Never run `vercel env pull` (it has blanked `.env.local` on this machine twice).
- **Invoke the skills; don't reimplement them.** impeccable, polish, and frontend-verify each carry their own protocol.

## 0. Aim at the right project

- `git status` first. Note uncommitted work.
- offlocal is project-scoped and keeps a "selected" project between sessions. Always: `list_projects` → `select_project` for THIS repo → `get_project_context`. Skipping this audits whatever repo was selected last.
- No matching offlocal project? Say so in the report and run local-only checks.
- If a launch plan (`launch_*`) exists for this project, also run `preflight_launch` and `verify_launch` and fold their results into the report.

## 1. Wiring audit (read-only)

- Required var names come from `.env.example`. If it is missing or stale, grep `process.env.` / `import.meta.env.` and report the drift — that is itself a finding.
- Check presence of each name on the deploy platform via offlocal (`get_project_context`, provider env getters). Names only.
- Stripe, when the product charges: mode must be **live** for launch (`get_project_context` shows it); `list_stripe_products` + prices match the pricing page; `list_stripe_webhooks` has an enabled endpoint on the production domain. Grep the repo for hardcoded `price_`/`prod_` IDs that point at test mode. The Stripe account is shared across products, so a purchase here fires every other product's `checkout.session.completed` endpoint on the account: list the endpoints and confirm each one's handler guards by its own price ids (2026-09-03: the first live Off Localhost sale minted a declick license and a CallClaw client row).

## 2. Design pass (bounded)

- Invoke `impeccable:impeccable` audit on the main surfaces. Fix criticals and quick cosmetic wins in this pass; structural redesigns get FLAGGED for Wes, not built — launch eve is not redesign time.
- Then invoke `polish` for the final micro-detail pass.

## 3. Verify

- Invoke `frontend-verify` over `/`, the pricing page, and every route the design pass touched.
- Build gates from `package.json`: install, lint, typecheck, test, build — read the output.
- Live check: fetch the production URL. HTTPS, real title, OG meta, favicon.
- SEO floor: `/sitemap.xml`, `/robots.txt` (with a Sitemap line, disallowing dashboard/api/thanks), `/llms.txt` all return 200 with real content; every indexable page has its own title + meta description; noindex pages carry `robots: { index: false }`. Missing any of these is a FAIL on the `verify` line, and a code fix, not an infra one.
- Search registration: Google Search Console property verified with the sitemap submitted and the home URL inspected, Bing Webmaster Tools imported from Search Console, the host's analytics enabled with its tag live on every page. Do it in this pass through the browser tools, following `references/search-registration.md`; only the Bing sign-in is handed to Wes. Reported on its own `search` line below.

## 4. Report (exact shape, then stop)

```
PREFLIGHT: <repo> — <date>
[PASS|FAIL] env      missing on platform: NONE | NAME_A, NAME_B
[PASS|FAIL] stripe   mode=<live|test|n/a>, webhook=<prod domain|MISSING>, products=<n>
[PASS|FAIL] design   impeccable: <n> fixed, <n> flagged; polish done
[PASS|FAIL] verify   frontend-verify: <n>/<n> routes pass
[PASS|FAIL] build    lint/typecheck/test/build output read
[PASS|FAIL] search   gsc=<verified|MISSING> sitemap=<submitted|MISSING> bing=<imported|PENDING WES> analytics=<vercel|posthog|MISSING>
VERDICT: LAUNCH READY | BLOCKED: <one line per blocker>
NEEDS GO-AHEAD (batched): <infra fixes with exact action + rollback | none>
```

Code fixes from the pass are committed via `/ship` after the report. Announcing is a separate skill: offer `/launch` only once the verdict is LAUNCH READY.

## Red flags — stop and correct

- An env VALUE anywhere in your output → redact, switch to names.
- About to set an env var, touch Stripe/DNS, or deploy mid-pass → Hard Stop; it goes in the batched list.
- impeccable proposing a rebuild → flag it, don't build it.
- Writing VERDICT without having read the frontend-verify table and build output → not a verdict, run them.
