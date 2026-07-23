# Rhema Public Academy — Website

Static site for Rhema Public Academy, Khumji Bazar, Noney District, Manipur.
Live at: https://rhema-phi-rouge.vercel.app/

## Stack

- Static HTML, no build step
- Tailwind CSS via CDN (config inlined per page — see note below)
- Vanilla JS for the Notices and Gallery pages
- Google Forms → Google Sheets → Apps Script Web App (JSON) for live content
- Deployed on Vercel

## File structure

```
.
├── index.html          Home — welcome message, President's & Principal's
│                        messages, quick facts, leadership
├── about.html           School history, mission, leadership grid
├── facilities.html      Infrastructure ledger (UDISE-sourced data)
├── notices.html         Live notice board (Apps Script feed)
├── gallery.html         Live photo gallery (Apps Script feed)
├── contact.html          Location, Formspree contact form
├── sitemap.xml           Lists all 6 pages, for search engines
├── robots.txt            Points crawlers to sitemap.xml
└── assets/
    └── style.css         Shared design tokens (colors, fonts, ledger/ridge
                           components) — linked by every page
```

## Design system

Forest green / ochre palette, Fraunces (display), Source Sans 3 (body),
IBM Plex Mono (data/labels). Recurring motifs: a repeated ridge-line SVG
divider between sections (hills of Noney), and "ledger card" components
styled like a paper record for anything that's literal government/UDISE
data.

Tailwind's config (`tailwind.config = {...}`) is duplicated in a
`<script>` block on every page rather than centralized, since this is a
plain static site with no build step or shared template system —
`fetch()`-based partials would break local file:// previews. If you add
a build step later, this is the first thing worth centralizing.

## Live content integration (Notices & Gallery)

Both pages fetch JSON from a Google Apps Script Web App URL hardcoded
near the top of the `<script>` block on each page (`NOTICE_SCRIPT_URL`,
`GALLERY_SCRIPT_URL`). Each script's expected payload shape is documented
inline in comments above the fetch call. Both pages fall back to sample/
placeholder content if the URL is empty or the fetch fails, so the site
never looks broken mid-setup.

**Current status:** both endpoints are live and returning data.

**Known gap:** the Apps Script URLs are hardcoded directly in public
HTML. Fine for a read-only `doGet` that only serves content, but worth
confirming neither deployment also accepts `doPost` for writes/deletes —
anyone can view source and hit the URL directly.

## SEO

`index.html` only (per your call — not replicated on the other five
pages) has:
- Dense, keyword-front-loaded title and meta description
- `meta keywords` (low ranking value today, included for parity with
  other school sites in this series)
- Canonical URL pointing at `/index.html`
- Open Graph + Twitter Card tags, `og:locale: en_IN`
- `School` JSON-LD structured data

**Not yet functional — needs real assets/data, not code:**
- `assets/hero.jpeg` and `assets/logo.png` don't exist yet. Every
  image-dependent tag (`og:image`, `twitter:image`, JSON-LD `logo`/
  `image`) points at these paths and will silently fail to render
  until the files are added.
- `postalCode` in the JSON-LD `address` block is an empty string —
  no PIN code was available. Fill it in directly in `index.html`.

`sitemap.xml` lists all 6 pages regardless of their SEO-tag status —
the sitemap controls what gets crawled/indexed, not what has rich
previews. If any page should be excluded from search results, that
needs a `noindex` meta tag or removal from the sitemap, not just
missing OG tags.

## Known open items

- `contact.html` — phone and email are placeholder text ("Add school
  phone number" / "Add school email"); the contact form posts to
  `https://formspree.io/f/YOUR_FORM_ID`, which needs a real Formspree
  endpoint or submissions vanish silently.
- Facilities data (`facilities.html`) is sourced from a UDISE filing
  originally tied to a "Noney Pt-1 / Rhema Public School" record —
  relabeled to Khumji Bazar per your confirmation that it's the same
  campus. Worth a periodic sanity check against what's actually true
  on the ground.
- Dr. P.G. Vagis is listed as "President" with no expanded title —
  "IET" was never resolved.

## Deployment

Static files, no build step. Push to the connected GitHub repo (or
redeploy manually) and Vercel serves it directly — same pattern as the
other school sites in this series.
