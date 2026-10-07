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

### 2026-10-03 — Stop Motion layout editor

**admin.html:** New "Stop Motion" tab with a full visual canvas editor.
- Character palette lists all 10 built-in PNGs (red-fig, blue-fig, rabbit, flytrap, 2-head worm, millipede, mama-bird, metal-fly, snake, worm). Click any to place it on canvas.
- Upload button adds new PNGs to Firebase Storage (`stopmotion/chars/`) and saves the list to `/stopmotionChars`.
- Desktop / Mobile toggle — each saves a separate layout. Desktop canvas is 16:9, mobile is 9:16 (max 320px wide).
- Per-layer controls: drag to move, corner handles to resize, rotation handle (yellow circle) to rotate, Flip H/V buttons, ↑/↓ z-order, Delete button. Delete/Backspace key also removes selected layer.
- "Save Layout" writes to Firebase `/stopmotionLayout/desktop` and `/stopmotionLayout/mobile`.

**stopmotion.html:** On page load, reads `/stopmotionLayout/desktop` or `/mobile` from Firebase (based on viewport width < 768px). Replaces hardcoded character images with dynamically positioned ones using saved position/size/rotation/flip. Falls back to hardcoded CSS positions if no layout is saved yet.

### 2026-10-03 — Fix Video Art + Stop Motion in index.html SPA

**Root cause discovered:** All nav links use hash routing (`#work/videoart`, `#stopmotion`) which renders views inside `index.html`, NOT the separate `work.html` / `stopmotion.html` files. Previous fixes to those standalone files had no effect on what users see.

**index.html:**
- Video Art hero: replaced blue overlay div with `<h2 class="vah-title">Video Art</h2>`. Added `.vah-title` CSS (`clamp(80px, 18vw, 320px)`, centered, script font). Removed `.vah-overlay` CSS. Route handler now uses `assets/videoart-hero.mp4` directly instead of searching Firebase for a video project. Hides `.page-header` when videoart filter is active; restores it for other filters.
- Stop Motion: fixed `.sm-title` `top: calc(var(--nav-h) + 8px)` → `+20px` and `line-height: 0.88` → `1`. Added Firebase REST API layout loader (`fetch stopmotionLayout/{key}.json`) that runs once when the stopmotion view is first shown, replaces hardcoded character positions with saved layout.

### 2026-10-03 — Stop Motion title + layout loader fix

**stopmotion.html:**
- Fixed title cut-off: changed `.sm-title` from `top: 10%` → `top: calc(var(--nav-h) + 20px)` and `line-height: 0.88` → `line-height: 1` so it clears the fixed nav.
- Replaced Firebase SDK layout loader (compat SDK IIFE) with a simple `fetch` call to the Firebase REST API (`stopmotionLayout/{key}.json`). The SDK approach was silently failing on the live site; the REST API is public-read (rules: `.read: true`), requires no initialization, and is more reliable. Removed the two Firebase compat `<script>` tags from the page.

### 2026-10-02 — Shop header cleanup

**shop.html:** Removed `<p class="header-label">The 6rac13 Shop</p>` — redundant label above the Shop h1.

### 2026-10-03 — Nav consistency + mobile scroll fix

**shop.html, contact.html, work.html, project.html, stopmotion.html:**
- Removed hamburger button + mobile nav overlay (CSS, HTML, JS) from all 5 remaining pages
- All pages now use the same compact inline nav as index.html (Jost, always visible, 11px at ≤640px, dropdown right-aligned)

**index.html:**
- Fixed mobile work grid cut-off: added `setTimeout(() => window.scrollTo(0, 0), 50)` alongside the immediate `scrollTo` in `showView()`. iOS Safari overrides scroll position after hashchange fires; the deferred call runs after the browser's scroll restoration and ensures the view starts at y=0.

### 2026-10-03 — Mobile nav redesign + grid + postcard fix

**index.html:**
- Removed hamburger button, mobile nav overlay (HTML, CSS, JS) entirely
- Nav links now always visible at all screen widths — matches Stone Rock style (Work / Shop / Contact top-right, Work has dropdown)
- At ≤640px: `nav padding: 0 14px`, `nav-links gap: 12px`, font 11px/0.1em letter-spacing; dropdown right-aligned so it doesn't overflow on small screens
- Work grid: removed `@media (max-width: 420px)` single-column override — grid stays 2 columns on all mobile sizes
- Postcard: margin reduced to `0 0` (full bleed) and `pc-msg` top padding reduced from 20% → 13% so text area is taller and more usable on mobile

### 2026-10-03 — Stop Motion project grid + YouTube hero + BTS embeds

**index.html:**
- Added `.sm-projects` section below `.sm-hero` in `#view-stopmotion` with a `work-grid` (`id="smWorkGrid"`)
- Added `currentGridId` variable; `expandProject`, `getGridCols`, `attachTileClicks` now use it instead of hardcoding `'workGrid'`
- Stopmotion route handler now sets `currentGridId = 'smWorkGrid'` and calls `loadProjects` to populate stop-motion-tagged tiles below the hero
- Work route handler sets `currentGridId = 'workGrid'` before loading to reset context
- `tileHTML` now handles `heroType === 'youtube'` — renders YouTube CDN thumbnail
- `expandProject` now handles `heroType === 'youtube'` — renders iframe embed in expand panel
- `btsLinkCardHTML` rewritten: YouTube/Vimeo links now render as `<iframe>` embeds (`.bts-embed` + `.bts-embed-wrap`); other platforms keep card link fallback
- Added `getVimeoId()` helper; removed Vimeo thumbnail async approach (no longer needed)
- CSS: `.expand-bts-link-grid` changed to `flex column`; added `.bts-embed`, `.bts-embed-wrap`, `.bts-embed-label` styles

**admin.html:**
- Added "YouTube" radio tab to Hero Type selector
- Added YouTube URL input field (`#heroYoutubeField`) shown when YouTube selected; "Set YouTube Hero" button validates URL and shows thumbnail preview
- `setHeroType()` handles `youtube` — hides upload zone, shows URL field
- Save/load handles `heroType === 'youtube'` correctly (stores URL as `heroUrl`)
- Project row thumbnail uses YouTube CDN thumb when `heroType === 'youtube'`

### 2026-09-30 — Revision pass C

**All pages (index, contact, shop, work, project, stopmotion):**
- Postcard footer margin changed from `var(--nav-h)` bottom to `var(--nav-h)` — leaves exactly one nav-height of space below the card on all pages.
- `.pc-send` shifted down 20px via `position: relative; top: 20px`.
- Textarea `maxlength` raised `200 → 500`; char counter updated to show `/ 500`.
- `.pc-wrap` now has `overflow: hidden; aspect-ratio: 1774 / 1100` — clips the transparent bottom ~33% of postcard.png (image is 1774×1650 but card design occupies only the top ~1100px). Root fix for the "dead space below postcard" issue that persisted across all pages.

### 2026-10-03 — Postcard editor: image overflow fix + per-row address spacing

**admin.html:**
- Fixed postcard preview image overflow: `.pc-editor-img` now uses `height:100%; object-fit:cover; object-position:top` so the 1774×1650 PNG fills the 1774/1100 aspect-ratio container instead of bleeding below it.
- Added `addrGap1/2/3` params (% of form half-width) to `PC_DEFAULTS` — control vertical spacing before each of the 3 address rows (From, email, Subject).
- Added 3 teal drag handles (numbered 1/2/3) in the addr section of `pcRender()`. Dragging down increases the gap before that row; dragging up decreases it.
- `pcOnMove()` handles `addrGap1/2/3` keys.
- Values readout updated to show all three gap values.
- Legend updated to describe the teal row-gap dots.

**contact.html, shop.html, index.html:**
- Firebase postcard loaders now apply `addrGap1/2/3` as `marginTop` on `.pc-addr` children 1/2/3. Units are % of `.pc-addr` width ≈ form half-width, matching how they're stored.

### 2026-10-03 — Firebase Storage token fix + admin postcard editor improvements

**index.html:**
- Added `storageUrl(url)` helper that strips `?token=...` params from Firebase Storage URLs. Firebase revokes download tokens periodically; stripping the token lets the browser use the public security rules path (`?alt=media`) instead.
- Applied `storageUrl()` to hero image/video src and all BTS photo/video src attributes.
- Added `onerror="this.closest('.bts-photo').style.display='none'"` on BTS `<img>` so tiles that still fail are hidden cleanly.

**admin.html:**
- Widened postcard layout editor preview container from `max-width: 640px` to `max-width: 960px` so the full postcard is visible without cropping.

### 2026-10-07 — Postcard right-panel mobile text tightening

**contact.html:**
- Added mobile overrides in `@media (max-width: 640px)` for the right address panel: `pc-to` 10px, `pc-lbl` 9px, `pc-input` 10px. The `clamp()` floor values (12px, 11px, 12px) were too large for the ~140px-wide right half of the postcard at mobile widths, causing "To: Gracie F. Vanderlaan" to clip and fields to feel oversized.
- Reduced `pc-row` padding from `3.5% 0 1.5%` to `1% 0 0.5%` to tighten vertical spacing between From / email / Subject rows.
- Reduced `pc-send` to 9px / 0.08em letter-spacing and `margin-right: 20px` (was 70px) so the button sits cleanly within the right panel.
