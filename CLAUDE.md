# hyperplace.org

Protocol and public home for Hyperplace — the layer the NLnet Restack
application cites. Minimal, fast, static, privacy-first: no analytics, no
third-party trackers, no signup wall, no AI concierge.

Read `hyperplaceorg-landing-brief.md` and `hyperplaceorg-setup.md` for
intent and constraints, and `STATUS.md` for current build/infra state
before starting work — it tracks what's blocked and what's already wired
up so it doesn't need re-deriving.

## Stack

Plain static HTML/CSS served from `public/`. Deliberately not Astro or a
framework — see `STATUS.md` "Stack decision" for why. Don't introduce a
build step or JS framework without a real reason (e.g. a second page that
benefits from templating).

## Design

The design source of truth is Figma (file `hyperplace`, page `dotorg`,
frames "Mobile 390" / "Tablet 768" / "Desktop 1440"), read via the Figma
MCP connector — **not** the `frontend-chalk` skill. Both project docs
override that skill's "apply to all UI" default for this repo: Figma is
already a complete design system here, so don't invent one.

If Figma access is unavailable, say so rather than shipping a guessed
design as if it were final — check `STATUS.md` first for the current
state of that blocker.

## Fonts

Self-hosted from Fontshare (or Bunny Fonts as fallback), woff2, two or
three weights. **Never Google Fonts.** Place files in `public/fonts/`,
include the Fontshare licence file, write `@font-face` + preload by hand.

## Content constraints

- No signup wall, no marketing copy, no AI concierge anywhere on this
  domain.
- Lead with the primitive and the commons — not commercial framing, not AI.

**Stale as of 2026-09-16**: this section originally also required a link
list (protocol contract, `lab.hyperplace.xyz/protocol`, the public repo,
the spec) and MIT/"build on it" framing. The founder explicitly asked to
remove all of that and simplify to just the one-line description — see
STATUS.md "Content, round 2". Left here as the brief's original intent in
case that direction reverses (e.g. before the NLnet submission, which
does cite this domain), but don't reintroduce those links without
checking with the founder first.

Similarly, current copy/type deliberately diverge from the Figma "dotorg"
frames on two points — see STATUS.md "Content, round 2" and "round 3" for
what and why before treating the frames as current spec.

## Related repos (not this one)

- `~/Projects/hyperplace/hyperplacexyz` — the Next.js app behind
  hyperplace.xyz. Marta is mothballed (pushed live 2026-09-16); the
  homepage now deliberately mirrors this repo's page exactly (content,
  type, layout — see STATUS.md). Its signup backend (Vercel functions +
  Supabase + Resend) is preserved and documented in this repo's
  `STATUS.md`, not deleted. Live production site — treat pushes there
  with the same care as this repo, confirming non-trivial changes first.
- `hyperplacelabs/hyperplace-protocol` — the public contract repo linked
  from this site.

## Deploy

Vercel project `hyperplace-org` (scope `hyperplacelab`), domains
`hyperplace.org` + `www.hyperplace.org` already attached. DNS lives on
Cloudflare, set to DNS-only (grey cloud) — never proxy (orange cloud) these
records, it breaks Vercel's TLS. See `STATUS.md` for verified state.
