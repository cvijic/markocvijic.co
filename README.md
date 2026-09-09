# markocvijic.co

Personal site of Marko Cvijić - Owned Media Director, Senior Partner and Founder at
[Funky Enterprises](https://funky.enterprises).

Static site. No build step, no dependencies, no framework. Plain HTML plus one
hand-written stylesheet.

## Structure

Ten pages, one shared stylesheet. Directory-based clean URLs, so `/about/` is
`about/index.html`.

| Path | URL | Purpose |
|---|---|---|
| `index.html` | `/` | Hub. Hero, box score, client band, the seven capabilities as cards, the flagship case, writing and about teasers. |
| `business-consulting/index.html` | `/business-consulting/` | Commercial diagnosis, system design, governance, engagement model, fit. |
| `enterprise-seo/index.html` | `/enterprise-seo/` | Technical foundation, AI answer engines, content architecture, process, operating rules, track record. |
| `ecommerce-seo/index.html` | `/ecommerce-seo/` | Catalog and crawl, revenue measurement, cross-border, the Agromarket case in phases. |
| `paid-media-oversight/index.html` | `/paid-media-oversight/` | Signal integrity, spend governance, six operating rules, fit. |
| `reputation-management/index.html` | `/reputation-management/` | Boundaries stated first, diagnosis, owned foundation, standing and monitoring. |
| `go-to-market/index.html` | `/go-to-market/` | Position, demand, convert, sectors, launch rules. |
| `analytics-attribution/index.html` | `/analytics-attribution/` | Tracking integrity, attribution, AI-native operations, real audit findings. |
| `about/index.html` | `/about/` | Why the center, box score, the role, track record, flagship case, how to engage. |
| `writing/index.html` | `/writing/` | Selected LinkedIn posts, recurring themes, books, Funky Enterprises Show. |
| `styles.css` | - | Hand-written CSS. Design tokens at the top, then reset, typography, layout, components. |
| `404.html` | - | Styled not-found page for GitHub Pages. |
| `assets/` | - | Portrait image. |
| `favicon.svg` | - | Favicon. |
| `CNAME` | - | Custom domain for GitHub Pages. Must contain `markocvijic.co`. |
| `robots.txt`, `sitemap.xml` | - | Crawl directives and sitemap. All ten URLs are listed in the sitemap. |
| `.nojekyll` | - | Tells GitHub Pages to serve files as-is, skipping Jekyll. |

Every page shares the same `<head>`, header and footer. If you change the nav or
the footer sitemap, change it on all ten - or regenerate them, see below.

### Adding a page

1. Create `new-slug/index.html` by copying an existing service page.
2. Add it to the footer `What I do` list on all ten pages.
3. Add it to `sitemap.xml`.
4. Add it to the `.svcs` card grid on `index.html` if it is a capability.

### Regenerating shared markup

The HTML in this repo is the source of truth and can be edited by hand. It was
originally emitted by a one-off Python generator that held the head, header and
footer in one place; that generator is deliberately not in the repo, because the
site's whole promise is that it has no build step. If you make the same edit
across ten files often enough, resurrect a generator rather than editing by hand.

## Local preview

Any static file server works:

```bash
python3 -m http.server 3000
# then open http://localhost:3000
```

## Theming

One theme, light. There is no toggle and no `prefers-color-scheme` switching -
every page declares `<meta name="color-scheme" content="light">` so browser
chrome (form controls, scrollbars) matches instead of rendering dark.

All colours live in CSS custom properties at the top of `styles.css`:

```css
--bg  --surface  --line  --text  --muted  --lime  --lime-text
```

Change them in one place and the whole page follows.

## Analytics

GA4, property `G-7G684NYNZ5`, on all eleven pages including `404.html`. The tag
is inline in `<head>` rather than loaded through a tag manager, because there is
nothing else to manage.

Two things about it are deliberate:

1. **It sits after the HTTPS upgrade script.** If it fired first, a plaintext
   visit would send a hit over `http`, redirect, then send a second one - two
   `page_view` events and a session split across two origins. Keep this order.
2. **It is gated on the live hostname** (`/(^|\.)markocvijic\.co$/`). Local
   preview and automated checks never reach the property, so you do not have to
   remember to filter your own traffic out.

Keeping it on `404.html` is intentional: the hit records the URL that was
actually requested with the title `Page not found`, which is the cheapest way to
find broken inbound links.

### Consent - open item

There is no consent gate. The tag sets analytics cookies on first load for every
visitor, including EU ones, which under GDPR and the ePrivacy Directive needs
prior consent for anything beyond strictly necessary storage. Traffic here is
low and non-commercial, so this is a small exposure rather than an urgent one,
but it is real and it is not fixed. Two ways to close it:

- **Consent Mode v2 plus a banner.** Set `ad_storage`, `ad_user_data`,
  `ad_personalization` and `analytics_storage` to `denied` by default, then
  update on consent. Until consent is given GA4 sends cookieless pings, so you
  keep modelled traffic numbers and lose user-level detail.
- **Drop GA4 for a cookieless analytics tool.** No banner needed, less data.

## Structured data

Each page carries one `application/ld+json` block holding a single `@graph`.
Four nodes are identical on every page and carry stable `@id`s, so crawlers
reconcile them into one entity each rather than eleven near-duplicates:

| `@id` | Type |
|---|---|
| `https://markocvijic.co/#person` | `Person` - Marko |
| `https://funky.enterprises/#organization` | `Organization` - Funky Enterprises |
| `https://markocvijic.co/#portrait` | `ImageObject` |
| `https://markocvijic.co/#website` | `WebSite` |

On top of those, each page adds its own nodes:

| Page | Page-specific nodes |
|---|---|
| `/` | `ProfilePage`, `ItemList` of the seven capabilities |
| the seven capability pages | `WebPage`, `BreadcrumbList`, `Service` |
| `/writing/` | `CollectionPage`, `BreadcrumbList`, two `Book` nodes |
| `/about/` | `AboutPage`, `BreadcrumbList` |
| `404.html` | none, deliberately |

Rules worth keeping if you edit it:

- Every page repeats the four shared nodes in full rather than referencing them
  by `@id` alone, so each page validates standalone.
- The `*Page` node's `url`, `name` and `description` must match that page's
  `<link rel="canonical">`, `<title>` and `<meta name="description">`. There is a
  check for this.
- Titles and descriptions are authored as plain text so the same string can be
  reused verbatim in JSON-LD; the generator HTML-escapes them only on the way
  into the `<head>`. Do not put `&amp;` in the JSON.
- The two `Book` nodes carry no `datePublished`, `isbn`, `offers` or
  `aggregateRating`, because neither book is out. Adding any of those would be a
  false claim in markup, not just an SEO shortcut.
- Breadcrumb `position` starts at 1 and the last item points at the page's own
  canonical URL.

## Editing content

Everything is plain HTML, so edit it directly.

### Page-level components

- **`.subhero`** - the top of every inner page: `.crumb`, `.subhero-title`,
  `.subhero-lede`, then two buttons.
- **`.group`** - `.group-head` (number, `.group-title`, `.group-note`) wrapping a
  `.specs` grid of `.spec` items. The standard capability block.
- **`.steps`** - the numbered process. `.step-num`, `.step-title`, one `<p>`.
- **`.rules`** - numbered operating rules. `.rule-num` plus one `<p>`.
- **`.rows`** - label/description pairs. `.row-key` is the label; add an
  `a.row-more` for a link to another page and it lands under the description.
- **`.cards` / `.cards-2` / `.cards-3`** - `.card`, `.card-accent` for the lime
  border, `.card-title`, `.card-body`, `.card-note`.
- **`.pills`** - the market or sector list.
- **`.page-nav`** - the two links at the foot of every inner page.

### Homepage

- **Stats** - the `.scores` block. One `.score` per number.
- **Client list** - the `.client-band` list.
- **Capabilities** - the `.svcs` grid. One `.svc` anchor per service, numbered
  `.svc-num`, ending in a `.svc-go` label.
- **Teasers** - `.teaser` is a two-column block used for the writing and about
  previews, and for the portrait on `/about/`.

### Writing

- **Posts** - `.post` links inside `.posts`. Newest first.
- **Books and Show** - one section each on `/writing/`, straightforward markup.

## Deployment (GitHub Pages)

1. Push to the default branch.
2. Repository **Settings → Pages** → Source: *Deploy from a branch* → branch `main`, folder `/ (root)`.
3. **Custom domain**: `markocvijic.co` → Save.
4. Wait for the DNS check to pass, then tick **Enforce HTTPS**.

### The HTTPS upgrade script

GitHub Pages cannot issue a server-side redirect, and **Enforce HTTPS** can only
be set from the Settings UI or with an `administration=write` token. Until it is
ticked, a visitor who types the bare domain is served over plaintext. Every page
therefore carries a small inline script in `<head>` that rewrites `http:` to
`https:` client-side, skipping `localhost` and `127.0.0.1` so local preview still
works. It is a fallback, not a substitute: it cannot protect the first request
and it does not send HSTS. Once **Enforce HTTPS** is on, delete the script from
all eleven files.

### DNS records at the registrar

Apex `A` records:

```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

Optional `AAAA` records for IPv6:

```
2606:50c0:8000::153
2606:50c0:8001::153
2606:50c0:8002::153
2606:50c0:8003::153
```

And a `CNAME` for `www` pointing to `<github-username>.github.io.`

## Licence

Content and images: © Marko Cvijić, all rights reserved.
Code: use it if it is useful to you.
