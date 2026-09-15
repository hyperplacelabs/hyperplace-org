# Status: hyperplace.org / hyperplace.xyz build

Last updated: 2026-09-15. See `hyperplaceorg-landing-brief.md` and
`hyperplaceorg-setup.md` for the source brief and setup notes this tracks.

## Blocked

**Figma MCP is not connected in this session.** `claude mcp list` shows only
an unauthenticated `dune` connector — no Figma server registered, despite it
being installed in VS Code. This blocks reading the actual design (file
`hyperplace`, page `dotorg`, frames "Mobile 390" / "Tablet 768" / "Desktop
1440"): typeface family, type scale, spacing, colour, exact copy and layout.

Until this is fixed, `public/index.html` / `public/css/style.css` are a
**placeholder**: real content from the brief, but system-font styling, not
the Figma design. Do not treat the current visual design as final. Once
Figma access works, re-read the three frames and rebuild the CSS/markup
against them, then self-host the correct Fontshare (or Bunny) family in
`public/fonts/` — do not use Google Fonts.

Fix path: restart/reconnect the Figma MCP server in VS Code, or run
`claude mcp list` there to confirm it's registered, then re-open this
conversation.

## hyperplace.org — infrastructure (verified 2026-09-15)

- **Repo**: `hyperplacelabs/hyperplace-org` (public, GitHub). Local repo
  initialised at `~/Projects/hyperplace/hyperplaceorg`, not yet pushed.
- **Vercel project**: `hyperplace-org` under scope `hyperplacelab`
  (project id `prj_Y8N9lkI8DGWODMENgZOVDOujhvA7`). Framework preset
  "Other", output directory `public` — matches the plain static HTML/CSS
  approach used here (chosen over Astro so no preset change is needed;
  see "Stack" below).
- **Domains**: `hyperplace.org` and `www.hyperplace.org` are both already
  attached to the `hyperplace-org` Vercel project. Apex redirects (308) to
  `www`; `www` currently 404s because there is no production deployment yet
  (deploy the moment the repo is pushed and connected).
- **DNS / Cloudflare**: nameservers are Cloudflare (`ian.ns.cloudflare.com`,
  `thea.ns.cloudflare.com`). Apex A records and the `www` CNAME resolve to
  Vercel's own addresses, and `curl -sI https://hyperplace.org` returns
  `server: Vercel` — confirming the records are **DNS-only (grey cloud)**,
  not proxied through Cloudflare. This is exactly the required setup (see
  `hyperplaceorg-setup.md`); no Cloudflare change needed.
- **Git↔Vercel connection**: not yet verified/created. Next step: `vercel
  link` this directory to the `hyperplace-org` project, then `vercel git
  connect` to wire it to `hyperplacelabs/hyperplace-org` so pushes to
  `main` auto-deploy. (A direct GitHub-hooks check hit a transient network
  error mid-session; re-verify after connecting.)

## Stack decision

Plain static HTML/CSS, not Astro. Reasoning: the Vercel project is already
configured with output directory `public`, which plain static files match
with zero config changes. Astro outputs to `dist/` and would need the
Vercel framework preset changed first. For a single static page with no
interactivity, plain HTML/CSS is also simply less machinery. Revisit if a
second page (e.g. a rendered spec) later makes a static-site generator
worth the setup cost.

## hyperplace.xyz — Marta pause

Working directory: `~/Projects/hyperplace/hyperplacexyz` (separate repo,
`hyperplacelabs/hyperplace-xyz`, live at `https://www.hyperplace.xyz`).

**Not yet pushed** — changes are local only pending explicit confirmation,
because this repo is live and pushing to `main` redeploys the public site.

What changed locally:
- `app/page.tsx` now renders a minimal holding page instead of
  `<ConciergeShell />`.
- Marta's UI components (`components/ConciergeShell.tsx`,
  `components/MartaMessage.tsx`, `components/InputBar.tsx`,
  `components/SignupPanel.tsx`) and the chat API routes
  (`app/chat/route.ts`, `app/api/chat/route.ts`) are **left in the tree,
  untouched** — parked per the brief ("park, preserve, do not delete"),
  just no longer referenced from the homepage.
- `/research` and `/privacy` pages are **left live and unchanged**. The
  signup confirmation email (`lib/resend.ts`) links to `/research`, and the
  privacy notice should stay reachable while personal data is held.
- `app/api/keepalive/route.ts` and the cron in `vercel.json`
  (`0 9 */2` → `/api/keepalive`) are untouched — this is what stops the
  Supabase project idling out on the free tier. Do not remove it while any
  signup data still lives there.

### Signup backend (preserved, documented for later revival)

Schema below is derived from the route code (`app/api/signup/route.ts`,
`app/api/confirm/route.ts`), not pulled from Supabase directly — treat as a
close approximation, verify against the actual table before building on it.

**Supabase table: `signups`**

| column | notes |
|---|---|
| `id` | primary key; also doubles as the email-confirmation token (see below) |
| `first_name` | nullable |
| `email` | unique — insert conflict (`23505`) is handled as "already signed up" |
| `early_tester` | boolean, defaults false |
| `early_tester_confirmed` | boolean, set true by the confirm endpoint |
| `context` | nullable free text |
| `source` | defaults to `'direct'` |

Hosted in Ireland (EU) per the current `/privacy` page.

**Vercel functions (all in `hyperplacexyz/app/api/`)**

- `POST /api/signup` — inserts a row; sends one of two Resend emails
  depending on `early_tester` (a "you're on the list" note, or a
  confirm-your-spot email with a link to `/api/confirm`).
- `GET /api/confirm?token=<id>` — marks `early_tester_confirmed = true` for
  that row, redirects to `/?confirmed=true`. **Note**: the "token" is
  literally the row's `id`, not a signed/random token — anyone with an id
  could confirm another row. Documenting as-is, not fixing; out of scope
  for this pause.
- `GET /api/opening` — unrelated to signups; generates Marta's opening
  line via the Anthropic API. Dead code once the concierge UI is parked,
  left in place.
- `GET /api/keepalive` — pings the `signups` table to keep the Supabase
  project from idling. **Load-bearing, do not remove.**

**Resend**: sends from `Hyperplace Labs <hello@updates.hyperplace.xyz>`.
Two email bodies live in `app/api/signup/route.ts` (`flowAEmail`,
`flowBEmail`).

**Env vars** (names only — see `hyperplacexyz/.env.local`, never commit
values): `ANTHROPIC_API_KEY`, `NEXT_PUBLIC_SUPABASE_URL`,
`NEXT_PUBLIC_SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_ROLE_KEY`,
`RESEND_API_KEY`, `NEXT_PUBLIC_SITE_URL`.

### Mailing-list decision: still deferred

Per the brief, not decided here. Option A (static form feeding this
backend) vs Option B (skip it, use Bluesky/GitHub for updates) — founder's
own note is that Option B is likely the sensible default until there's an
audience. No action taken either way; the backend above is just preserved.

## hyperplace.xyz — page.tsx replacement content

Placeholder holding page: name, one-line descriptor, links to
`hyperplace.org` and `lab.hyperplace.xyz`. Same caveat as the .org page —
styled with system fonts for now, not the Figma "dotxyz" design (deferred
per the brief: ".xyz build deferred", tackled after .org).

## Known separate issue (flagged in `hyperplaceorg-setup.md`, not yet fixed)

`hyperplacexyz/hyperplace-lab/protocol/index.html:275` links to
`github.com/hyperplace-xyz/contracts/PlaceRegistry.sol`, which is private
and 404s for visitors. Should point to
`github.com/hyperplacelabs/hyperplace-protocol`. This is a different Vercel
deploy target (`hyperplace-lab`, its own `.vercel` folder) from both repos
above — setup.md flags this as submission-hygiene for the NLnet
application, worth fixing before 3 November regardless of when the wider
lab tidy happens.

## Open items / next steps

1. Fix Figma MCP access (blocks real .org design).
2. `vercel link` + `vercel git connect` the hyperplace-org project to
   `hyperplacelabs/hyperplace-org`, then push this repo's initial commit.
3. Confirm before pushing the Marta-pause commit to `hyperplacexyz` (public
   site — needs explicit go-ahead, not just local changes).
4. Once .org is live: revisit hyperplace.xyz's own "dotxyz" Figma page for
   its real design (currently a system-font placeholder).
5. Fix the broken protocol-page GitHub link in `hyperplace-lab` before the
   NLnet submission.
6. Decide the mailing-list question (Option A vs B) — parked, not blocking.
