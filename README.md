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
