# Brief: hyperplace.org landing, .xyz minimal, Marta pause

*Written 11 September 2026 by the funding workstream (Scout), as a handoff for a separate build conversation and project. This is a brief, not a spec: it fixes intent and constraints, the build conversation decides the how.*

## Why this exists

Hyperplace is moving to a layered public presence that mirrors the architecture:

- **hyperplace.org** becomes the protocol and public home. Generic, neutral, the place others build on. This is what the NLnet Restack application will cite.
- **hyperplace.xyz** becomes the service and eventual commercial layer. For now it gets a minimal holding page; its richer build is deferred.
- **Marta**, the AI concierge currently on hyperplace.xyz, is paused. Its original purpose (the YC run) is over, and an AI concierge on the front page is a needless perception risk while applying to an AI-wary open-source fund.

There is a timing dependency: hyperplace.org must be minimally live before it is cited in the NLnet application, target submission late October 2026. So "minimal but live" beats "polished but late".

## Infrastructure (founder actions, noted for context)

- Domain registered at Spaceship: `hyperplace.org`.
- DNS on Cloudflare.
- Repo on GitHub, deploy via Vercel.
- Bluesky exists as a secondary channel: `@hyperplace.bsky.social`.

The build conversation produces the site code; the founder wires up the DNS, GitHub and Vercel.

## hyperplace.org: the landing page

Minimal, fast, static, privacy-first (no analytics or third-party trackers). It is a protocol home, not a product pitch.

Content, roughly:

- What Hyperplace is, in a line or two: an open, self-sovereign spatial identity primitive. Place is a pattern, not a coordinate; the missing layer is sovereignty, not better mapping.
- A short "what it is / why it matters" that a technical reader gets quickly. Lead with the primitive and the commons, not with any commercial framing and not with AI.
- Links out to the substance: the protocol (the deployed contract on Base, and the lab protocol page at lab.hyperplace.xyz/protocol), the public repository (github.com/hyperplacelabs/hyperplace-protocol), and the specification once it exists.
- An open, "build on it" invitation, and the MIT / open-licence framing.
- Restraint. No signup wall, no marketing, no AI concierge.

Design constraints:

- Sans-serif typography from Fontshare, self-hosted, or Bunny Fonts. No Google Fonts.
- We will use a different design skill. We have  `frontend-chalk` design-system skill in the build conversation; it governs UI style. But this is for wireframe mockup studies. This is a different thing here. 
- I am creating a landing page in Figma and will get the details for you. 
- Accessible, responsive, light. Fast on a phone.

## hyperplace.xyz: minimal replacement page

Replace the Marta landing with a minimal holding page: the name, a one-line descriptor, and a link across to hyperplace.org and the lab. Take the Marta concierge UI down. Keep it consistent in style with .org.

## Marta and the signup backend: park, preserve, do not delete

Marta did two jobs. The concierge conversation, which is paused. And a signup path: after interest, it offered early-bird notification signup and an option to register as a tester, backed by Vercel functions, Supabase and Resend. That backend is still useful and must be preserved, not deleted, along with any captured data.

Decision deferred, to tackle later:

- Option A: a simple static form (name/email) feeding the existing backend or a lightweight replacement, when there is a reason to collect signups.
- Option B: skip the mailing list for now and share updates via Bluesky and GitHub instead. Honest note from the founder: it is hard to see who would use an early-bird list at this stage, so Option B may be the sensible default until there is an audience.

For now: preserve the backend and data, take the collection UI down with Marta, and document what exists (endpoints, Supabase schema, Resend setup) so it can be revived cleanly. Capture that documentation in the new project's own STATUS/README so this workstream can act on it without re-deriving it.

## Out of scope for now

- The mailing list / tester-onboarding flow (parked, see above).
- Any commercial or service build on .xyz beyond the holding page.

## Suggested opening prompt for the separate conversation

```
Set up the hyperplace.org landing page and a minimal replacement page for hyperplace.xyz, and pause Marta. Work in this project.

Read hyperplaceorg-landing-brief.md for intent and constraints. We are looking for a suitable minimal, swiss style UI design skill. Minimal, fast, static, privacy-first, no third-party trackers. Sans-serif from Fontshare (self-hosted) or Bunny Fonts,
never Google Fonts.

hyperplace.org is the protocol and public home: what Hyperplace is in a line or two, links to the protocol, the public GitHub repo and the spec, an open "build on it" framing, MIT. No signup wall, no AI concierge.

hyperplace.xyz gets a minimal holding page. We might link it across to .org and the lab. Take the Marta concierge UI down but preserve the signup backend (Vercel, Supabase, Resend) and its data; document what exists so it can be revived, and park the mailing-list decision. 

Priority is minimal-but-live: hyperplace.org needs to be live before it is cited in the NLnet application (target late October 2026).

https://www.figma.com/design/fmojNgXWt2rhZsaGQ28fBK/hyperplace?node-id=450-2&t=oqP7hHJV1rhk3JaG-1

The figma file is called 'hyperplace' and the page is called 'dotorg'. There are three frames, for each of breakpoints: 'Mobile 390', 'Desktop 1440' and 'Tablet 768'

Note there is also a further page called 'dotxyz' which we will come back to later when we tackle the page for `hyperplace.xyz`. 
I have also installed the Figma MCP server in VS Code. Let me know if you have issues accessing the file. 

I have began setting up and configuring Github, Vercel. You should find `/hyperplace-org` as a directory on Github. We need to configure the details. We want to check Cloudflare to make that's set it up and configured properly.  

Set up `STATUS.md` and `Claude.md` to help us keep track. 

```
