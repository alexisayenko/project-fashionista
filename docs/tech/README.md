# Technological stack

Inventory of technologies, services, and external resources the Fashionista project uses.

> **Status:** This document describes the project's stack as of the in-progress migration to **Astro**. The `src/`, `dist/`, `package.json` layout below is the canonical target; differences between the current working tree and this document represent in-progress migration work.

## Frontend

- **[Astro](https://astro.build/)** — static-site framework. Pages live as `.astro` components in `src/pages/`; per-house content is data, not markup, in `src/content/houses/*.yaml` validated against a Zod schema in `src/content/config.ts`. A single layout (`src/layouts/HouseLayout.astro`) renders every house from the same template — see [ui-ux.md → Content schema](ui-ux.md#content-schema) for the field shape.
- **HTML5** — Astro's compiled output. We do not hand-author per-page HTML.
- **CSS3** — vanilla, in `src/styles/site.css`, imported once by the layout. No preprocessor, no CSS framework.
- **TypeScript** — used for the content schema (`src/content/config.ts`) and component scripts that benefit from typing. Type-checking runs at build via `astro check`.
- **JavaScript** — minimal, scoped per-component. Hero slider and signature-piece lightbox carry over as inline IIFEs inside `.astro` components.

## Third-party libraries

Pinned in `package.json`; lockfile (`package-lock.json`) is committed; `node_modules/` is gitignored.

- **[astro](https://astro.build/)** — site framework (see Frontend above).
- **[flag-icons](https://github.com/lipis/flag-icons)** — country flag glyphs via CSS, used on the houses grid to mark each maison's country. Imported once by the site layout. Switched from the jsDelivr CDN to npm at the Astro migration so version provenance lives in `package-lock.json` and the bundle is self-contained.

## Typography

System font stacks only — no web fonts / Google Fonts loaded.

- **Serif** — Didot → Bodoni 72 → Playfair Display → Georgia → generic serif
- **Sans-serif** — Helvetica Neue → Arial → generic sans-serif

## Content storage

Per-house data lives in `src/content/houses/<slug>.yaml`, validated at build time by the Zod schema in `src/content/config.ts`. See [ui-ux.md → Content schema](ui-ux.md#content-schema) for the canonical field list and shape.

Static media splits across two folders:

- **`public/assets/`** — logos, wordmarks, and other SVGs. Served verbatim, untouched by the build, with stable URLs. Matches the [compliances.md §2b](compliances.md#2b-logos--wordmarks) rule against altering, recolouring, or distorting marks.
- **`src/assets/houses/<slug>/`** — photographs (hero portraits, slider photos, signature-piece images). Imported through Astro's [`<Image>`](https://docs.astro.build/en/guides/images/) component for responsive variants, AVIF/WebP conversion, content-hashed filenames, and dimension auto-injection.

## Build & tooling

- **npm** — package manager. `package.json` and `package-lock.json` are committed; `node_modules/` is gitignored.
- **`astro build`** — produces optimized static HTML in `dist/`, which is what GitHub Pages serves. `dist/` is gitignored.
- **`astro check`** — TypeScript and content-schema check, run as part of the build.
- **No test framework** — content-shape correctness is enforced by the Zod schema at build; visual / editorial correctness is caught by manual review.

## Hosting & deployment

- **GitHub** — repository hosting (`github.com/alexisayenko/project-fashionista`).
- **GitHub Pages** — static hosting. Deploys from the `gh-pages` branch, which is updated by a GitHub Action that runs `astro build` on every push to `main` and pushes `dist/` to `gh-pages`.
- **GitHub Actions** — CI/CD. Workflow at `.github/workflows/deploy.yml`.

## Content sources

Listings below cover provenance — author, license, source URL, download date — which the Astro migration does not change. For where the files physically live in the repo (the `public/assets/` vs `src/assets/houses/<slug>/` split), see [Content storage](#content-storage).

- **Wikimedia Commons** — freely-licensed photography
  - Current Armani portrait: [File:Giorgio Armani 1997.jpg](https://commons.wikimedia.org/wiki/File:Giorgio_Armani_1997.jpg) by GianAngelo Pistoia, CC BY-SA 4.0
  - Coco Chanel hero slider (4 photos), all downloaded 2026-04-30, stored in `assets/`:
    - `chanel-coco-1928-mariniere.jpg` — [File:Gabrielle Chanel en marinière.jpg](https://commons.wikimedia.org/wiki/File:Gabrielle_Chanel_en_marini%C3%A8re.jpg), photographer unknown (1928), PD-anon-70-EU
    - `chanel-coco-1931-la.jpg` — [File:Coco Chanel in Los Angeles, 1931.jpg](https://commons.wikimedia.org/wiki/File:Coco_Chanel_in_Los_Angeles,_1931.jpg) by Los Angeles Times / UCLA Library (1931), CC BY 4.0 (mandatory attribution)
    - `chanel-coco-1925-aintree.jpg` — [File:Hugh Grosvenor, Duke of Westminster und Coco Chanel.jpg](https://commons.wikimedia.org/wiki/File:Hugh_Grosvenor,_Duke_of_Westminster_und_Coco_Chanel.jpg), Radio Times Hulton Picture Library (1925), PD-UK-unknown
    - `chanel-coco-1920.jpg` — [File:Coco Chanel, ca 1920.jpg](https://commons.wikimedia.org/wiki/File:Coco_Chanel,_ca_1920.jpg), photographer unknown (c. 1920), PD-US
  - Chanel N°5 flacon (signature-piece illustration, lightbox-enlargeable): `chanel-no5-bottle.jpg` — [File:CHANEL_No5_parfum.jpg](https://commons.wikimedia.org/wiki/File:CHANEL_No5_parfum.jpg) by Wikimedia user Arz (2006), PD-self (no attribution required, but credited as good practice); native 400×727, no resize needed; the same file used in the [English Wikipedia article infobox](https://en.wikipedia.org/wiki/Chanel_No._5); downloaded 2026-04-30 (replaces a prior CC BY 2.0 photo by Lily — swapped for Arz on visual / editorial grounds and license simplicity)
- **Wikimedia Commons / Wikipedia** — brand logo & wordmark files (PD-textlogo / PD-shape, below US copyright threshold of originality per Compendium §906; trademarks remain). See [compliances.md §2b](compliances.md) for usage rules. All files below downloaded 2026-04-30 and stored in `assets/`.
  - Chanel wordmark: [File:Chanel_logo.svg](https://commons.wikimedia.org/wiki/File:Chanel_logo.svg) — PD-textlogo, Wikimedia Commons → `chanel-wordmark.svg`
  - Chanel interlocking-Cs device: [File:Chanel_logo_interlocking_cs.svg](https://en.wikipedia.org/wiki/File:Chanel_logo_interlocking_cs.svg) — PD-shape, English Wikipedia (en.wp-only because Wikimedia treats it as possibly non-free in France; clears US §906) → `chanel-logo-cc.svg`
  - Index-grid wordmarks (15 files), all PD-textlogo on Wikimedia Commons:
    - Louis Vuitton: [File:Louis Vuitton logo and wordmark.svg](https://commons.wikimedia.org/wiki/File:Louis_Vuitton_logo_and_wordmark.svg) → `louis-vuitton-logo.svg` (combined logo + wordmark — only "combined" file in the set)
    - Hermès: [File:Hermes wordmark.svg](https://commons.wikimedia.org/wiki/File:Hermes_wordmark.svg) → `hermes-logo.svg`
    - Gucci: [File:Gucci Logo.svg](https://commons.wikimedia.org/wiki/File:Gucci_Logo.svg) → `gucci-logo.svg`
    - Gucci interlocking-GG monogram: [File:1960s Gucci Logo.svg](https://commons.wikimedia.org/wiki/File:1960s_Gucci_Logo.svg) → `gucci-monogram.svg`
    - Louis Vuitton interlocking-LV monogram (single glyph, NOT the tiled Toile canvas): [File:LV Monogram.svg](https://commons.wikimedia.org/wiki/File:LV_Monogram.svg) → `louis-vuitton-monogram.svg`
    - Prada: [File:Prada-Logo.svg](https://commons.wikimedia.org/wiki/File:Prada-Logo.svg) → `prada-logo.svg`
    - Dior: [File:Dior Logo.svg](https://commons.wikimedia.org/wiki/File:Dior_Logo.svg) → `dior-logo.svg`
    - Armani: [File:Giorgio Armani.svg](https://commons.wikimedia.org/wiki/File:Giorgio_Armani.svg) → `armani-logo.svg`
    - Versace: [File:Versace old logo.svg](https://commons.wikimedia.org/wiki/File:Versace_old_logo.svg) → `versace-logo.svg` (older text-only mark; the Medusa-head logo is not PD)
    - Balenciaga: [File:Balenciaga Logo.svg](https://commons.wikimedia.org/wiki/File:Balenciaga_Logo.svg) → `balenciaga-logo.svg`
    - Saint Laurent: [File:Saint Laurent logo.svg](https://commons.wikimedia.org/wiki/File:Saint_Laurent_logo.svg) → `saint-laurent-logo.svg`
    - Fendi: [File:Fendi logo.svg](https://commons.wikimedia.org/wiki/File:Fendi_logo.svg) → `fendi-logo.svg` (W3C-invalid SVG but renders cleanly in browsers)
    - Burberry: [File:Burberry wordmark.svg](https://commons.wikimedia.org/wiki/File:Burberry_wordmark.svg) → `burberry-logo.svg` (text only; equestrian-knight figurative mark not included)
    - Bottega Veneta: [File:Logo of Bottega Veneta.svg](https://commons.wikimedia.org/wiki/File:Logo_of_Bottega_Veneta.svg) → `bottega-veneta-logo.svg`
    - Valentino: [File:Valentino logo.svg](https://commons.wikimedia.org/wiki/File:Valentino_logo.svg) → `valentino-logo.svg`
    - Givenchy: [File:Givenchy - wordmark (France, 2003).svg](https://commons.wikimedia.org/wiki/File:Givenchy_-_wordmark_(France,_2003).svg) → `givenchy-logo.svg`
    - Dolce & Gabbana: [File:Dolce & Gabbana.svg](https://commons.wikimedia.org/wiki/File:Dolce_%26_Gabbana.svg) → `dolce-gabbana-logo.svg`

## Version control

- **Git** — all history lives in a GitHub-hosted repo.
