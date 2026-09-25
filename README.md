# Penn MEDIATED — Job Openings

The job-openings page for the [Center on Media, Technology and Democracy](https://mediated.upenn.edu). Static HTML/CSS, no build step. Mirrors the content of the live [`mediated.upenn.edu/job-openings/`](https://mediated.upenn.edu/job-openings/) embed — heading and current-openings status only, not the surrounding WordPress nav/menu bar or site footer (newsletter signup, funder logos, social links, address), which are page chrome supplied by the WordPress wrapper rather than part of the embedded content.

Same conventions as the [`about`](https://github.com/PennMEDIATED/about) and [`home`](https://github.com/PennMEDIATED/home) repos — shared spacing tokens, brand colors, and fonts.

- `index.html` — page markup
- `styles.css` — all styling (design tokens live at the top in `:root`)
- `assets/` — images and logos (none yet — add here if the page grows a logo or graphic)

## Updating content

The `.openings__title` heading never changes. Everything below it is either the empty-state line or one `<article class="job">` per open position.

**Adding a posting.** Copy the `.job` block already in `index.html` and edit it in place:

1. `.job__title` — the role name. Sans, not serif: a role labels a category rather than naming a work or a person, the same call as `events`' `.event-card__title`.
2. `.job__body` — one `<p>` per paragraph of the description. `<strong>` for emphasis; do **not** paste the Unicode bold characters (𝐑𝐞𝐬𝐞𝐚𝐫𝐜𝐡) that WordPress copy sometimes carries, because screen readers read them out character by character.
3. `.job__meta` — the one-line salary/benefits/location summary. Greyed, always last before the link.
4. `.job__apply` — the standalone application link. Category 2 (independent link): dark, weight 600, hairline underline, trailing `⟶`. A link inside a sentence uses `.job__inline` instead — category 1, red-orange with no underline. The two are not interchangeable; see `## Hyperlinks`.

Point both links at the real Penn WorkDay requisition URL — that is what applicants actually use, so a stale link is worse than no link.

**When a posting closes.** Delete its `<article class="job">` block. If that was the last one, restore the empty-state line kept as a comment directly above it:

```html
<p class="openings__lead">We have no current job openings &#8212; please check back soon!</p>
```

Multiple postings stack automatically — `.job + .job` adds the divider rule and spacing, so nothing else needs changing.

## Style guide (shared across `about`, `home`, and `team-job-openings`)

All three repos are static HTML/CSS built off the same design system. If you're adding or editing anything, pull values from here rather than guessing new ones — that's what keeps the sites looking like one brand instead of drifting apart.

### Design tokens (`:root` in `styles.css`)

**Spacing** — Atlassian's 8px scale. Always use the variable, never a raw pixel value:

```
--space-025: 2px   --space-100: 8px   --space-300: 24px  --space-600: 48px
--space-050: 4px   --space-150: 12px  --space-400: 32px  --space-800: 64px
--space-075: 6px   --space-200: 16px  --space-500: 40px  --space-1000: 80px
--space-250: 20px
```

**Color:**

| Token | Hex | Use |
|---|---|---|
| `--c-dark` | `#0d0d0c` | Primary text, dark backgrounds |
| `--c-accent` | `#5533ee` | Brand purple |
| `--c-red` | `#f03d1f` | Brand red/orange, links, tags |
| `--c-gray` | `#888680` | Secondary/muted text |
| `--c-gray-dark` | `#54534f` | Body copy needing real contrast (~8:1 on white) — matches `home`; prefer this over `--c-gray` for paragraph text if this page adds long-form copy |
| `--c-light-bg` | `#f8f7f4` | Placeholder/image background |
| `--c-white` | `#ffffff` | — |
| `--c-bg` | `#ffffff` | Page/body background |

This repo doesn't currently use `--c-light-bg` or the card-arrow badge — both exist in the shared token/component set but have no matching use case here yet, since the page is a single status message. Reach for them if this page grows a listings grid.

**Brand gradient** — used on every purple-to-red surface (the `about` page's orbital section, both repos' newsletter/supporters block, the `home` hero): `linear-gradient(150deg, #5533ee 0%, #df3611 81%)` via `--c-gradient`. Never write this gradient out by hand or approximate it with different stops — reference the variable so a future palette tweak only has to happen in one place per repo. Not currently used on this page (no gradient surface here yet).

**Type:**
- `--f-serif`: `'EB Garamond', Georgia, 'Times New Roman', serif` — headlines, quotes, the "MEDIATED" wordmark
- `--f-sans`: `'DM Sans', system-ui, -apple-system, sans-serif` — everything else

**Layout:** `--max-w: 1440px` page cap, `--pad-x: var(--space-1000)` (80px) side padding on the shared `*__inner` containers, scaling down responsively (32px under 900px, 20px under 480px) — backported from `home` since this page's heading is wide enough to benefit on mobile.

### Layout conventions

- Every section's content wrapper is named `.<section>__inner` and shares one rule (`width:100%; max-width:var(--max-w); margin-inline:auto; padding-inline:var(--pad-x);`). Add new sections to that shared selector list instead of writing a one-off inner container.
- BEM-ish naming: `.block__element`, modifiers as `.block--variant` or `.block__element--variant`.

### Heading and body-copy positioning

- **Body-copy blocks get no `max-width` of their own.** Paragraphs fill the full width of their padded `*__inner` container instead of stopping at a narrower fixed value. A narrower `max-width` inside a wide container looks asymmetric — all the leftover space piles up on the right since text is left-aligned, not centered.
- **A heading immediately followed by body copy uses a flat `--space-300` (24px) gap**, consistently, wherever that pattern occurs across the three repos. `.openings__title` → `.openings__lead` in this repo follows the same rule as `.partners__title` → `.partners__lede` in `about`.

### Shared components

- **No eyebrow/kicker above hero or section headings** — sitewide convention (the `.eyebrow` component that used to sit above headings in `about`, `team-leadership`, and `events` has been removed from all three). Don't add one above "Employment Opportunities" or anywhere else.
- **External-link arrow badge** (`.card-arrow`): a 26px black circle with a white arrow (10px), used on `about`/`home`'s logo and card tiles to signal "opens an external site." Not used on this page yet (no external-link tiles here) — pull it in if a listings-grid card component gets added.
## Embedding this page

WordPress renders the real site; this repo is the source. The launch plan is direct-to-disk deployment, which needs no iframe — but iframe embedding still works and is the documented fallback, so keep this snippet accurate if you rename the repo or change its Pages URL.

Paste into a **Code module** — not a Text module, which mangles iframes and scripts. Two things have to be set, and they deliberately live in different places.

**Per page, in the builder.** The site runs **Divi 5**, where width belongs to the row, not to the module. A Divi 5 row ships at **width 80%, max-width 1080px**, so an untouched embed renders in a narrow column and every full-bleed colour band in the design collapses with it. Set:

- Row → Design → Sizing → **Width 100%** and **Max Width `none`** — `none`, not 100%
- Section → Design → Spacing → **padding 0** top and bottom
- Row → Design → Spacing → **padding 0** top and bottom

These are design settings, so they belong where the next person will look for them. Putting the width in CSS instead leaves the builder showing 80% / 1080px while the page renders full width, and that mismatch costs someone an afternoon eventually.

**Once, sitewide.** Divi has no setting for the last problem: an `<iframe>` is `display: inline` by default, so it sits on a text baseline and leaves a 4–6px gap underneath that nothing in the builder accounts for. Add this once under Divi → Theme Options → Custom CSS and no page needs it again:

```css
.et_pb_code iframe { display: block; width: 100%; border: 0; }
```

It is keyed to `.et_pb_code` rather than a per-section class on purpose — every iframe on this site sits in a Code module, so there is no hook to add and nothing to remember when page fourteen arrives. Divi's own Video and Map modules don't use Code modules, so a collision is unlikely; if someone does add a Code-module embed that shouldn't be full width, give that one its own override rather than reintroducing a class here.

Divi caches its compiled CSS to a static file, so clear that cache (Divi → Theme Options → Builder → "Clear Divi Static CSS File Cache") after editing Custom CSS, or the change will not show for visitors.

On Divi 4 this was all different: a **Fullwidth Section** holding a **Fullwidth Code** module, with separate CSS ID and CSS Class fields on the Advanced tab. Divi 5 removed the section-type chooser (the add-section button offers flex and grid layout options now) and folded ID and class into Advanced → **Attributes**, so ignore Divi 4 tutorials on both points. The embed snippet itself:

```html
<iframe id="pm-team-job-openings" src="https://pennmediated.github.io/team-job-openings/" title="Job Openings — Penn MEDIATED" scrolling="no" loading="lazy" style="width:100%;height:600px;border:0;display:block"></iframe><script>(function(){var f=document.getElementById('pm-team-job-openings');window.addEventListener('message',function(e){if(e.source!==f.contentWindow)return;var d=e.data||{},h=d.frameHeight||(d.type==='partners-page-resize'?d.height:0);if(h)f.style.height=h+'px';});})();</script>
```

The `height` in the snippet is only the starting value. Every Penn MEDIATED page posts its real height to the parent as `{ frameHeight: <int> }` — on load, on resize, once webfonts settle, and on any `ResizeObserver` change, so reveal animations, expanding cards and `<details>` toggles all resize the frame. The listener in the snippet applies it. `grants-rfp` also emits an older `{ type: 'partners-page-resize', height }` message; the snippet accepts both.

The page checks `window.self === window.top` before posting, so opening it directly does nothing. If you add a new page repo, copy the script from the bottom of this `index.html` so it behaves the same way.


## Images and video

This applies to every image, GIF and video added to any Penn MEDIATED repo. It is written to be followed directly — by a person or by a Claude session — without further instruction.

### The one rule that is never optional

**Every `<img>` and `<video>` carries explicit `width` and `height` attributes, holding the file's real intrinsic pixel dimensions.**

```html
<img src="assets/example.webp" width="640" height="334" alt="…">
```

They do not set the display size — CSS does. They give the browser the aspect ratio *before* the file downloads, so it reserves a correctly shaped box instead of collapsing to nothing and shoving everything below it down the page as each file lands. That shift is measured by search engines (Cumulative Layout Shift) and is worse for a reader, who loses their place or clicks a link that just moved.

Every repo has a global `img, video { max-width: 100%; height: auto; display: block; }` reset, so the CSS keeps winning and the attributes only ever contribute the ratio. **Never guess the numbers** — read them off the file.

### Pick the format by what the file is

| Content | Format | Never use |
| --- | --- | --- |
| Photo, screenshot, artwork | **WebP**, quality 88 | PNG or JPEG at full camera resolution |
| Logo, wordmark, icon | **SVG** if you have it, else WebP | — |
| Anything that moves | **MP4** (H.264) + a WebP poster | **GIF, ever** |

GIF is the big one. It has no interframe compression, so a screen recording is roughly ten times the size it needs to be: `research-compendium.gif` was 11.3MB for 290 frames; the identical recording as H.264 is 1.2MB.

### Size it to the box it displays in, not to what you were sent

Find the CSS box the image renders into, then export at **2×** that width for retina. Anything beyond that is bytes the browser downloads and immediately throws away. (`gni-membership.png` was 7992px wide, rendering into a 319px box — a 470KB file doing a 33KB job.)

This repo currently ships no images. If you add one, measure its box at a 1440px viewport and export at 2× that width.

If you are adding an image somewhere not listed, measure the box first (`getBoundingClientRect().width` in the browser, at a 1440px viewport) and double it.

### Commands

Stills — resize and convert in one pass:

```python
from PIL import Image
TARGET = 640                      # 2x the CSS box
im = Image.open('source.png')
w, h = im.size
if w > TARGET:
    im = im.resize((TARGET, round(h * TARGET / w)), Image.LANCZOS)
im.save('out.webp', quality=88, method=6)
print(im.size)                    # <- these are the width/height attributes
```

Animation — MP4 plus a poster frame:

```bash
ffmpeg -i source.gif -movflags +faststart -pix_fmt yuv420p \
       -vf "scale=1280:-2:flags=lanczos" -crf 24 out.mp4
ffmpeg -i source.gif -frames:v 1 -vf "scale=1280:-2:flags=lanczos" poster.png
python3 -c "from PIL import Image; Image.open('poster.png').convert('RGB').save('out-poster.webp', quality=80, method=6)"
ffprobe -v error -show_entries stream=width,height -of default=nw=1 out.mp4
```

`-crf 24` is a good default; raise it toward 30 for a smaller file, lower it toward 20 for a sharper one. `-pix_fmt yuv420p` is required for Safari and iOS.

### Markup for video

```html
<video src="assets/name.mp4" poster="assets/name-poster.webp" width="1280" height="622"
       autoplay muted loop playsinline preload="metadata" aria-label="…"></video>
```

Each attribute earns its place: `muted` is what permits autoplay at all, `playsinline` stops iOS opening it fullscreen, `poster` means the slot is never empty while the video loads, and `aria-label` replaces `alt` (a `<video>` has no `alt`).

CSS cannot stop autoplay, so **a page with video needs the reduced-motion script** at the end of `<body>`. If the page already has one, leave it alone; if you are adding the first video to a page, add it:

```html
<script>
  if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
    document.querySelectorAll('video[autoplay]').forEach(function (v) {
      v.autoplay = false; v.pause(); v.currentTime = 0; v.removeAttribute('loop');
    });
  }
</script>
```

Also check the CSS: any rule that sizes or crops an image needs to name `video` too, or the video slot will not match the image slot it replaced (`.card__image img` becomes `.card__image img, .card__image video`).

### Before you call it done

- [ ] File is WebP, SVG or MP4 — no GIF, no full-resolution PNG or JPEG
- [ ] Its width is about 2× the CSS box it renders into
- [ ] `width`/`height` attributes match the file's real dimensions
- [ ] Real `alt` text (or `aria-label` on a video) that describes the image; empty `alt=""` only if it is purely decorative
- [ ] Lives in this repo's `assets/`, not hotlinked from another site
- [ ] Page opened in a browser at 1440px and ~400px — nothing overflows, nothing jumps on load
- [ ] Originals are not committed alongside the optimised file; git history is the backup

Do not commit an unoptimised original "just in case" — the previous commit already holds it, and a duplicate in the working tree also ships to the server.

## Hyperlinks

One taxonomy, five categories, shared by every page repo. Pick the category by what the link *is*, not by which repo you happen to be editing.

**1. In-text links** — embedded mid-sentence in flowing prose.

| ground | text | underline | hover |
| --- | --- | --- | --- |
| white / light | `--c-red-dark` | none | fade to `opacity: 0.7` |
| colour / gradient | `--c-white` | `border-bottom: 1px solid rgba(255, 255, 255, 0.5)` | fade to `opacity: 0.7` |

Both grounds use `font-weight: 500` and `transition: opacity 0.15s`, and both fade rather than change hue. On a white ground **colour is the affordance** — no underline; the underline is category 2's job. On a coloured ground the red is invisible, so the link goes white and takes the hairline rule instead. Where an underline is used it is a `border-bottom`, never `text-decoration`.

#### Why interactive red is `--c-red-dark`, not `--c-red`

`--c-red-dark` (`#df3611`) is the closing stop of `--c-gradient`, promoted to a token of its own and declared in all twelve repos.

`--c-red` (`#f03d1f`) measures roughly **3.9:1** against white — under the 4.5:1 WCAG AA threshold for body text, and the same 3.9:1 applies to white text sitting on a `--c-red` fill. `--c-red-dark` measures about **4.5:1** either way and clears it. The two are near-indistinguishable at text sizes, so this is a contrast fix, not a visual change.

**The rule: anything you click is `--c-red-dark`.** Links and buttons take it wherever they would otherwise be red-orange — as text colour, as a box fill, as a hover or active state, and on the markers inside them (disclosure chevrons and their labels). It applies in every category and every state.

**`--c-red` stays the brand accent for everything you don't click**: section headings, eyebrow and metadata labels, tag and pill backgrounds, accent bars and card borders, full-width colour bands, the `.card-arrow` hover gradient, and focus rings. These are either large text, non-text UI at the 3:1 threshold, or sit on a tinted rather than white ground.

The one deliberate hold-out is red link text on a **dark** ground (`home`'s `.footer__email`), where the darker red would *reduce* contrast rather than improve it. That link has a separate outstanding issue — on a dark ground the standard is white text with an opacity fade, not red at all.

**2. Independent links** — a standalone text link that isn't inside a sentence ("Learn More About the Center", "Download the Full Schedule"). Unlike category 1 these carry the underline and are set in the body colour, so they read as a control rather than as emphasis inside a sentence:

| ground | text | underline | hover |
| --- | --- | --- | --- |
| white / light | `--c-dark`, `font-weight: 600` | `border-bottom: 1px solid rgba(13, 13, 12, 0.35)` | text and underline both turn `--c-red-dark` (`transition: color 0.15s, border-color 0.15s`) |
| colour / gradient | `--c-white`, `font-weight: 600` | `border-bottom: 1px solid rgba(255, 255, 255, 0.5)` | fade to `opacity: 0.7` |

Plus a **thin arrow** `⟶` after the text. Use `⟶` (`&#10230;`), not the `↗` badge from category 4.

**3. Document buttons** — an independent link that opens a document (a PDF, a report). A filled button box, not text:

| ground | box | text |
| --- | --- | --- |
| white / light | `--c-red-dark` | `--c-white` |
| colour / gradient | `--c-white` | `--c-dark` |

Hover is **movement, not colour** — a lift or nudge. Do not darken or recolour the box.

**4. Links to another web page** — this site or an external one. The containing box carries the shared `.card-arrow`: a 26px dark circle with a white `↗`, in the box's top corner. On hover the arrow scales slightly and its background becomes a sliding purple-to-orange gradient (`@keyframes card-arrow-slide`), and the box itself animates. No separate text button — the whole box is the link.

**Exception:** a link to a research paper is category 2, not this — thin arrow, no badge.

**5. Hyperlinked headings** — a heading that is itself a link (a post title, a card title). Sits in the body colour and shifts to `--c-red-dark` on hover (or fades, on a coloured ground), with **no arrow and no underline**.

### Dropdowns and disclosures

A dropdown, `<details>` block or expand/collapse control uses one affordance sitewide: a **chevron SVG** (`M2 5l5 5 5-5`, 13×13, `--c-red-dark` stroke, `stroke-width: 1.8`) beside a `--c-red-dark` label at `--fs-small`, rotating `180deg` on open with `transition: transform 0.25s`. See `llm-civic-discourse`'s "Full summary & details" toggle for the reference implementation.

Never leave the marker to the browser — style `<select>` with `appearance: none` and supply the chevron, and hide the native `<summary>` marker. The `↗` circle badge is category 4's language and does not belong on a disclosure control.

- **Scroll-triggered reveal**: sections carry a `.reveal` class; once the page's script confirms it can run (`js-reveal-ready` added to `<html>`), each fades/lifts in the first time it scrolls into view. This page's single `.openings` section uses the same one-shot reveal as most `about`/`home` sections (no `.reveal--toggle` re-fade behavior, since there's nothing to scroll past). A no-JS fallback marks everything visible immediately.

### Keeping the repos in sync

`about`, `home`, and `team-job-openings` are separate repos with duplicated CSS, not a shared stylesheet — so consistency is a discipline, not something enforced automatically. When you change a shared token or component in one repo, check whether the same change belongs in the others before considering the task done.

## Typography

Sitewide convention. The `--fs-*`/`--lh-*` block at the top of `styles.css` is canonical and identical in every page repo.

**Two families, no third — and the split is by what the text *is*, not by heading level.**

- **`--f-serif` (EB Garamond)** — page titles, and **titles of works or names of people**: a blog post title, a paper title, a person's name. Plus pull-quote copy.
- **`--f-sans` (DM Sans)** — **section headings**, card and UI labels, running prose, metadata, controls, and uppercase micro-labels.

The one that trips people up: **a section heading is not serif.** "Past Events", "Funded Grants", "Latest Updates" are all DM Sans 700 at `--fs-h2`. Serif marks a thing that has its own name — `blog`'s `.post-item h2`, `research-compendium`'s `.entry__title`, `team-leadership`'s `.person-card__name`, `team-faculty`'s `#fb-name` and `#pd-title` — while sans marks the furniture around it, *including card titles that label a category rather than name a work* (`events`' `.event-card__title`, `data`'s `.data-project__title`, `home`'s `.news-card__title`).

So two `--fs-h3` card titles can legitimately differ: a post title is serif because it names a work, an event card title is sans because it labels an event. That is the rule, not an inconsistency.

**Serif titles are weight 600**, sitewide and without exception — page titles, post and paper titles, people's names. (Serif *body* copy is a different thing: pull-quotes and blockquotes stay at their own weights.)

There is no monospace face.

**Measure is capped per container, and the numbers differ on purpose.** A `max-width` on body copy is a reading-comfort cap (~65-75 characters), not a layout width — so it depends on how wide the container already is. `events`' `.event-card__desc` caps at **560px** because `.past-events__grid` is two columns and a card is half the row; `blog`'s `.post-excerpt` caps at **720px** because the feed is a single full-width column. Both land the same line length. **Do not "align" these two numbers** — matching them would make one of the two pages read wrong.

**Sizes come from tokens, never raw px.**

| Token | Mobile (=<480px) | Desktop (>=1440px) | Used for |
| --- | --- | --- | --- |
| `--fs-display` | 36px | 76px | full-bleed hero |
| `--fs-h1` | 36px | 56px | page title |
| `--fs-h2` | 26px | 40px | section titles |
| `--fs-h3` | 20px | 24px | card and third-level titles |
| `--fs-lede` | 18px | 20px | intro paragraphs |
| `--fs-body` | 16px | 16px | body copy |
| `--fs-small` | 14px | 14px | captions, meta, form controls |
| `--fs-small-serif` | 15px | 15px | EB Garamond at small sizes |
| `--fs-micro` | 12px | 12px | uppercase labels, tags, counts |

The top five are `clamp()` values that interpolate across the viewport, so tablet widths need no separate `@media` override. Only add a breakpoint font-size when a specific layout actually demands it.

**12px is the floor.** Nothing ships smaller. EB Garamond and uppercase-with-letter-spacing both read smaller than their nominal size, which is what `--fs-small-serif` and the 12px floor exist to absorb.

**Line heights are tokens too** — `--lh-display` 1.05, `--lh-heading` 1.15, `--lh-lede` 1.26, `--lh-title` 1.3, `--lh-body` 1.55. Never set a line-height in px; it breaks the fluid sizes.

**Heading gaps.** Section title to first content is `var(--space-300)` (24px); page or hero title to content is `var(--space-250)` (20px).

**Section rhythm.** A full-width colored section carries `var(--space-1000)` (80px) top and bottom padding, so its heading never sits flush against the band's edge. The page hero's bottom padding is `var(--space-600)` (48px) — shorter than 80px because the section below supplies its own.
