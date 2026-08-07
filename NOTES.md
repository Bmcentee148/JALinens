# J&A Linen Service — Build Notes

Benchwork Digital · first pass, August 5, 2026

## Files

```
JA_Linens/                  repo root — nothing here is published
├── netlify.toml            publish = "site"
├── .gitignore              keeps source material out of the repo
├── NOTES.md                this file
├── images/                 original photography (ignored)
├── *.png                   master logos + screenshots (ignored)
│
└── site/                   THE DEPLOY — served at the domain root
    ├── .gitignore
    ├── index.html          the whole site (CSS inlined, single file)
    ├── thank-you.html      form success page
    ├── 404.html
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

Open `site/index.html` in a browser to review. No build step, no dependencies
except Google Fonts.

The split matters: anything inside `site/` is live at `jaapron.com/<path>`.
Everything above it — including these notes — stays private.

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

## Testimonials

Three real Google reviews, all 5-star. Source screenshots are in `reviews/`
(root level, not deployed).

| Shown as | Reviewer | Age | Angle |
|---|---|---|---|
| Anthony D. | Anthony Dagostino (Local Guide, 494 reviews) | ~5 yrs | Switched suppliers, no hidden costs, owner's cell |
| Matt | Matt (6 reviews) | ~8 yrs | Reliability, names John and Jonathan |
| Shauna B. | Shauna Bednar (4 reviews) | ~9 mths | Short and warm, about the staff |

Editorial decisions, all reversible:

- **Cut the competitor's name.** Anthony's review says he replaced "Sintas"
  (Cintas, misspelled) with J&A. Naming a national competitor on a client site
  invites trouble and adds nothing, so that sentence is omitted. The "no hidden
  costs, contracts" point survives it.
- **"BS" is still in there**, verbatim. It's authentic and it lands. If John
  would rather it weren't on his website, cut to "No hidden costs, contracts…"
- **Omissions are marked with `…`**, and no word is altered. Only exception:
  "HIGHLY RECOMMENDED!!" set as "Highly recommended!" so it isn't shouting in
  all-caps next to Anton headlines.
- **Surnames reduced to an initial** — public reviews, but first-name-plus-initial
  is the kinder convention. Matt has no surname on Google.
- **No job titles or towns.** The placeholders invented "Restaurant Owner ·
  Town, NY"; Google doesn't tell us either, so the attribution is just the name
  and "Google review".
- **Dates omitted.** Two of the three are 5 and 8 years old. True, but "8 years
  ago" under a testimonial reads as neglect rather than longevity.

**Worth confirming with John** that he's happy to have these on the site, even
though they're already public.

### Review schema — deliberately not added

No `aggregateRating` or `review` markup in the JSON-LD. Google's structured-data
policy restricts self-serving review markup for `LocalBusiness`, and getting it
wrong risks a manual action. The stars in the testimonial cards are presentational
only. If they want star ratings in search results, the route is the Google
Business Profile, not markup on their own site.

## Facts to confirm with John

- [ ] "100s of local businesses" — is there a real number they'd rather use?
- [ ] **Emergency service — what can they actually commit to?** The site now says
      "emergency service available" with no timeframe attached. If John confirms a
      real one, these are the exact strings to change:
      utility bar and mobile menu `Emergency service available`,
      hours table `Just call`, trust strip `Rush` / `Orders when you're stuck`,
      and the Why J&A heading `Emergency Service`.
- [ ] "Same driver, same day each week" (How It Works, step 3) — plausible for a
      route business but nobody told us this. Confirm or soften.
- [ ] Service area — "Manhattan to Montauk" came from their own materials
- [ ] Email — `Jaapron@hotmail.com` is what's on Instagram. A domain address
      (john@jaapron.com) would look sharper and is free to set up.
- [ ] Business name for the footer/legal line: "J&A Coat, Apron & Linen Service"
- [ ] "No contracts" claim — reviews say it, but confirm before we put it in the hero

### Where "24-hour" came from, and why it's gone

The claim traced back to a **filename on their old site** — `jaapron.com/24houremergency.html`.
That page is real and indexed, but its title is just "Emergency Services", the
Google/Yelp description says only "with our emergency services, you'll never be
left out to dry", and the page body was never retrievable (jaapron.com returns
empty). A URL slug on an undated page isn't a service commitment, and it was
sitting in four places including the bar above every screen, right next to
"Mon–Fri 8:00am–5:00pm".

Removed everywhere. Emergency service is still on the site in four places, just
without a clock attached. "Same day" was considered and rejected for the same
reason — it's an unverified timing promise, only a smaller one.

The `/24houremergency.html` redirect in `netlify.toml` stays. That's a real URL
of theirs and should still land somewhere sensible.

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

Repo: `github.com/Bmcentee148/JALinens`, branch `main`.

Layout — **`netlify.toml` must sit at the repo root**, not in `site/`. Netlify
only reads that file from the repo root (or from a configured base directory);
it will never find one in a subfolder.

```
JA_Linens/            <- repo root
├── netlify.toml      <- publish = "site"
├── .gitignore        <- keeps source material out
├── images/           (ignored)
├── *.png             (ignored — master logos, screenshots)
└── site/             <- THE DEPLOY, published at the domain root
    ├── .gitignore
    ├── index.html
    └── ...
```

Anything committed inside `site/` is publicly reachable at `jaapron.com/<path>`.
Before adding a file there, ask whether a stranger opening it is a problem.

In Netlify, leave **Base directory** and **Build command** empty. `netlify.toml`
handles it. Don't set a base directory — with `publish = "site"` already relative
to the repo root, setting base to `site` as well would resolve to `site/site`.

Every push to `main` deploys.

`netlify.toml` handles:

- Publish directory (`site`, no build step)
- 301s from the twelve old jaapron.com URLs to the right sections
- Long-lived immutable cache on `/assets/img/*` (filenames carry the width, so a
  changed image is a changed filename), HTML set to always revalidate
- `nosniff`, `SAMEORIGIN`, referrer and permissions policy headers

Two `.gitignore` files, doing different jobs:

- **Root** — keeps `images/` and the loose master PNGs out of the repo entirely.
  The `/` prefix on those rules scopes them to the root so they don't touch
  `site/assets/img/*.png`.
- **`site/`** — keeps OS junk and two superseded files out of the deploy:
  `assets/img/ja-logo.png` (replaced by the WebP) and `assets/img/og-image.webp`
  (social platforms want the JPEG). 168 KB of dead weight. Both stay on disk
  locally as editable masters.

Committed total is about **1.1 MB**.

There's also a branded `404.html`, which Netlify serves automatically for unknown
paths. It keeps the phone number on it.

**SEO in place.** Title and meta description, semantic H1–H3, LocalBusiness schema
with full NAP + hours + service catalog + areaServed, canonical, Open Graph,
sitemap.xml, robots.txt, descriptive alt text, explicit image dimensions.

### Absolute URLs — the one thing to change at launch

The site is currently live at **`https://jaaprons.netlify.app`**, and every
absolute URL points there. When jaapron.com is switched over, search-and-replace
that string across four files:

```bash
cd site
grep -rl "jaaprons.netlify.app" . | xargs sed -i '' 's|https://jaaprons.netlify.app|https://www.jaapron.com|g'
grep -rn "netlify.app" .   # should come back empty
```

That covers `index.html` (canonical, og:url, og:image ×3, twitter:image, and
four spots in the JSON-LD), `thank-you.html`, `sitemap.xml` and `robots.txt`.

**Why it matters:** og:image has to be absolute *and* actually resolve. It
originally pointed at jaapron.com while the site was on Netlify, so iOS tried
to fetch an image from a domain that doesn't serve this site, failed, and fell
back to scraping the page — which is why sharing it produced the stockroom
photo instead of the truck.

Pick www or apex to match whatever Netlify is set to as primary, and make sure
the other one redirects. A mismatch breaks this again in exactly the same way.

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
call bar takes over. Between 760 and 1140 the nav is gone, so there's room to
keep the number visible in the header.

**Sticky call bar (mobile, below 760px).** It doesn't sit there the whole time.
Two IntersectionObservers watch the hero and the contact section + footer, and
the bar only shows in between — it slides up once the hero scrolls away and
slides back down when the contact section arrives. Those are the two places the
phone number is already large on screen, so an always-on bar was redundant there
while permanently costing 58px of a small screen.

Progressive enhancement: the bar is plain visible in CSS, and the script adds
`.is-managed` to take over. With JS off, or on a browser without
IntersectionObserver, it just stays visible like before. While hidden it's
`visibility:hidden`, so it isn't keyboard-focusable.

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

Tracking is `.015em` rather than `.005em` — heavy caps close up without it.

### Type scale

At the 18px body maximum:

| Level | 390px (phone) | 1440px (desktop) |
|---|---|---|
| h1 | 45.2px · 2.74× body | 64.8px · 3.60× |
| h2 | 34.9px · 2.11× | 44.8px · 2.49× |
| h3 | 22.7px · 1.38× | 24.8px · 1.38× |
| body | 16.5px | 18px |

**Every clamp uses a two-term preferred value — `rem + vw`, not bare `vw`.**
This matters more than it looks. `clamp(2.25rem, 5.7vw, 4.05rem)` never clears
its own minimum until about a 630px viewport, so every phone from a 320px SE to
a 430px Pro Max rendered the identical 36px headline. The rem offset makes the
type start scaling immediately. If you add a heading, copy this pattern.

### Hero headline

The break points are **explicit**, not left to natural wrapping:

```html
<h1><span>Long Island</span> <span>Runs&nbsp;With</span> <em>J&amp;A.</em></h1>
```

with `.hero h1 span,.hero h1 em{display:block}`. It stacks the same at every
width — LONG ISLAND / RUNS WITH / J&A.

Natural wrapping didn't work. Anton is narrower than it looks, so at phone
widths "LONG ISLAND RUNS WITH" fit on one line and left "J&A." orphaned
underneath, and the break point shifted as the font size changed. Chasing it
with a `max-width:14ch` cap only worked at some sizes. Three blocks is stable.

Because the longest line is now "LONG ISLAND" rather than the full 21-character
phrase, there's much more room, which is why h1 sits at ~51px on a 390px phone
(3.1× body). Keep the `&nbsp;` — it stops "Runs With" splitting if that line
ever needs to wrap. Screen readers still read it as one sentence.

**Anton is heavy but condensed, so it needs *more* size than a normal-width face
to carry the same presence, not less.** The first pass got this backwards — the
scale was cut ~15% on the reasoning that Anton is heavier, which left h3 at
1.17× body and the "Why J&A" headings at 1.03×, indistinguishable from body
copy. Headline presence is driven by width, and Anton is narrow.

If a heading ever needs its own size, keep it at **1.25× body or above** or it
stops reading as a heading. The two overrides that exist (`.why__item h3` at
1.29×, `.callbox h3` at 1.33×) sit just under the base on purpose, because
they're in narrower columns.

Note the social share card (`og-image.jpg`) is set in Nimbus Sans Narrow Bold,
which is lighter than Anton. If it's ever rebuilt, Anton would match better.
