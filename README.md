# altdataforge-website

Source for [altdataforge.com](https://altdataforge.com) — Alt Data Forge's public marketing site.

**Stack:** plain HTML + vanilla CSS. Zero build step. Zero dependencies. Two pages.

**Deploy:** Cloudflare Pages, auto-deploys on push to `main`.

---

## Project layout

```
altdataforge-website/
├── index.html          # Home — hero + contact form
├── about.html          # About — 4-paragraph founder + mission
├── style.css           # Single stylesheet, ~150 lines
├── robots.txt          # Search engine instructions
├── sitemap.xml         # 2-page sitemap
└── README.md           # This file
```

---

## Local preview

No build step needed. Just open `index.html` in a browser, or serve with any static server:

```bash
# Option 1: Python (built-in)
cd altdataforge-website
python -m http.server 8000
# Visit http://localhost:8000/

# Option 2: Node http-server
npx http-server -p 8000
# Visit http://localhost:8000/
```

The site renders identically locally and in production.

---

## Source-of-truth coupling

The HTML body content **must mirror** [`docs/plans/ALTDATAFORGE_WEBSITE_COPY.md`](https://github.com/AlexBocio/phoenix-private/blob/main/docs/plans/ALTDATAFORGE_WEBSITE_COPY.md) v3 LOCKED in the Phoenix monorepo (private). Any copy change happens in that doc first; bump the version; mirror the change to the HTML files here; commit and push; Cloudflare Pages auto-deploys.

This separation is intentional:
- Phoenix monorepo: private. Holds the locked copy doc, the `page_state.md` deployment register at `docs/monetization/channels/website/`, the activity log, and the inbound-form-submission register.
- `altdataforge-website` (this repo): public. Holds only the HTML/CSS/static assets that get deployed to the live site. **Nothing proprietary lives here** — everything in this repo is already public-readable on `altdataforge.com` via View Source.

---

## Discipline rules (non-negotiable)

These mirror the locked copy doc and the channel GM at `docs/monetization/channels/website/README.md`:

| Rule | Why |
|---|---|
| **No Bundle A specifics in body copy** (FDA, biotech catalysts, drug recalls, warning letters, FAERS, 510(k), Open Payments, PDUFA, CRL) | ADR-001 Tegus discipline — preserves wedge value until 5 paid Bundle A customers |
| **No "Phoenix" mentions** anywhere — body, footer, alt-text, meta tags, comments, CSS class names | External brand discipline; "Phoenix" is internal codename only |
| **No pricing on the site** | Per locked v3 — pricing is conversation-only |
| **No Substack link until first Substack post is live** | Tegus discipline + audience signal |
| **Open-license sources only** in any data shown | FINRA / USPTO / SEC EDGAR / Congress / FRED-BEA-BLS safe; Polygon-derived data forbidden |
| **No tracking pixels beyond GA4** | Buy-side prospects inspect this; no third-party scripts beyond Squarespace native + 1 GA4 |

---

## Contact form

The form on `index.html` POSTs to **Formspree** (free tier: 50 submissions/month).

### One-time setup (do once after first deploy)

1. Sign up at https://formspree.io with `AlexB@AltDataForge.com`
2. Create a new form named "altdataforge.com contact"
3. Set notification email to `AlexB@AltDataForge.com`
4. Copy the form's endpoint URL (looks like `https://formspree.io/f/abcdEFGH`)
5. Replace `YOUR_FORMSPREE_ID` in `index.html` line ~28 with your form ID
6. Commit + push — Cloudflare Pages re-deploys automatically

### Submissions

Every submission emails `AlexB@AltDataForge.com`. Per Phoenix monorepo `docs/monetization/channels/website/inbound_log.md` PII protocol: log only date + topic tag + paraphrased intent + referrer + first-touch state. **Never paste raw email bodies** into any persistent doc.

### When to upgrade beyond Formspree

If submission volume exceeds 50/month, upgrade Formspree ($10/mo) OR migrate to a Cloudflare Pages Function for free unlimited (more setup).

---

## Deploy — Cloudflare Pages

### One-time setup

1. Create Cloudflare account at https://dash.cloudflare.com (free)
2. Go to **Workers & Pages** → **Create application** → **Pages** → **Connect to Git**
3. Authorize the GitHub integration; select `AlexBocio/altdataforge-website`
4. Build settings:
   - **Framework preset:** None
   - **Build command:** (leave empty — no build step)
   - **Build output directory:** `/` (root)
   - **Environment variables:** none
5. Click **Save and Deploy**. First deploy takes ~30 seconds.
6. Cloudflare assigns a `*.pages.dev` URL — verify the site renders correctly there before custom-domain swap.

### Custom domain — DNS swap

Once `*.pages.dev` URL renders correctly:

1. In Cloudflare Pages → your project → **Custom domains** → **Set up a custom domain**
2. Enter `altdataforge.com` → Cloudflare prompts for DNS configuration
3. Cloudflare guides you through pointing the domain — typically a CNAME from `altdataforge.com` to `<your-project>.pages.dev`
4. Wait 5–15 min for DNS propagation
5. Visit `https://altdataforge.com` — verify it renders with HTTPS (Cloudflare provisions the cert automatically)
6. Repeat for `www.altdataforge.com` if desired (set as redirect to apex)

### Decommission Squarespace

After DNS swap is confirmed and Cloudflare-served site is rendering:

1. Cancel Squarespace renewal (don't delete account immediately — keep for 30 days as fallback)
2. Email at `AlexB@AltDataForge.com` is unaffected (Google Workspace handles email; Squarespace was only the website host)
3. After 30-day fallback period, delete the Squarespace site
4. Update `docs/monetization/channels/website/page_state.md` in Phoenix monorepo: flip Home + About from `pending` to `live`; update "Live copy version" column

---

## Update workflow (for ongoing copy changes)

```
1. Edit ALTDATAFORGE_WEBSITE_COPY.md in Phoenix monorepo (canonical)
2. Bump version in frontmatter (v3 → v3.1, or v3.x → v4 for major)
3. Mirror the change here:
     - Edit index.html and/or about.html
     - Edit style.css if visual change required
4. Test locally:
     python -m http.server 8000
5. Commit with message describing what changed:
     git commit -m "copy: <one-line summary>"
6. Push:
     git push origin main
7. Cloudflare Pages auto-deploys in ~30s
8. Update Phoenix monorepo:
     - docs/monetization/channels/website/page_state.md  (live copy version + last edit)
     - docs/monetization/channels/website/activity_log.md  (action: copy-edit, what changed)
```

---

## Branch previews (the killer feature)

Cloudflare Pages auto-creates a preview URL for every branch:

```
git checkout -b new-hero-variant
# ... edit hero copy ...
git push -u origin new-hero-variant
```

Cloudflare deploys to `https://new-hero-variant.altdataforge-website.pages.dev` automatically.

Three variants in three branches = three preview URLs to compare side-by-side. Use this for A/B copy testing without ever touching production.

---

## Decommission paths (emergency)

If for any reason the site needs to come down fast:

1. **Cloudflare Pages → Settings → Pause deployment** (stops new builds; current deploy stays live)
2. **Cloudflare Pages → delete project** (full takedown; DNS will start returning errors)
3. **Re-point DNS to a maintenance page** (set CNAME to a static "we'll be back" page hosted elsewhere)

For non-emergency rollback:
- Cloudflare Pages → Deployments → click any past deployment → "Rollback to this deployment"
- One-click revert to any previously-deployed state. No code changes required.

---

## Why HTML + vanilla CSS over Astro / Next.js / Tailwind

Per the locked v3 doctrine: **the site is intentionally minimal — 2 pages, no images, no JS**. Anything more than plain HTML + a stylesheet is over-engineering for the v1 scope. If the site grows beyond ~5 pages or needs componentization, migrate to Astro at that point — but not before.

Decision tree for future migrations:

| Trigger | Migration |
|---|---|
| Adding 3+ pages with shared components (header / footer / nav) | Astro |
| Adding a blog / Substack mirror | Astro with content collections |
| Adding interactive widgets (calculator, sample-data preview) | Astro + Svelte islands or Next.js |
| Adding a customer portal / signed-URL R2 download UI | Next.js with Cloudflare Pages Functions |

For now: HTML + CSS. Two files of body content. ~150 lines of styling. Sub-second page loads. Done.

---

## Repo policy

This repo is **public** by design — the deployed site is public-readable anyway via View Source on `altdataforge.com`. Nothing proprietary lives here.

The Phoenix monorepo (private, at `S:\PhoenixBackups\phoenix.git`) has a separate policy: github.com pushes are forbidden. That ban applies to the monorepo specifically, not to this public-marketing repo. Per session log 2026-05-06: this repo's existence was authorized as a single-repo exemption to the github ban.

---

## License

Source code in this repo: MIT (no license file yet — defer to v1 launch decision)
Site copy: All Rights Reserved, Alt Data Forge 2026
