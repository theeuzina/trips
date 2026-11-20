# The Eu Zina – Trips

Static landing pages for the Eu Zina website, served at https://trips.theeuzina.com/ from the [docs](./docs/) folder.

## Adding a landing page

See [AGENTS.md](./AGENTS.md) for how pages and images are structured.

It's also a good idea to add a redirect from `theeuzina.com/FOO` to `trips.theeuzina.com/FOO` (where `FOO` is the page's folder name, e.g. `mexico`), just in case someone types or shares the main-site address. Set it up in the [Wix redirects manager](https://manage.wix.com/dashboard/3315b222-1337-4611-833c-bcc8f2a52850/seo-home/redirects).

### Uppercase URLs

GitHub Pages paths are case-sensitive, so `/Mexico` or `/MEXICO` would normally be a 404. [`docs/404.html`](./docs/404.html) catches those and redirects to the lowercase path (`/mexico/`), which covers every page and casing automatically. Keep page folder names lowercase. Don't add `Mexico/`-style folders for this: on case-insensitive filesystems (the macOS default) they'd collide with `mexico/` and break checkouts.

## [Mexico City 2027](https://trips.theeuzina.com/mexico/) ([docs/mexico/](./docs/mexico/))

The page is self-contained: `index.html` contains the layout and styling, and the `assets/` directory contains its images.

### Links

- The first hero CTA scrolls to the investment section.
- The remaining CTA buttons open the Mexico 2027 Calendly page.

## [The Wild Within 2026](https://trips.theeuzina.com/safari/) ([docs/safari/](./docs/safari/))

A conservation and wellness safari retreat at Amaya Lodge, Timbavati, South Africa. The page is a self-hosted copy of the original Gamma site (the-wild-within-clg5uxo.gamma.site), rebuilt as a self-contained `index.html` with its images in `assets/`.

- Gamma's layout is reproduced as twelve cards, with Gamma's margins (32px, cards at most 1161px wide). On screens narrower than 1024px each card stacks its photo above its text; Gamma's own narrow-screen layout didn't.
- Gamma's fade-ins are reproduced with the same timing: each block fades in and rises 20px over 1s once it is 100px inside the viewport, and boxes in a group follow 0.15s apart. The first card animates in CSS on load, starting at 1% opacity so Chrome still records the first paint (FCP/LCP). Visitors who ask for reduced motion get no animation.
- Gamma's icons are Font Awesome Pro, which can't be redistributed, so the page uses the closest [Phosphor](https://phosphoricons.com/) icons (MIT) instead, inlined as an SVG sprite. Phosphor has no elephant, so "Encounter" uses an eye.
- The page has no booking buttons. The only link is the contact email.
