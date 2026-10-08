# AGENTS.md

Guidance for agents working on this repo: static landing pages for EU Zina trips.

## Layout

```
docs/                    GitHub Pages root, served at https://trips.theeuzina.com/
  CNAME                  Custom domain. Managed by GitHub Pages; don't edit.
  index.html             Redirects the bare domain to https://www.theeuzina.com/
  favicon.svg, favicon.ico  Shared site icon (the theeuzina.com mark; .ico is a 48px fallback)
  <slug>/index.html      One landing page per trip, e.g. docs/mexico/
  <slug>/assets/         That page's published images and fonts
originals/<slug>/        Full-size source images for each page. Committed, NOT published.
```

Everything under `docs/` is public. Never put source files, drafts or anything private there. Keep originals in `originals/<slug>/`.

## Landing pages

- Each page is self-contained: one `index.html` with inline `<style>` and `<script>`, plus its own `assets/` folder.
- New trips go in a new sibling folder (`docs/<slug>/`). Copy `docs/mexico/index.html` as a starting point.
- **No third-party assets.** Images, fonts and scripts are all hosted in this repo. Outbound links (such as Calendly booking links) are fine. To check, the only URLs left in a page should be links:
  ```sh
  grep -o 'https\?://[^"'"'"' )]*' docs/<slug>/index.html | sort -u
  ```
- Fonts (DM Sans and Fraunces, variable `woff2`, latin and latin-ext subsets) are self-hosted in `docs/mexico/assets/fonts/` and declared with `@font-face` in the page's `<style>`. If a new page uses the same fonts, move them to a shared `docs/assets/fonts/` and point both pages at it instead of duplicating them.
- Link the shared icon in `<head>`: `<link rel="icon" href="/favicon.ico" sizes="48x48">` and `<link rel="icon" href="/favicon.svg" type="image/svg+xml">`.
- Add a section for the new page to `README.md`.

## Images

Requires `cwebp` (`brew install webp`). `sips` (built into macOS) reads dimensions.

1. Save the full-size original to `originals/<slug>/<name>.jpg`, using a short, descriptive, kebab-case name. Use the highest-resolution version available. Never hotlink.
2. Generate WebP versions into `docs/<slug>/assets/`, named `<name>-<width>.webp`:

   | Kind | Widths | Quality |
   |---|---|---|
   | Full-bleed CSS backgrounds (hero, photo breaks) | 1000, 2000 | 60 |
   | Inline `<img>` photos | 700, 1200 | 65 |
   | Small images (original under 700px wide) | original width only | 75 |

   Never upscale. If the original is narrower than a target width, use the original width and name the file by its real width (e.g. `blue-house-sidewalk-1166.webp`).

   ```sh
   # run from the repo root; set SLUG first
   O=originals/$SLUG; A=docs/$SLUG/assets
   enc(){ src=$1 q=$2; shift 2; name=$(basename "${src%.*}"); sw=$(sips -g pixelWidth "$src" | awk '/pixelWidth/{print $2}')
     for w in "$@"; do [ "$w" -gt "$sw" ] && w=$sw; cwebp -quiet -q "$q" -resize "$w" 0 -metadata none "$src" -o "$A/$name-$w.webp"; done; }
   enc $O/hero.jpg 60 1000 2000        # background
   enc $O/host-photo.jpg 65 700 1200   # inline photo
   enc $O/small.jpg 75 540             # small image at its own width
   ```
   Check one result by eye before moving on. If a grainy photo comes out heavy (over ~400 KB), lowering quality a few points is fine.

3. Reference them in the page:
   - **Inline photos:** always give `srcset`, `sizes`, intrinsic `width`/`height` (the original's dimensions), `loading="lazy"` and `decoding="async"`:
     ```html
     <img src="assets/name-700.webp" srcset="assets/name-700.webp 700w, assets/name-1200.webp 1200w"
          sizes="(max-width:800px) calc(100vw - 44px), 32vw" width="1200" height="1600"
          loading="lazy" decoding="async" alt="…">
     ```
   - **Backgrounds:** put the 1000 version in the base CSS rule and the 2000 version in `@media(min-width:1000px){…}`.
   - **Below-the-fold backgrounds:** add the `lazy-bg` class. The page's script adds `bg-in` as the element nears the viewport, and `.js .lazy-bg:not(.bg-in){background-image:none}` holds the image back until then. Without JavaScript, backgrounds load normally.
   - **Hero image:** never lazy-load it. Preload it in `<head>` with `fetchpriority="high"`, using one `<link rel="preload" as="image">` per size with matching `media` attributes.
   - Don't put the `reveal` fade-in class on above-the-fold content. It hides the headline until the script runs and slows Largest Contentful Paint.
   - Keep page content inside a `<main>` element.
4. Delete published variants that are no longer referenced, and check every `assets/` path in the HTML points to a file that exists.

## Checking performance

Serve `docs/` locally and run Lighthouse in mobile mode (the default):

```sh
(cd docs && python3 -m http.server 8765) &
npx -y lighthouse http://localhost:8765/<slug>/ --chrome-flags="--headless=new" --view
```

In-browser Lighthouse runs pick up browser-extension scripts; use an incognito window. Targets: Performance 90+, Accessibility 100, total transfer under ~500 KB on mobile.
