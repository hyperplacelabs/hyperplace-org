# Setup: hyperplace.org build and deploy

*15 September 2026*

## Where to build

- **Build in Claude Code** (VS Code extension or CLI), opened on `~/Projects/hyperplace/hyperplaceorg`. It runs git, a local dev server for breakpoint preview, the Figma connector and Vercel deploy natively.
- **Keep the funding and application work in Cowork** (this Scout thread).
- The Figma capture is part of the build conversation, not a separate one. See below.

## Stack

Recommended: **Astro**. It is built for minimal, content-first sites, ships zero JavaScript by default, self-hosts fonts trivially, and deploys to Vercel with no config. Ideal for a super-minimal international-style landing page.

Simpler alternative: **plain static HTML and CSS** if you want nothing between you and the markup. Also fine. Avoid Next.js here, it is overkill for a one-page landing.

## Repository

1. In the project folder: scaffold the stack, then `git init` (Astro's create wizard can do this).
2. Create a GitHub repo under the **hyperplacelabs** org, to sit alongside the public protocol repo. Name e.g. `hyperplaceorg` or `hyperplace-org`. Public is consistent with the open framing but private also works; the deployed site is public either way.
3. `git remote add origin`, commit, push.

## Vercel

1. In Vercel, "Add New Project" and import the GitHub repo. Vercel auto-detects Astro. First deploy gives a `*.vercel.app` URL.
2. Every push to the main branch redeploys. Use preview branches for work in progress.
3. Project settings, Domains: add `hyperplace.org` (and optionally `www`). Vercel then shows the **exact DNS records** to create. Use those values, not remembered ones.

## Cloudflare (the one real gotcha)

DNS is on Cloudflare. Add the records Vercel shows, typically an A record on the apex to `76.76.21.21` and a CNAME `www` to `cname.vercel-dns.com`, but follow whatever Vercel displays.

**Set these records to "DNS only", the grey cloud, not proxied (orange cloud).** Vercel terminates TLS and serves the site; layering Cloudflare's proxy on top causes redirect loops and certificate errors unless carefully configured. Grey-cloud the Vercel records and let Vercel issue the certificate automatically. No other Cloudflare change is needed.

## Figma capture

In the build conversation, share:

- The Figma file link and the specific frame names or URLs for the three breakpoints (mobile, tablet, desktop).
- Dev-mode or shared access on the file so the Figma connector can read it.

The build agent reads the design (layout, spacing, type scale, colours) and implements it as clean semantic HTML and CSS. Because the design lives in Figma, Figma is the design system, so you do **not** need a Chalk-like aesthetic skill. If you want a code-quality skill, the Vercel agent-skills are a reasonable option, but they are optional. Skip frontend-chalk.

## Fonts

- Self-host from Fontshare (privacy-first, matches the no-Google-Fonts steer). Bunny Fonts is the CDN fallback.
- Which files: **woff2** for each weight you use (add woff as a fallback only if you must support very old browsers). Keep it to **two or three weights** for minimalism and performance.
- Where: place them in the repo, e.g. `public/fonts/` for Astro. The build agent will confirm the exact path for the chosen stack and write the `@font-face` and preload. Include the licence file from Fontshare.

## Content (from the brief, recap)

hyperplace.org is the protocol and public home: what Hyperplace is in a line or two, links to the protocol (contract on Base, the lab protocol page) and the **public** repo `github.com/hyperplacelabs/hyperplace-protocol`, the spec once it exists, an open "build on it" framing, MIT. No signup wall, no AI concierge. Minimal, fast, static, no third-party trackers.

## Staging (agreed order)

1. **hyperplace.org** — this workstream. Priority, because it is cited in the NLnet application and must be live before submission (target late October 2026).
2. **hyperplace.xyz** — second Figma design based on .org with additions still to be decided. Mothball Marta and the Resend newsletter signup but preserve both. Minimal holding page for now.
3. **lab pages later** — simplify `lab.hyperplace.xyz/protocol` and `/studio-north`. Fix the broken GitHub link on the protocol page: it points to `github.com/hyperplace-xyz/contracts/PlaceRegistry.sol` (private, 404s for visitors) and must point to `github.com/hyperplacelabs/hyperplace-protocol`. See the note below, this one may need doing before submission, not later.
4. **hyperplace-diagrams.md** — decide where it lives. If it documents the protocol, the natural home is the public protocol repo (`hyperplacelabs/hyperplace-protocol`) as a `docs/` file (GitHub renders Mermaid natively), optionally surfaced on hyperplace.org. Decide during stage 1 or 2.

## Timing dependency with the application

- hyperplace.org must be serving a real page before it is cited. Target: live by mid-to-late October, comfortably before the 3 November deadline.
- The broken protocol-page link is a submission-hygiene item, not just a stage-3 cleanup: if the application cites `lab.hyperplace.xyz`, a reviewer may click through the protocol page and hit a 404 on the repo link. Fix that one link before submission even if the wider lab tidy waits.
