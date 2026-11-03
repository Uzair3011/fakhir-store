# Fakhir Outwears — Lahore Local SEO: Implementation Report

**Site:** https://fakhir-store.vercel.app/ · **Market:** Lahore, Punjab, Pakistan only
**Date:** 2 November 2026 · **Prepared for:** Fakhir Outwears (Fakhir Store)

> **Deployment status — read first.** All changes below are implemented and verified in the
> code package `fakhir-store-seo-implementation.zip` (a full copy of the live site with the
> SEO work applied). The production Vercel project is **not** in the Vercel account connected
> here (that account holds other projects), and no public GitHub repo exists for the site, so
> the package could not be pushed live from this session. To go live: replace the project
> files with the package contents in the repo/folder that deploys `fakhir-store`, then deploy
> as usual. Note the original repo generates product pages with `scripts/build-pages.mjs` —
> port the product-page changes (meta descriptions, breadcrumb target, `priceValidUntil`,
> the new "About / Delivery in Lahore" section, footer) into that generator's template so a
> rebuild does not overwrite them. Every other file is static and can be copied as-is.

---

## 1. Audit findings (live site, 2 Nov 2026, before changes)

**What was already good** (kept, not rebuilt): unique keyword-led titles on all 7 pages,
canonical URLs, `lang="en-PK"`, Open Graph tags, Product schema with `priceCurrency: PKR`,
image sitemap basics, WebP images (~83–109 KB), and excellent lab performance
(PageSpeed mobile: Performance 99, Accessibility 96, Best Practices 100, LCP 1.9 s, CLS 0).

**Issues found:**

| # | Issue | Evidence |
|---|-------|----------|
| 1 | **No indexed presence found** | `site:fakhir-store.vercel.app` returned zero results |
| 2 | **No indexable category pages** | `/jackets/`, `/tracksuits/`, `/faq/` all 404; nav categories were `#shop` anchors filtered by JavaScript |
| 3 | **Homepage had no crawlable links to products** | Product grid is rendered by JS; static HTML links were only `#shop` anchors. Product pages were reachable only via the sitemap |
| 4 | **Duplicate LocalBusiness schema** | Two separate `ClothingStore` blocks with the same `@id` on the homepage (one hand-written, one generated with the reviews) |
| 5 | **Breadcrumb pointed at a fragment** | Product breadcrumbs/schema linked "Bomber jackets" to `/#shop`, which is not a page |
| 6 | **Product meta descriptions too long** | 173–207 characters (Google truncates ≈160); led with boilerplate instead of the buying keyword |
| 7 | **Thin product pages** | ~30 words of unique product copy per page; the rest was the same 5 reviews repeated on all 6 pages |
| 8 | **Misleading navigation** | "Tracksuits" and "Sale" nav items pointed to categories with no products (all 6 products are jackets; none are on sale) |
| 9 | **Wrong-currency placeholder** | Cart subtotal rendered "$0.00" on a PKR store |
| 10 | **No FAQ content or FAQ schema**, no `WebSite` schema, no `priceValidUntil` on offers | Missing rich-result and entity signals |
| 11 | **No custom 404 page** | Vercel's plain-text NOT_FOUND |
| 12 | **No cache/security headers** | `styles.css`/`app.js` served `max-age=0, must-revalidate`; no `vercel.json` at all |
| 13 | **Sitemap gaps** | 7 URLs only, no image titles, static `lastmod` |
| 14 | **Local entity gaps** | ClothingStore schema had city-level address only (correct — no street address exists to publish), but no Google Business Profile, citations or local links strategy in place |

## 2. What was implemented

### New indexable landing pages (the core fix)
| Page | Target keywords | Content |
|------|-----------------|---------|
| `/bomber-jackets/` | bomber jackets in Lahore · buy bomber jacket Lahore · bomber jacket price in Lahore | All 6 jackets with crawlable cards, style guide (faux-leather / jacquard / statement), price section, Lahore delivery areas, WhatsApp CTA |
| `/jackets/` | men's winter jackets in Lahore · jackets for men Lahore · winter jackets Lahore price | Full collection hub, "which jacket suits a Lahore winter" guide, sizing/ordering |
| `/varsity-jackets/` | varsity jacket in Lahore · varsity jackets for men Pakistan | Midnight Panel + Monochrome Block, styling guide, links to bomber hub |
| `/faq/` | jacket delivery in Lahore · bomber jacket size M or L · order jacket on WhatsApp Lahore | 7 real FAQs (price, sizes, areas incl. DHA/Gulberg/Johar Town/Model Town/Bahria Town/Wapda Town/Cantt, ordering, payment-confirmation) with **FAQPage schema** |

Each new page: unique title (57–63 chars) and meta description (145–153 chars), canonical,
Open Graph, `CollectionPage` + `ItemList` + `BreadcrumbList` schema, one H1, and static
product cards with descriptive image alt text (e.g. "Navy Python Jacquard Bomber in Navy —
men's jacket in Lahore").

### Homepage
- Merged the two duplicate `ClothingStore` blocks into **one `@graph`** (ClothingStore with
  its 5 real on-site reviews + aggregate rating, plus a new `WebSite` node) — single entity,
  valid JSON-LD.
- Header nav now links to real pages (`/jackets/`, `/bomber-jackets/`, `/faq/`); the empty
  "Tracksuits"/"Sale" promises are gone.
- Body copy in the "Bomber jackets in Lahore" section now links to the bomber, varsity,
  jackets and FAQ pages — the homepage finally passes crawlable internal links.
- Footer expanded sitewide: all category pages + all 6 product pages + WhatsApp number.
- Cart placeholder fixed: `$0.00` → `Rs 0`.

### Product pages (all 6)
- Meta descriptions rewritten to 136–153 chars, keyword-led (e.g. "Buy the Onyx black
  faux-leather bomber jacket in Lahore for Rs 3,000…").
- Added a unique ~150-word "About this jacket" + "Delivery in Lahore" section per product
  (built only from the product's real attributes) with internal links to the category page
  and FAQ — product pages went from ~30 unique words to a full, distinct page.
- Breadcrumb (visible + schema) now points to the real `/bomber-jackets/` page.
- Added `priceValidUntil: 2027-01-31` to every Offer.

### Technical
- **`vercel.json` added:** 30-day cache for images, 7-day cache for CSS/JS (was
  `max-age=0`), `X-Content-Type-Options: nosniff`, `Referrer-Policy`.
- **Custom `404.html`** (noindex, links back to the collection).
- **Sitemap rebuilt:** 11 URLs (was 7), image entries now carry `<image:title>`,
  `lastmod` updated.
- **robots.txt audited:** already correct (allow all + sitemap) — left unchanged.
- Nothing about design, branding, layout or store behaviour was changed; new pages reuse
  the site's own header, footer, card and button styles (one small CSS block appended).

### Verification (local build of the package)
All 11 sitemap URLs return 200 · sitemap XML valid · every JSON-LD block parses ·
one H1 per page · all titles 42–63 chars · all descriptions 136–153 chars ·
**zero broken internal links** · reviews and schema data identical to live (nothing fabricated).

### Keyword map (final)
| Page | Primary | Secondary |
|------|---------|-----------|
| `/` | bomber jackets in Lahore | buy bomber jacket in Lahore, men's jackets Lahore |
| `/bomber-jackets/` | bomber jacket price in Lahore | bomber jackets for men, faux leather / jacquard bomber Lahore |
| `/jackets/` | men's winter jackets in Lahore | jackets for men Lahore, winter jacket price Lahore |
| `/varsity-jackets/` | varsity jacket in Lahore | varsity jackets for men Pakistan, varsity bomber jacket |
| `/faq/` | jacket delivery in Lahore (+ area names) | jacket sizes M L, order jacket on WhatsApp |
| Onyx | black faux-leather bomber jacket men | black bomber jacket Lahore |
| Monochrome | track jacket for men Lahore | black and white track jacket |
| Midnight Panel | varsity bomber jacket men | black and white varsity jacket |
| Cocoa | brown bomber jacket men | pinstripe bomber jacket |
| Copper Python | jacquard bomber jacket men | snake print bomber jacket |
| Navy Python | navy blue bomber jacket men | navy jacquard bomber |

Market context used: comparable Lahore/Pakistan bombers sell at Rs 2,650–5,950
(Bandana, Charcoal, Weave, Lookbuy) and the typical men's jacket in Pakistan is ~Rs 9,490
(Oct 2026 market survey) — at Rs 3,000, Fakhir's price is a genuine ranking and click
advantage, so price is in every title/description rather than hidden.

## 3. Remaining recommendations (need owner access or a decision)

1. **Deploy the package** (above), then in **Google Search Console**: verify the property,
   submit `sitemap.xml`, and use URL Inspection → Request Indexing for `/` and
   `/bomber-jackets/`. Repeat in Bing Webmaster Tools.
2. **Google Business Profile (highest-impact local step):** create a profile as
   *Clothing store*, name **Fakhir Outwears**, phone **+92 323 4123244**, website URL,
   service area **Lahore**. If there is no customer-facing storefront, register with the real
   business address for verification and set it as a **service-area business** (address
   hidden) — do not invent a shop address. Add each jacket as a Product (Rs 3,000), upload
   the product photos, and mirror the same details on Bing Places and Apple Business Connect.
3. **Reviews:** ask the 5 customers whose reviews already appear on the site (and every new
   buyer) to post their review on Google. Do not copy the website reviews into Google
   yourself — they must come from the customers' own accounts.
4. **Citations (keep NAP identical everywhere):** Fakhir Outwears · Lahore, Punjab, Pakistan ·
   +92 323 4123244 · https://fakhir-store.vercel.app/ — submit to Cylex Pakistan, Hotfrog
   Pakistan, Tuugo, Fyple, Yalwa and Pakistan business directories, plus the brand's Facebook,
   Instagram and TikTok profiles with the same name, phone and website link.
5. **Buy a custom domain** (e.g. fakhiroutwears.pk) and point the Vercel project at it: a
   `vercel.app` subdomain weakens local trust and brand searches. 301s are automatic on Vercel.
6. **Decide the delivery fee and returns policy**, then they can be added to Product schema
   (`shippingDetails`, `hasMerchantReturnPolicy`) for Google merchant listings — not added
   now because the site does not publish them and they must not be guessed.
7. **Measure the M and L jackets** (chest, length, sleeve in cm) so a real size-guide page can
   be published — high search demand, but measurements must come from the actual garments.
8. **Local links:** Lahore fashion Instagram/blogger seeding (gifted jacket for review),
   university society pages, and local winter-shopping roundups. Avoid paid link schemes.

## 4. 30-day Local SEO action plan

**Days 1–7 — Go live & get indexed.** Deploy the package · buy/connect custom domain ·
Search Console + Bing verification · submit sitemap · request indexing for homepage and
/bomber-jackets/ · create and verify the Google Business Profile · add all 6 jackets as GBP
products with photos.

**Days 8–14 — Local presence.** GBP first post (AW2026 collection, Rs 3,000) · submit the
10 citations above · set up Facebook/Instagram/TikTok with identical NAP and website link ·
ask the first 5 reviewers + every new buyer for a Google review · reply to every GBP/WhatsApp
question within the day (response behaviour is a local ranking input).

**Days 15–21 — Content & links.** Publish "Bomber jacket prices in Lahore: what Rs 3,000
should buy you" (honest market comparison) and, once measured, the M/L size guide · pitch 10
Lahore micro-influencers/fashion pages for reviews · submit the product feed to Google
Merchant Center for free listings once delivery/returns (item 6) are decided.

**Days 22–30 — Measure & expand.** In Search Console, record impressions/queries for
"bomber jacket" + Lahore terms and the 4 new pages' first rankings · add real customer
questions from WhatsApp to the FAQ (with schema) · second citation batch · GBP insights
review (calls, direction/WhatsApp taps) · decide next content from actual query data —
likely a "winter jackets under Rs 3,500 in Lahore" guide and area-focused GBP posts
(DHA, Gulberg, Johar Town) rather than doorway pages.
