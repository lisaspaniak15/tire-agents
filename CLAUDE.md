# tire-agents

## What This Repo Is

`tire-agents` is the agent-facing portal for Truth in Real Estate (rebranding from Kati Spaniak Team), planned to live at https://tire-agents.fly.dev. It is **not yet built** — the current deployment is a static "coming soon" placeholder page marketing the TruthCertified agent program. It is not the CRM (that lives in `truth-in-real-estate`). This repo is reserved for the future phase where certified agents will log in, view referral dashboards, track performance, and manage their TruthCertified status. The four-site TIRE architecture is: `tire-main` (marketing), `tire-clients` (client portal), `tire-agents` (agent portal), and `truth-in-real-estate` (internal CRM).

---

## Tech Stack

- **Frontend:** Single static HTML file (`index.html`) — no JavaScript framework, no build step, no JS at all
- **Styling:** Inline CSS within `index.html`, Google Fonts (loaded via CDN)
- **Server:** nginx:alpine (Docker container)
- **Deployment:** Fly.io (Docker-based)
- **Dependencies:** None (package.json exists but has no dependencies)

---

## File Structure

```
tire-agents/
├── index.html      # The entire frontend — one static HTML/CSS "coming soon" page
├── package.json    # Minimal; only script is "start" (npx serve); no deps
├── Dockerfile      # Builds nginx:alpine image, copies index.html + nginx.conf
├── nginx.conf      # nginx config; listens on port 8080, SPA-style fallback to index.html
├── fly.toml        # Fly.io deployment config (app: tire-agents, region: iad)
├── .gitignore
└── CLAUDE.md
```

---

## How to Run / Develop

**Local dev (simple file serve):**
```bash
npx serve -p 3003 --open
```
Or just open `index.html` directly in a browser — there is no build step and no JS to execute.

**No build step required.** All changes are made directly to `index.html`.

---

## Deployment

- **Live URL:** https://tire-agents.fly.dev
- **Platform:** Fly.io
- **Region:** iad (Ashburn, VA)
- **Config:** `fly.toml` — app name `tire-agents`, internal port 8080, force HTTPS, scales to zero (min_machines_running: 0)
- **VM:** 256mb RAM, shared CPU, 1 vCPU

**To deploy:**
```bash
fly deploy
```
This builds the Docker image (nginx:alpine + index.html), pushes to Fly.io, and serves on port 8080.

The Dockerfile copies `index.html` and `nginx.conf` into the nginx image. Any change to `index.html` requires a `fly deploy` to go live.

---

## Environment Variables

None required. This is a fully static site with no server-side logic, no API calls, and no secrets. No `.env` file needed.

When the real agent portal is built (future phase), it will need at minimum:
- `SUPABASE_URL`
- `SUPABASE_ANON_KEY`

---

## Key Patterns / Conventions

- **All edits go in `index.html`** — there are no components, no partials, no imports.
- **Design tokens** (matching the broader TIRE design system):
  - Primary brand color: `#1B4332` (dark green)
  - Accent: `#D4AF37` (gold)
  - Text: `#1a1a1a`
  - Font: Inter (loaded from Google Fonts)
- **External links** in the current placeholder:
  - CTA links to `https://katispaniak.com` (will update when brand rebrands to truthinrealestate.com)
  - Footer links to `https://tire-main.fly.dev` and `https://tire-clients.fly.dev`
- When the real portal is built, follow the same Supabase/React/Deno pattern used in `truth-in-real-estate` (the CRM repo).
- **All new Supabase tables** must include: `GRANT SELECT/INSERT TO anon, authenticated;` — otherwise the app gets "permission denied" errors.

---

## Integrations

Currently: **none**. The placeholder page has no API calls or third-party integrations.

Planned integrations for the future agent portal (not yet built):
- **Supabase** — agent auth, referral data, performance tracking
- **Dropbox Sign** — referral agreement signing (template IDs documented in CRM repo)
- **GHL (Go High Level)** — agent lead tracking (Praveen manages this workflow)
- **Skyslope** — transaction/compliance tracking (Shail manages this workflow)

---

## Known Issues / Gotchas

- **This repo is a placeholder.** Do not expect any dynamic functionality — there is intentionally zero JavaScript.
- **CTA links point to `katispaniak.com`**, not `truthinrealestate.com`. These will need to be updated when the brand rebrand completes.
- **nginx.conf has SPA-style fallback** (`try_files $uri $uri/ /index.html`) — this is correct for when a real SPA is eventually added, but has no effect currently since there's only one page.
- **`fly deploy` is the only deploy path** — there is no CI/CD pipeline. Deploys are manual from the CLI.
- **Security note:** A HubSpot token was previously hardcoded in Git in a related repo (`truth-in-real-estate`). This repo currently has no secrets, but when secrets are added in future phases, use Fly.io secrets (`fly secrets set`) — never hardcode in source files.

---

## Machine Setup

- **Desktop (main):** Lisa's primary dev machine. Has full credentials and `flyctl` authenticated. Use for deployments.
- **Laptop (secondary):** Being brought to full parity. If `flyctl` isn't authenticated, note the task and deploy from the desktop.

---

## Session Update Rule

At the end of every session, update this CLAUDE.md to reflect any significant changes made (new features, files, deployment changes, process changes).
