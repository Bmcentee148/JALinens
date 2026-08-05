# J&A Linen Service — Build Notes

Benchwork Digital · first pass, August 5, 2026

## Files

```
site/
├── index.html              the whole site (CSS inlined, single file)
├── thank-you.html          form success page
├── robots.txt
├── sitemap.xml
├── favicon.ico
└── assets/img/
    ├── ja-logo.webp        720×261, cleaned + trimmed from JALogo.png
    ├── ja-shield-512.png   schema.org logo (PNG — Google prefers it)
    ├── apple-touch-icon.png  iOS home screen (PNG — iOS won't take WebP)
    ├── og-image.jpg        1200×630 social share card (see below)
    └── photos/             7 photos × 2–3 widths each, all WebP
```

Open `index.html` in a browser to review. No build step, no dependencies except Google Fonts.

## Page order

1. Utility bar — location, hours, emergency service
2. Header — logo, nav, call button (sticky). Below 900px the nav and call
   button are replaced by a Menu button that opens a slide-in panel.
3. Hero — truck photo, "Long Island Runs With J&A", 1958 badge, call CTA
4. Trust strip — 1958 / 4 generations / 100s of businesses / 24hr
5. What We Supply — stockroom photo + six product categories
6. How It Works — three steps
7. Inside Our Plant — three photos (washers, plant floor, finished goods)
8. Who We Serve — six business types
9. Why J&A — truck photo + six differentiators
10. Testimonials — **placeholder, must be replaced**
11. Contact — building sign, call box, hours, address, map, quote form
12. Footer
13. Sticky call bar (mobile only)

## Photos

All seven supplied photos are in use. Originals stayed untouched in `../images/`.

| File | Where it's used | Source |
|---|---|---|
| `ja-truck-waterfront` | Hero background | unnamed.jpg |
| `linen-stock-aisle` | What We Supply, portrait feature | unnamed-2.jpg |
| `industrial-washers` | Inside Our Plant, left | unnamed-3.jpg |
| `plant-floor` | Inside Our Plant, center | unnamed-7.jpg |
| `finished-linens` | Inside Our Plant, right | unnamed-6.jpg |
| `ja-truck-side` | Why J&A, feature | unnamed-4.jpg |
| `ja-building-sign` | Contact, above the call box | unnamed-5.jpg |

**Optimization.** WebP at quality 74, `method=6`. Each photo generated at 2–3
widths and served via `srcset`/`sizes` so phones download the small variant.
Hero is `fetchpriority="high"`, everything below the fold is `loading="lazy"`.
Every image carries explicit `width`/`height` to prevent layout shift.

Result: **2.0 MB of JPEGs → 564 KB total desktop page weight**, and roughly
300 KB on a phone. Nothing is upscaled beyond its source resolution.

To re-run after adding photos, the conversion settings are: crop to the target
aspect with centering `(0.5, 0.45)`, resize with LANCZOS, save WebP q=74 method=6.

### Social share card

`og-image.jpg` is 1200×630, built from the waterfront truck shot with a dark
bar across the bottom: **J&A LINEN SERVICE** on the left, **LONG ISLAND SINCE
1958** on the right, red hairline on top of the bar, and a soft gradient
lead-in so the bar doesn't read as an accident.

The crop is centred at `(0.5, 0.62)` rather than `0.45`. That lifts the truck
in the frame so the bar lands on empty road instead of slicing through the
wheels — worth knowing if the image is ever regenerated.

The bar is set in **Nimbus Sans Narrow Bold**, not Oswald. Oswald wasn't
available where this was generated, and the condensed grotesque is arguably a
closer match to the lettering on the trucks anyway. If it's ever rebuilt in
Oswald, keep the sizes (54px / 25px) and the 56px side margins.

Note the truck wrap says "Over 50 Years" while the site says 1958 and four
generations. Both are true, the wrap is just older. Nobody will notice at
share-card size, but John might.

### Note on the layout change

The original design had a photo thumbnail on each of the six product cards
(Aprons & Towels, Chef Wear, Table Linens, Rugs & Mats, Event Rentals, Shelf
Restocking). None of the supplied photos actually show those things
individually — they're trucks, the plant, and the stockroom. Rather than
mislabel a plant-floor shot as "Rugs & Mats," the product cards are now clean
text cards, and the real photos got bigger, more prominent placements where the
caption matches what's actually in the frame.

If J&A later shoots true product close-ups, we can put thumbnails back on the
cards. Worth asking for: chef coats on a rack, folded bar towels, an entrance
mat, a catering table set, and a driver restocking a customer's shelf.

**Testimonials.** Three placeholder quotes are marked with a yellow "Placeholder"
tag. Real quotes exist on Yelp and Google; get J&A's OK before publishing any
customer name. Delete the `<span class="placeholder-tag">` lines once replaced.
If they'd rather not use testimonials at all, delete the whole section — the trust
strip already carries that weight.

## Facts to confirm with John

- [ ] "100s of local businesses" — is there a real number they'd rather use?
- [ ] 24-hour emergency service — still offered? How is it reached after 5pm?
- [ ] Service area — "Manhattan to Montauk" came from their own materials
- [ ] Email — `Jaapron@hotmail.com` is what's on Instagram. A domain address
      (john@jaapron.com) would look sharper and is free to set up.
- [ ] Business name for the footer/legal line: "J&A Coat, Apron & Linen Service"
- [ ] "No contracts" claim — reviews say it, but confirm before we put it in the hero

Note: the `geo` coordinates were removed from the schema markup rather than
shipped as a guess. Google geocodes from the street address, so nothing is lost.
If we want a precise pin later, pull the real lat/long off the Google Business
Profile and add the `geo` block back.

## Technical

**Form.** Wired for Netlify Forms (`data-netlify="true"`) with a honeypot field for
spam. Works automatically on Netlify deploy — no config. Set up an email
notification under Site settings → Forms → Form notifications so submissions
actually reach them. Posts to `/thank-you.html` on success.

**Map — removed on purpose.** There's no embedded map. The call box has a
**Get Directions** button that deep-links to Google Maps (or Apple Maps on
iPhone). That's a plain link — no iframe, no third-party script, no cookies.

Why it's gone:

- An embedded Google Map sets third-party cookies (`NID` and friends) and phones
  home to Google on every page load, before the visitor has done anything.
- In the EU that legally requires a consent banner before the map loads. J&A
  serves Long Island, so the real-world exposure is close to zero — but a
  consent banner is a conversion killer, and this avoids ever needing one.
- It also drops ~1 MB of third-party JavaScript and several hundred ms off load.
- The unofficial `google.com/maps?output=embed` URL is blocked from framing by
  Google, which is what produced the blank grey box in the first pass. The
  official Maps Embed API works, but needs an API key and a Cloud project.

A map earns its place when a visitor needs to *find* an unfamiliar storefront.
J&A's customers are booking a delivery route, not driving over. The directions
button covers the rare case.

### Putting the map back

1. Get a key: `console.cloud.google.com` → enable **Maps Embed API** (free, no
   usage cap on this API) → Credentials → Create API key → restrict it under
   Application restrictions → HTTP referrers → `*.jaapron.com/*`

2. Paste this CSS just above the `/* ---------- Directions link ---------- */`
   comment in the `<style>` block:

```css
.map{
  margin-top:1.4rem;border:1px solid var(--line);border-radius:4px;
  overflow:hidden;aspect-ratio:16/9;background:var(--paper-2);
}
.map iframe{width:100%;height:100%;border:0;display:block}
```

3. Paste this markup where the `<!-- Map removed for now -->` comment sits in
   `index.html`, replacing `YOUR_KEY_HERE`:

```html
<div class="map">
  <iframe
    title="Map showing J&amp;A Linen Service at 56-58 Penataquit Avenue, Bay Shore, New York"
    src="https://www.google.com/maps/embed/v1/place?key=YOUR_KEY_HERE&amp;q=56-58%20Penataquit%20Ave%2C%20Bay%20Shore%2C%20NY%2011706&amp;zoom=16"
    loading="lazy" referrerpolicy="no-referrer-when-downgrade" allowfullscreen></iframe>
</div>
```

Keep the Get Directions button either way — it's the part people actually tap.

If the map goes back and J&A ever markets to EU visitors, they'd need a consent
banner. Not a concern for a Long Island route business.

**Domain.** They own jaapron.com. Canonical URL, sitemap and schema all point at
`https://www.jaapron.com/`. If we deploy to a different host, point the existing
domain at Netlify and the URLs stay correct. The old site there is a multi-page
HTML site that currently returns empty pages.

Redirects for the old page URLs are already set up in `netlify.toml` — each one
points at the matching section of the one-pager rather than just the homepage.

## Git & deploy

**This folder is the repo root.** Netlify publishes it directly, which means
anything committed is reachable at `jaapron.com/<path>`. Before adding a file,
ask whether a stranger opening it would be a problem.

Set up:

```bash
cd site
git init
git add .
git commit -m "Initial site"
git branch -M main
git remote add origin <repo-url>
git push -u origin main
```

Then in Netlify: **Add new site → Import an existing project**, pick the repo,
and leave *Base directory* and *Build command* empty — `netlify.toml` sets the
publish directory. Every push to `main` deploys.

`netlify.toml` handles:

- Publish directory (repo root, no build step)
- 301s from the twelve old jaapron.com URLs to the right sections
- `NOTES.md` returns 404 — it's committed for history but not readable publicly.
  It 404s rather than redirecting, so we don't advertise that it exists.
- Long-lived immutable cache on `/assets/img/*` (filenames carry the width, so a
  changed image is a changed filename), HTML set to always revalidate
- `nosniff`, `SAMEORIGIN`, referrer and permissions policy headers

`.gitignore` keeps out OS junk and two superseded files that would otherwise ship
168 KB of dead weight: `assets/img/ja-logo.png` (replaced by the WebP) and
`assets/img/og-image.webp` (social platforms want the JPEG). Both stay on disk
locally as editable masters.

Committed total is about **1.1 MB**.

There's also a branded `404.html`, which Netlify serves automatically for unknown
paths. It keeps the phone number on it.

**SEO in place.** Title and meta description, semantic H1–H3, LocalBusiness schema
with full NAP + hours + service catalog + areaServed, canonical, Open Graph,
sitemap.xml, robots.txt, descriptive alt text, explicit image dimensions.

**Still to do at launch:**
- [ ] Submit sitemap in Google Search Console
- [ ] Claim/verify the Google Business Profile and match NAP exactly
- [ ] Confirm NAP consistency on Yelp and BBB against the site
- [ ] Delete the unused `assets/img/ja-logo.png` (superseded by the WebP)

**Mobile menu.** Breakpoint is 1140px, not the usual ~900. Anton's companion UI
face (Source Sans 3) is normal-width where the old display face was condensed,
so the same six nav labels take noticeably more room — at 900px they wrapped to
two lines and doubled the header height. Two fixes: `white-space:nowrap` on nav
links and buttons so they can never wrap, and the handover to the hamburger
moved up to 1100px. The nav label for the first section is also shortened to
"Services" while the section heading still reads "What We Supply".

Below 1100px the desktop nav is replaced by a Menu button opening a slide-in
panel from the right: the six nav links, then the call button pinned at the
bottom with hours under it.

The header call button only disappears below **760px**, where the sticky bottom
call bar takes over. Between 760 and 1100 the nav is gone, so there's room to
keep the number visible in the header.

Nav type is `clamp(.85rem, .471rem + .569vw, .97rem)` — 13.6px right at the
handover, scaling to 15.5px from about 1240px up, where the 1180px wrap stops
growing. The header call button tracks it at .92rem so it doesn't read small
beside a larger nav.

If nav labels are ever lengthened, re-check the header at exactly 1141px — that's
the tightest it gets. Current estimate is about 1000px of content in 1061px of
space, so roughly 60px of slack. The budget at that width caps the nav at about
15.6px, which is why the clamp minimum sits at 13.6px rather than higher.

It closes on tap-outside, on Escape, on the X, and when a link is tapped (so the
anchor scroll is actually visible). While open, focus is trapped inside the
panel, background scroll is locked, and the panel is marked `inert`. Focus
returns to the Menu button on close. Resizing back to desktop closes it cleanly.

Phone number now appears 7 times: header (desktop), hero, mobile menu, call box,
under the form, footer, and the sticky mobile call bar.

**Accessibility.** Skip link, visible focus rings, labelled form fields,
decorative SVGs hidden from screen readers, `prefers-reduced-motion` respected,
and the mobile menu is keyboard-operable end to end.

## Brand

Sampled from the logo:

- Blue `#1349A5` · Red `#C1090D` · Ink `#111214` · Paper `#F7F5F1`

Three type roles, two families:

| Var | Family | Used for |
|---|---|---|
| `--display` | Anton 400 | h1–h4 and the big numerals (trust strip, step numbers, phone number, quote mark) |
| `--ui` | Source Sans 3 600 | small uppercase UI — nav, buttons, eyebrows, form labels, badges |
| `--body` | Source Sans 3 400 | paragraphs |

**Anton ships one weight only (400).** Never set `font-weight` above 400 on
anything using `--display` — the browser fakes it by smearing the glyphs and it
looks broken at large sizes. Every display rule is pinned to 400.

Anton is display-only for a reason: at 13px with wide tracking it turns into a
chunky mess, so all the small uppercase UI runs in Source Sans 3 instead. That
split is what keeps the headings loud and the interface readable.

The type scale was reduced when Anton replaced Oswald — Anton carries far more
weight per pixel, so h1 went from 4.35rem to 3.7rem max and the rest scaled to
match. Tracking opened from `.005em` to `.015em`; heavy caps close up without it.

Note the social share card (`og-image.jpg`) is set in Nimbus Sans Narrow Bold,
which is lighter than Anton. If it's ever rebuilt, Anton would match better.
