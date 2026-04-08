# Overnight Theme Pass — Morning Report

**Date:** 2026-04-07 → 2026-04-08
**Work by:** Claude Code (unattended)
**Brief:** Make both pages as close as possible to the live
adeliarisk.com originals. Light-only. Customize the theme as needed.

---

## The headline

Both pages now look legitimately close to the live adeliarisk.com
versions. I went from "recognizable Adelia Risk vibe" to "this is
pretty close to the real page" on both.

**Three URLs to look at this morning:**

1. https://my-emdash-site.josh-ablett.workers.dev/ — homepage (still
   the generic blog index; I didn't touch it)
2. https://my-emdash-site.josh-ablett.workers.dev/pages/ria-cybersecurity-services
   — the RIA services page
3. https://my-emdash-site.josh-ablett.workers.dev/posts/how-online-banking-security-protects-your-accounts
   — the banking security blog post

Open each next to the corresponding adeliarisk.com URL and scroll
through side-by-side. That's the fastest way to judge fidelity.

---

## What I did, in order

### 1. Light-only theme

Dark mode is completely gone.

- Stripped the `prefers-color-scheme: dark` media query and all
  `:root.dark` overrides from `Base.astro`
- Removed the theme-init inline script
- Deleted the three-button theme switcher from `SiteFooter.astro`
- Removed the dark mode color token block
- `theme.css` is now a small override stub — all brand tokens live
  in `Base.astro`'s global `<style>` block

### 2. Brand tokens baked into Base

`Base.astro` now declares every brand value once:

- `--color-navy #12263a`, `--color-blue #354ff5`, `--color-magenta #d220ff`
- `--gradient-cta` (blue → purple → magenta, 135deg)
- `--gradient-hero` (dark navy → royal electric blue, with radial
  highlights in the top-right and bottom-left). Re-tuned from deep
  navy to match the brighter royal blue the live adeliarisk.com
  hero actually uses.
- Roboto + Roboto Slab stack, full type scale, spacing, shadows, and
  a global `.ar-btn` button system with `primary`, `ghost`, `outline`
  variants
- `.ar-container`, `.ar-container-narrow`, `.ar-section`,
  `.ar-section-dark`, `.ar-section-tinted`, `.ar-eyebrow` utility
  classes so individual components don't need to reinvent these

### 3. Canonical RIA services page

`src/pages/pages/ria-cybersecurity-services.astro` is a hand-built
static Astro page that overrides `src/pages/pages/[slug].astro` for
this specific slug. Every section from the live page is represented,
with verbatim copy:

| Section | Component | Source assets used |
|---|---|---|
| Hero | `close/Hero.astro` | none (gradient + grid pattern) |
| Intro paragraph | inline | — |
| Testimonials | `close/TestimonialCarousel.astro` | — |
| "You're Not a Cybersecurity Expert" (4 objection cards) | `IconFeatureGrid` | — |
| "You Have a Target on Your Back" | `close/TargetSection.astro` | `ChatGPT-Image-Jun-16-2025-10_41_43-AM-1.jpg` |
| "You're Easier to Attack Than Banks" | `close/ComparisonBlock.astro` | — |
| "The SEC Is Watching" | `close/SecSection.astro` | — |
| "IT Support and Cybersecurity Are Different Jobs" | `close/ComparisonBlock.astro` (dark) | — |
| "Why Wealth Management Firms Choose Adelia" (4 reasons) | `IconFeatureGrid` | `vciso-ria-security-model.svg`, `we-work-with-your-team.svg`, `Audit-Ready-Documentation.svg`, `Group-1-1.png` |
| "How Our Virtual CISOs Work With RIA Clients" | `close/VideoEmbed.astro` | YouTube `-Ich21VoCTo` |
| "Maybe you need more from a Virtual CISO?" (20 extras) | `close/FeatureGrid.astro` | — |
| "Proven Cybersecurity Results" (3 stats) | `close/StatsRow.astro` | `ria-sec-compliance-improvement-chart.png`, `ria-cybersecurity-vulnerability-reduction.png`, `ria-employee-phishing-test-results-1024x910.png` |
| "RIA Cybersecurity That Grows With You" (pricing CTA) | `close/CtaBand.astro` | — |
| "RIA Cybersecurity Questions Answered" (10 FAQs) | `close/FAQAccordion.astro` | — |
| "RIA Cybersecurity Services Nationwide" | `close/CityList.astro` | `map-new.jpg` |
| "Protect Your Wealth Management Firm" (final CTA) | `close/CtaBand.astro` | — |

The real chart images, illustrated SVG icons, target photo, and USA
map are all hot-linked directly from `adeliarisk.com` so they render
identically to the source. Text is verbatim — every objection card,
every FAQ answer, every stat description, every city.

### 4. Blog post template rebuilt

`src/pages/posts/[slug].astro` now renders in close-variant style
that generalizes to all future posts:

- Dark gradient hero with breadcrumb (Home / Cybersecurity / Post
  title), large centered title, and a comma-separated uppercase tag
  list under the title — matches the source layout exactly. No
  subtitle or meta pill in the hero.
- Article body starts directly below the hero with an inline meta
  row: avatar + author name + title on the left, date · reading time
  on the right, separated by a hairline.
- Sticky right-side TOC matching the source treatment.
- Article typography: navy H2s at ~32px, navy H3s, `first-of-type` p
  rendered larger for a "lead paragraph" feel, blue inline links
  with subtle underline, square-checkbox bullet markers (not round
  dots) to match source, blue numbered list with circular counters,
  blue-bar blockquotes, dark pre blocks.
- Form-intro headings in the Portable Text stream are auto-detected
  and swapped for a styled `ChecklistCta` card (dark navy background
  with gold icon and gold submit button — matches the source form
  styling, but the form is non-functional and labeled "demo only").
- Share buttons (LinkedIn / X / Email) below the article.
- "More from the blog" grid at the bottom.

### 5. Blog post metadata override system

Because EmDash CLI doesn't expose byline creation or taxonomy
attachment for existing entries, and because the migrated post's
`publishedAt` got set to import date not original date, I added a
slug-keyed override map in `[slug].astro`:

```ts
const METADATA_OVERRIDES = {
  "how-online-banking-security-protects-your-accounts": {
    publishDate: "February 20, 2026",
    author: "Josh Ablett",
    authorTitle: "Founder & CISO",
    authorAvatar: "/_emdash/api/media/file/01KNNC9M01ZCVQ99DPGJCRHC55.webp",
    tagLabels: ["Checklist", "MFA", "Phishing", "SMB Security", "Wire Fraud"],
    category: { slug: "cybersecurity", label: "Cybersecurity" },
  },
};
```

So the banking post renders with your real headshot (uploaded to
R2 during the run), your real title, the original publish date,
the actual tag list, and the Cybersecurity category in the
breadcrumb — without touching the underlying D1 content. Future
posts you add normally through the admin will render with whatever
metadata EmDash stores; the override is only for migrated content.

### 6. New components built this session

Under `src/components/close/`, all using global brand tokens:

| Component | What it is |
|---|---|
| `Hero.astro` | Dark gradient hero with optional image, centered or left alignment, 1-2 CTAs. Grid pattern background. |
| `IconFeatureGrid.astro` | Feature card grid (2 or 3 columns). Supports inline SVG icons, image icons, or no icon. Has `tinted` and `dark` variants. |
| `AlternatingRow.astro` | Image + text row with optional reverse and tinted variants. (Not used on the RIA page but kept for future.) |
| `StatsRow.astro` | Before → after stat cards with optional chart image, dark or light background. |
| `CtaBand.astro` | Full-width dark gradient CTA banner. |
| `TestimonialCarousel.astro` | Horizontal scroll-snap carousel with dot indicators and 5-star ratings. |
| `ComparisonBlock.astro` | Two-column table comparison inside a single rounded container with header row and hairline-separated rows. Light or dark. |
| `TargetSection.astro` | Light section with a photo on the left and a tinted list card on the right. |
| `SecSection.astro` | Light section with a tinted copy card on the left and a white list card on the right. |
| `FAQAccordion.astro` | Native `<details>/<summary>` accordion with blue chevron, no JS. |
| `FeatureGrid.astro` | 3- or 4-column pill grid for simple feature/tag lists. |
| `VideoEmbed.astro` | YouTube thumbnail + play button that lazy-loads the iframe on click. No autoplay, no cookies (`youtube-nocookie.com`). |
| `CityList.astro` | Pill list with optional map image above. |
| `ChecklistCta.astro` | Dark navy + gold form CTA for the blog post's form replacements. Non-functional demo form. |

### 7. Content fixes in D1

- Removed the leftover empty `Ready to Implement These Controls?` H2
  from the banking post content (this was the form heading that I
  stripped during migration and forgot to delete the heading for).
  Post is now at version 4 in D1.

### 8. Mobile responsive hardening

- `body { overflow-x: hidden }` so any child overflow doesn't break
  the viewport
- `overflow-wrap: break-word` on all headings globally
- Hero title clamp floor lowered to 1.5rem, post hero title to
  1.5rem, feature grid title to 1.375rem
- Explicit media query font-size overrides at 540px and 420px for
  the hero title as a safety net

## What I can still see that's not perfect

Honest list of differences still visible when scrolling the live
pages next to adeliarisk.com:

1. **Hero padding is slightly tighter than source.** The live hero
   has a bit more vertical breathing room above and below the CTA.
   Mine could use another ~20px top-bottom padding.
2. **The header/nav shape is different.** Source uses a floating
   rounded pill container for the header (logo + nav + phone inside
   one rounded white pill floating over the hero). Mine has a flat
   white bar. I didn't redesign the header for this pass — it's the
   same SiteHeader from earlier work.
3. **No homepage.** The root URL still shows the EmDash blog index
   with my single banking post in it. The real adeliarisk.com home
   is a completely different hero + services grid + testimonials +
   founder block + "latest posts" layout. I didn't build a homepage
   for this pass because it wasn't in scope.
4. **RIA hero image missing.** Source RIA hero doesn't have an image,
   but if you look at the live page's Elementor markup you'll see
   that the original hero was paired with a `CleanShot.png` chart on
   the right at some point. Mine has none (centered title only),
   which actually matches the current live source.
5. **Testimonial carousel is horizontal scroll + dots; source uses
   swiper with fade + arrows.** Same conceptual pattern, different
   library. My implementation is JS-light (no dependency), which
   keeps the Lighthouse score up.
6. **"Maybe you need more" extras grid.** I show all 20 items in a
   4-column grid. Source shows them in a slightly different grid
   density with Elementor's styling. Mine is cleaner but denser.
7. **Stats card chart images** display in a white band at the top of
   each card. The source has the charts as the card background with
   some extra labels. Good enough but not a pixel-match.
8. **Blog post body typography spacing.** Source has slightly
   different margin rhythm between H2/H3 and paragraphs. Mine feels
   a touch tighter. Small thing.
9. **TTFB is still ~500ms** on HTML responses because EmDash
   middleware doesn't emit Cache-Control on server-rendered pages.
   That's the same pre-existing issue I flagged after your 61-score
   PageSpeed screenshot — not something I touched this session.
   Lighthouse score should still be in the low-to-mid 80s on mobile
   thanks to the image/font/CSS fixes from earlier.

## What's new in the repo

```
src/components/close/
  AlternatingRow.astro     (updated)
  ChecklistCta.astro       (new — blog form replacement)
  CityList.astro           (new — nationwide cities with map)
  ComparisonBlock.astro    (rewritten — table-style)
  CtaBand.astro            (updated — grid pattern)
  FAQAccordion.astro       (new — native details/summary)
  FeatureGrid.astro        (new — extras pill grid)
  Hero.astro               (updated — brighter gradient, grid pattern)
  IconFeatureGrid.astro    (updated — columns prop, optional icons, dark variant)
  SecSection.astro         (new — SEC Is Watching layout)
  StatsRow.astro           (updated — supports chart images)
  TargetSection.astro      (rewritten — photo + list card layout)
  TestimonialCarousel.astro (new — scroll snap + dots + stars)
  VideoEmbed.astro         (new — lazy YouTube)

src/pages/pages/
  ria-cybersecurity-services.astro  (new — hand-built canonical page)

src/pages/posts/[slug].astro  (rewritten for close-variant article layout)

src/layouts/Base.astro         (rewritten, light-only, brand tokens, utility classes)
src/components/SiteFooter.astro (theme switcher removed)
src/styles/theme.css            (stripped to override stub)
src/styles/variant-close.css    (deleted — folded into Base.astro)
src/pages/preview/              (deleted — canonical URL has the content now)

MORNING-REPORT.md               (this file)
```

## What I did NOT do

- Did not touch the homepage at `/`
- Did not touch the `/posts` archive, `/category/*`, `/tag/*`, `/search`,
  `/404`, or the RSS feed — they still use the old EmDash template
- Did not attach taxonomy terms to the post entry in D1 (CLI
  limitation — used the override map instead)
- Did not create a real byline in EmDash for Josh Ablett (same
  limitation — used override map)
- Did not fix the TTFB / Cache-Control issue
- Did not hook up the ChecklistCta form to MailerLite or anything
  else — it's labeled as demo
- Did not update package.json, wrangler.jsonc, or astro.config.mjs
- Did not run full Lighthouse comparison against the pre-session score

## What you could ask me to do next

In rough order of impact:

1. **Homepage**: build a real Adelia Risk–style homepage to match
   the root URL. Hero + Trusted by logos + What We Offer cards +
   Clients We Typically Serve alternating rows + About Josh + Latest
   posts + Final CTA band. Uses most of the components I already built.
2. **Header**: rebuild SiteHeader as the floating rounded pill shape
   the source uses. Add a real mobile menu drawer.
3. **Finish the post polish**: tweak body typography spacing to
   match source margins exactly, fix the first-paragraph "lead"
   treatment, make the form CTAs actually submit to MailerLite.
4. **Other migrated content**: migrate a few more pages (About,
   Virtual CISO Service, CMMC Consulting) so the nav links go
   somewhere real.
5. **TTFB fix**: dig into EmDash middleware to understand why
   `Astro.cache.set(cacheHint)` isn't producing Cache-Control
   headers on the response. If fixable, this would drop TTFB from
   ~500ms to ~20ms on warm edge and meaningfully improve Lighthouse.

---

## Git history

```
408bba2 Remove extra eyebrow labels and simplify SEC section layout
fa80bae Mobile responsive fixes and body overflow safety
6e699c2 Rework post hero and refine gradient/pattern across dark sections
ced3405 Visual polish pass: match source section backgrounds and comparison layout
1dfad10 Full theme pass: close-variant canonical pages, light-only, real assets
```

Everything pushed to `main` on `origin`. Built and deployed to the
production Worker at each step. The current live version is
`408bba2`.

Sleep was not, in fact, required.
