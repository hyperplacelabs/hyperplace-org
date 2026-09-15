# Status: hyperplace.org / hyperplace.xyz build

Last updated: 2026-09-16. See `hyperplaceorg-landing-brief.md` and
`hyperplaceorg-setup.md` for the source brief and setup notes this tracks.

## Figma (resolved 2026-09-16)

Figma MCP is now connected. Read file `hyperplace`, page `dotorg`, all
three frames: Mobile 390 (node `451:3`), Tablet 768 (node `2001:7`),
Desktop 1440 (node `2001:9`), file key `fmojNgXWt2rhZsaGQ28fBK`.

**That Figma file is a low-fidelity wireframe**, not a finished design:
placeholder lorem ipsum copy, no design tokens/variables defined
(`get_variable_defs` returned empty), and no protocol/repo link list, MIT
line, or Bluesky link — just a wordmark, one tagline/home link, one body
paragraph, a hairline rule, and one footer line, repeated near-identically
across breakpoints with a lot of deliberate vertical whitespace.

What was taken directly from Figma and implemented as-is:
- Typeface **Archivo**, weight 300 (mobile) / 200 (tablet+desktop),
  self-hosted (downloaded from Bunny Fonts' CDN once, not loaded from it
  at runtime) as `public/fonts/archivo-{200,300}.woff2` + `OFL.txt`.
- Sizes: 21px on mobile, 48px on tablet and desktop, for all text.
- Colour: black text on white, with a single accent colour — pure
  `#00FF00` — used consistently for every link-styled element across all
  three frames. Kept as-is despite being an unusual choice, since it's
  deliberate and consistent, not a one-off.
- A left-aligned content column (~798px), not centred, with large
  right-hand whitespace on desktop — kept rather than centring the page.
- The single 1px black hairline rule (reproduced as a CSS border, not an
  image — the exported asset was a trivial straight line).

An initial pass also added a protocol/repo link list and an MIT +
"build on it" + Bluesky line to the footer, since CLAUDE.md's content
constraints call for them. **The founder then explicitly asked to remove
all of that** (2026-09-16) and simplify the copy — see "Content, round 2"
below. So as of now the page deliberately does **not** carry the protocol
contract, lab notes, source repo, spec, or Bluesky links CLAUDE.md
describes. That's a live decision, not an oversight: flagging it here so
it isn't silently reintroduced, and flagging to the founder that CLAUDE.md
itself is now stale on this point and could use an update if this
direction sticks.

## Content, round 2 (2026-09-16, founder-directed)

- Lede copy changed to: "An open, self-sovereign spatial identity
  primitive. Discover ways of experiencing place as a pattern."
- Removed entirely: the protocol contract / protocol notes / source / spec
  link list, and the Bluesky link.
- Footer simplified from "hyperplace.org — open, build on it" to just
  "hyperplace.org".
- Fixed a real layout issue: tablet/desktop used the same
  `justify-content: flex-end` trick as mobile to pin content near the
  bottom of the viewport. On a short phone screen that reads as one
  contained gap under the title; on a tall/wide tablet or desktop window
  it produced a disproportionate void and stranded the paragraph far from
  the title, especially once the page got shorter. First fix: gave `main`
  normal block flow at 768px+ with a bounded `margin-top` on `.lede`
  instead of flex-end.

## Content, round 3 (2026-09-16, founder-directed)

Founder asked to drop the Figma-specified 48px/weight-200 type for
tablet+desktop entirely and reuse mobile's 21px/weight-300 sizing and
spacing at every breakpoint — only the container's side padding (`--gutter`)
and max column width still scale up with viewport width, for line length.

This actually **resolved the round-2 spacing fix's original motivation**:
the disproportionate-void problem was largely a function of 48px type not
filling a tablet/desktop viewport proportionally the way it does on a
phone. With matching 21px type at every breakpoint, content-to-viewport
proportions land close to mobile's own (~60-70% void either way), so the
`justify-content: flex-end` bottom-anchor was reinstated for all
breakpoints — the round-2 `display:block` + bounded `margin-top` override
was removed as no longer needed. Screenshot-verified at
390×844 / 768×1024 / 1440×900; all three now look like the same design at
different widths, per the founder's ask.

Net effect: **the tablet/desktop frames in Figma no longer match the
built site** on type size/weight. Deliberate, founder-directed — flagging
so it isn't "fixed" back to the Figma spec without checking first.

One implementation note for whoever touches this next: Figma's exported
reference code applied `line-height: 28px` literally to every single-line
text node, including the 48px ones. Applied verbatim in flow CSS that
causes stacked lines to overlap — it's a Figma-internal box value, not a
deliberate 28px line box at 48pt type. Line-heights in `style.css` were
derived from the actual Y-coordinate gaps between elements instead; don't
reintroduce the raw 28px value at the larger sizes.

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

## hyperplace.xyz — Marta pause (pushed live 2026-09-16, see below)

Working directory: `~/Projects/hyperplace/hyperplacexyz` (separate repo,
`hyperplacelabs/hyperplace-xyz`, live at `https://www.hyperplace.xyz`).

What changed:
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

## hyperplace.xyz — Marta pushed live (2026-09-16)

The Marta-pause commit was pushed and is live at `www.hyperplace.xyz`.
`hyperplacexyz`'s `origin` remote was stale (pointed at
`hyperplacexyz.git`; actual repo is `hyperplacelabs/hyperplace-xyz`,
hyphenated) — fixed before pushing.

Founder then asked (2026-09-16) for the .xyz homepage to have the exact
same content and layout as .org, not a separate design — so the earlier
"placeholder holding page" plan (own copy, links to .org/lab) is
superseded. Implemented as:

- `app/page.tsx` + new `app/page.module.css` (Next.js app router, CSS
  Modules) reproduce the .org static page's markup, Archivo type, and
  spacing exactly — only the self-referential text differs
  ("hyperplace.xyz" instead of "hyperplace.org", both in the header
  home-link and the footer). Fonts copied into
  `hyperplacexyz/public/fonts/` (same self-hosted Archivo 200/300 files
  as the .org repo).
- Scoped to this one route via a CSS Module so `/research` and
  `/privacy` (both still live per the earlier decision) keep the
  existing IBM Plex Sans / cream house style in `globals.css`, untouched.
- Fixed a real bug found in the process: `globals.css`'s `html, body`
  rule had no `margin: 0`. Invisible on the cream pages (default browser
  body margin blends into the cream background) but showed as a visible
  border around this page's full-bleed white background. Fixed globally;
  verified no regression on `/research` and `/privacy` via screenshots.
- Verified with `npm run build` + Playwright screenshots at
  390×844 / 768×1024 / 1440×900, then pushed and confirmed live.

This means .xyz no longer needs its own "dotxyz" Figma pass for now —
park that unless/until the founder wants .xyz to diverge from .org again.

## Lab protocol link — fixed in git, not yet deployed (2026-09-16)

`hyperplacexyz/hyperplace-lab/protocol/index.html:275` linked to
`github.com/hyperplace-xyz/contracts/PlaceRegistry.sol` (private, 404s for
visitors). Now points to
`github.com/hyperplacelabs/hyperplace-protocol/blob/main/contracts/PlaceRegistry.sol`
(verified 200). Committed and pushed to `hyperplacexyz` for history.

**Deploy note**: `hyperplace-lab/` is plain static HTML with its own
Vercel project (`hyperplace-lab`, separate `.vercel/project.json`), not
built from git pushes — its git-triggered auto-deploys have been failing
for 90+ days (tries to build the whole Next.js app from the repo root and
errors on a missing Supabase env var; pre-existing, unrelated to this
change). The real deploy path is `cd hyperplace-lab && vercel --prod`,
which the auto-mode classifier blocked as a direct-production-push action
needing explicit sign-off. **Not yet deployed live** — needs someone to
run that command (or grant permission) to actually update
`lab.hyperplace.xyz/protocol`.

## Open items / next steps

1. **Deploy the lab link fix** — run `cd hyperplacexyz/hyperplace-lab &&
   vercel --prod --scope hyperplacelab` (blocked by the auto-mode
   classifier as a direct production push; needs the founder to run it or
   grant permission). Fix is committed to git already, just not live.
2. Mailing-list question (Option A vs B) — explicitly parked by the
   founder (2026-09-16), pick up later.
3. hyperplace.xyz's own distinct design ("dotxyz" Figma page) is on hold
   indefinitely now that .xyz deliberately mirrors .org — only revisit if
   the founder wants them to diverge again.
4. When the .org Figma file gets a fuller pass (real copy instead of
   lorem ipsum), re-read and reconcile — note the founder has already
   diverged from it twice (dropped the link list/MIT/Bluesky content, and
   unified type size across breakpoints rather than the 48px tablet/desktop
   spec) — see "Figma", "Content round 2" and "Content round 3" above.

## Done

- hyperplace.org scaffolded, pushed, deployed, Vercel↔GitHub connected.
- Domains + DNS verified correct (grey-cloud Cloudflare, Vercel-issued TLS).
- Real Figma design implemented, then simplified twice per founder
  feedback (copy/links trimmed, type unified across breakpoints).
  Screenshot-verified at 390/768/1440px with Playwright throughout.
- Marta mothballed and pushed live on `hyperplace.xyz`.
- hyperplace.xyz homepage now mirrors hyperplace.org exactly (content,
  type, layout), pushed live. `/research` and `/privacy` untouched.
- Signup backend (Vercel functions, Supabase, Resend, keepalive cron)
  preserved and documented for later revival.
- Broken `lab.hyperplace.xyz/protocol` GitHub link fixed in git (deploy
  still pending, see Open items above).
