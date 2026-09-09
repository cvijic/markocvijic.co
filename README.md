# markocvijic.co

Personal site of Marko Cvijić — Owned Media Director, Senior Partner and Founder at
[Funky Enterprises](https://funky.enterprises).

Static site. No build step, no dependencies, no framework. Two files do the work:
`index.html` and `styles.css`.

## Files

| Path | Purpose |
|---|---|
| `index.html` | The whole page. Sections: hero, box score, work, selected work, writing, books, show, off the clock, contact. |
| `styles.css` | Hand-written CSS. Design tokens at the top, then reset, typography, layout, components. |
| `404.html` | Styled not-found page for GitHub Pages. |
| `assets/` | Portrait image. |
| `favicon.svg` | Favicon. |
| `CNAME` | Custom domain for GitHub Pages. Must contain `markocvijic.co`. |
| `robots.txt`, `sitemap.xml` | Crawl directives and sitemap. |
| `.nojekyll` | Tells GitHub Pages to serve files as-is, skipping Jekyll. |

## Local preview

Any static file server works:

```bash
python3 -m http.server 3000
# then open http://localhost:3000
```

## Theming

Dark is the default. Light mode follows `prefers-color-scheme`, and the `Theme`
button overrides it, storing the choice in `localStorage` under `mc-theme`.
An inline script in `<head>` applies the stored choice before first paint so the
page does not flash the wrong theme.

All colours live in CSS custom properties at the top of `styles.css`:

```css
--bg  --surface  --line  --text  --muted  --lime  --lime-text
```

Change them in one place and the whole page follows.

## Editing content

Everything is plain HTML, so edit it directly.

- **Stats** — the `.scores` block. One `.score` per number.
- **Client list** — the `.client-band` list.
- **Disciplines and selected work** — `.row` blocks: `.row-key` is the label, the
  `<p>` is the description.
- **Writing** — `.post` links. Newest first.
- **Books, Show, Off the clock** — one section each, straightforward markup.

## Deployment (GitHub Pages)

1. Push to the default branch.
2. Repository **Settings → Pages** → Source: *Deploy from a branch* → branch `main`, folder `/ (root)`.
3. **Custom domain**: `markocvijic.co` → Save.
4. Wait for the DNS check to pass, then tick **Enforce HTTPS**.

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
