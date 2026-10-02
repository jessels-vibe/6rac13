# 6rac13 — Claude Code Context

## Project Overview
Portfolio and shop site for Grace Fairchild Vanderlaan (brand: **6rac13**), a multimedia artist based in Los Angeles.
Disciplines: Art (murals, portraits, film backgrounds, photography, ceramics, logo design), Video Art (documentaries, stop motion, music videos), Events (gallery, printmaking classes).

- **Live site:** TBD (GitHub Pages + custom domain)
- **Hosting:** GitHub Pages
- **Admin login:** TBD (create in Firebase Console → Authentication → Email/Password)

## Tech Stack
- **Frontend:** HTML/CSS/JS — vanilla, no build step, GitHub Pages static site
- **Fonts:** Pinyon Script (script titles) + Jost (body/nav) — Google Fonts
- **Admin backend:** Firebase (Auth + Realtime DB + Storage)
- **Contact form:** Formspree (replace `PLACEHOLDER` in contact.html)

## Design Tokens
```
--blue:   #1C1CCC   ← primary background (all pages except shop)
--yellow: #F2C800   ← accent, typography, arrows
--dark:   #28283A   ← dark charcoal
--red:    #CC1818   ← accent / CTA
--cream:  #F0E4C4   ← postcard footer background
```
- Script font: **Pinyon Script** (Google Fonts — closest to Gracie's Amoresa Aged)
- UI font: **Jost** (Google Fonts — closest to Gracie's Coco Gothic)
- Nav: fixed, sticky. Uppercase, 12px, letter-spacing 0.12em
- Shop page: yellow background, blue text (inverted from all other pages)

## Firebase Data Structure

```
/projects/{pushId}
  title:        string
  description:  string
  category:     "art" | "videoart" | "events"
  heroType:     "image" | "video"
  heroUrl:      string  (Firebase Storage download URL)
  btsPhotos:    { [key: string]: string }  (keyed by bts_<timestamp>)
  featured:     boolean  ← shows on home page (index.html)
  visible:      boolean  ← shows in work.html gallery
  order:        number   ← sort order (lower = first)
  addedAt:      number   (Unix timestamp)

/shopItems/{pushId}
  title:        string
  description:  string
  price:        string   (e.g. "$35")
  category:     "Prints" | "Lighters" | "Stickers" | "Shirts" | "Zines" | "Other"
  platform:     "Blurb" | "Etsy" | "Society6" | "Shopify" | "Other"
  externalUrl:  string   (checkout link — opens in new tab)
  imageUrl:     string   (Firebase Storage download URL)
  visible:      boolean
  order:        number
```

Firebase Storage paths:
- `projects/{projectId}/hero.{ext}` — hero image or video
- `projects/{projectId}/bts/bts_{timestamp}.{ext}` — BTS photos
- `shop/{itemId}/image.{ext}` — shop item product photo

## Pages

| File | Purpose | Notes |
|------|---------|-------|
| `index.html` | Home — ID card hero + floating creatures + featured works grid | Featured projects: `visible && featured` |
| `work.html` | Portfolio gallery — filterable by Art / Video Art / Events | URL param `?filter=art` etc. |
| `project.html` | Individual project — hero, title, desc, prev/next, BTS photos | URL param `?id={firebaseKey}&filter={cat}` |
| `shop.html` | Shop — yellow background, admin-managed items, external checkout | URL param none needed |
| `contact.html` | Contact — form (Formspree) + email + Instagram | |
| `admin.html` | Firebase-gated admin panel — Projects tab + Shop tab | Auth required |

## Assets (`assets/` folder)
- `g-logo.png` — chrome metallic G logo (nav icon + postcard stamp)
- `id-card.png` — Grace Fairchild Vanderlaan ID card (home hero)
- `rabbit.png`, `snake.png`, `worm.png`, `millipede.png` — stop motion creatures
- `metal-fly.png`, `mama-bird.png`, `venus-flytrap.png` — stop motion creatures
- `blue-fig.png`, `red-fig.png` — stop motion figure characters

## Setup Checklist (one-time)
1. **Firebase project:** Create new project at console.firebase.google.com
2. **Firebase Auth:** Enable Email/Password provider → create admin user
3. **Realtime Database:** Create in US region, set rules to auth-gated writes / public reads
4. **Storage:** Enable → set rules (public read, auth-required write)
5. **Wire in config:** Replace all `"PLACEHOLDER"` strings in `index.html`, `work.html`, `project.html`, `shop.html`, `admin.html` (5 files) with real Firebase config object values
6. **Formspree:** Create endpoint at formspree.io, replace `PLACEHOLDER` in `contact.html`
7. **GitHub repo:** Create repo → push this folder → enable GitHub Pages (branch: main, root `/`)
8. **Custom domain:** Point domain DNS to GitHub Pages

## Firebase Security Rules (Realtime DB — recommended)
```json
{
  "rules": {
    ".read": true,
    ".write": "auth != null"
  }
}
```

## Work Categories
- `art` → Art (Murals, Portraits, Film Backgrounds, Photography, Ceramics, Logo Designs)
- `videoart` → Video Art (Documentaries, Stop Motion, Music Videos)
- `events` → Events (Heartfelt Gallery, Printmaking Classes)

## Nav Dropdown (Work)
Clicking "Work" in the nav opens a dropdown with: All Work / Art / Video Art / Events.
Selecting a filter navigates to `work.html?filter={cat}` and updates the page title + filter bar.

## Key Rules
- All pages share the same fixed nav with Work dropdown
- Every page ends with the postcard footer (cream background, vintage postcard design, G logo stamp)
- Do not remove `mix-blend-mode: multiply` from creature images — removes white BG on blue
- Shop page is the only yellow-background page — all CSS is inverted
- project.html passes `filter` param to maintain category context for prev/next navigation

## Git Workflow
- Main branch: `main`
- Always push to origin/main after committing — changes not pushed don't go live on GitHub Pages
- After every code change, append an entry to the Change Log below

---

## Change Log

### 2026-09-28 — Initial build

**What was built:** Full site from scratch — 6 pages + admin panel.

**Pages:** index.html (home), work.html (portfolio), project.html (individual project), shop.html, contact.html, admin.html.

**Admin features:** Firebase Auth login, two tabs (Projects + Shop), CRUD with add/edit/delete modals, hero image/video upload, BTS photo multi-upload with per-photo delete, drag-and-drop reorder, featured/visible toggles, Save & Publish pattern (stages order/visibility changes, writes all at once).

**Still needs:** Real Firebase config (replace PLACEHOLDER in 5 files), Formspree endpoint (contact.html), admin login email created in Firebase Console, GitHub repo + Pages setup.

### 2026-09-29 — Revision pass A

**index.html:** Added "All Work" to nav dropdown + mobile sub-links. Removed duplicate Video Art `<h2>`. Stop Motion title pushed below nav (`top: calc(var(--nav-h) + 8px)`). Inline project expand system — tile click expands panel in grid (CSS grid-template-rows transition), pushState URL `?p=id`, Esc/× closes, back button closes. Removed category label from expanded view. Expand hero uses `object-fit: contain` (not cropped). Multi-category frontend: `getCategories()` helper reads `categories[]` with fallback to legacy `category` string. Title fades (opacity transition) when switching filters. Mobile postcard form removed — single postcard scales to all widths. Postcard textarea: no border, `padding: 18% 8%`, `line-height: 1.5`, 200-char limit with counter. BTS video links rendered as clickable platform cards.

**contact.html:** Reduced header gap (`padding-top: 40px`, `margin-bottom: 16px`). Mobile postcard form removed. Same postcard + char counter + no-border textarea. "All Work" added to nav dropdown.

**admin.html:** Category single `<select>` replaced with multi-select chip checkboxes. BTS photos section replaced with repeatable video link list (URL + optional label, drag-to-reorder). `saveProject()` writes `categories[]` array and `btsLinks[]`. `getCategories()` migration helper for old `category` string data.

**shop.html:** Added "All Work" to nav dropdown + mobile sub-links.

### 2026-09-30 — Revision pass B

**index.html:** Reduced contact-strip bottom padding to `0` (removed dead space between "Get in touch" and postcard). Inline expand scroll now accounts for fixed nav — uses `getBoundingClientRect()` + `scrollTo()` offset by `--nav-h + 8px` so hero isn't hidden under nav. `expand-body` padding-top increased `36px → 56px` for gap between hero and title.

**shop.html:** Postcard CSS brought in line with index.html — removed `border: 2px solid`, removed `margin-top: 100px` / `margin-left: 20px`, set `padding: 22% 6% 4% 11%`, `line-height: 1.5`, `font-size: clamp(13px, 1.6vw, 18px)`. Fixed `pc-to` padding-bottom `5% → 2.5%`. Fixed `pc-send` margin-right `50px → 70px`. Added `.pc-char-count` CSS, `id="pcMsg"` + `maxlength="200"` to textarea, char counter span + JS. Removed mobile postcard form entirely (CSS, HTML, JS). Added `margin-top: 60px` on postcard-footer to separate it from shop grid.

### 2026-10-02 — Shop header cleanup

**shop.html:** Removed `<p class="header-label">The 6rac13 Shop</p>` — redundant label above the Shop h1.

### 2026-09-30 — Revision pass C

**All pages (index, contact, shop, work, project, stopmotion):**
- Postcard footer margin changed from `var(--nav-h)` bottom to `var(--nav-h)` — leaves exactly one nav-height of space below the card on all pages.
- `.pc-send` shifted down 20px via `position: relative; top: 20px`.
- Textarea `maxlength` raised `200 → 500`; char counter updated to show `/ 500`.
- `.pc-wrap` now has `overflow: hidden; aspect-ratio: 1774 / 1100` — clips the transparent bottom ~33% of postcard.png (image is 1774×1650 but card design occupies only the top ~1100px). Root fix for the "dead space below postcard" issue that persisted across all pages.
