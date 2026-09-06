# Tugas Rutin 4 — Konversi CSS ke SCSS (7-1 Pattern)

Konversi `style.css` (Tugas Pertemuan 2) ke SCSS modular menggunakan **7-1 pattern**
dan `@use` (bukan `@import`).

## Struktur folder

```
src/scss/
├── abstracts/
│   ├── _variables.scss   # colors (light/dark map), font, spacing map, breakpoints, $buttons map
│   ├── _mixins.scss      # 5 mixin reusable
│   └── _index.scss       # @forward semua abstracts -> 1 pintu masuk
├── base/
│   ├── _reset.scss       # reset dasar (*, html, img)
│   ├── _typography.scss  # heading, paragraf, eyebrow
│   └── _base.scss        # body, link, skip-link
├── components/
│   ├── _buttons.scss     # .btn-primary / .btn-secondary via @each loop
│   ├── _table.scss       # info-table + quick-facts
│   ├── _cards.scss       # card, card-grid, timeline
│   ├── _gallery.scss     # gallery-grid + @for loop (stagger transition-delay)
│   └── _forms.scss       # contact-list & contact-form
├── layout/
│   ├── _header.scss      # site-header + main-nav
│   ├── _hero.scss        # hero section (pakai respond-to mixin)
│   ├── _section.scss     # .section wrapper + contact-grid
│   └── _footer.scss      # site-footer
├── pages/
│   └── _home.scss        # placeholder override khusus halaman (kosong, single-page site)
├── themes/
│   └── _dark-mode.scss   # :root + body:has(#dark-toggle:checked) via theme-vars mixin
├── vendors/
│   └── _index.scss       # placeholder untuk CSS pihak ketiga (belum dipakai)
└── main.scss             # entry point, hanya berisi @use ke semua partial
```

## Variables (`abstracts/_variables.scss`)

- `$color-light` / `$color-dark` — Sass map warna, di-generate jadi CSS custom
  property (`--ink`, `--paper`, dst.) lewat mixin `theme-vars`.
- `$spacing` — skala spacing (`xs`…`xxl`).
- `$breakpoints` — `("mobile": 768px)`.
- `$buttons` — map varian tombol (bg, warna teks, border, warna hover) dipakai
  oleh loop `@each` di `components/_buttons.scss`.

## Mixins (`abstracts/_mixins.scss`) — 5 buah

| Mixin | Fungsi |
|---|---|
| `theme-vars($map)` | Loop `@each` untuk generate custom property dari sebuah map (dipakai 2x: light & dark theme) |
| `respond-to($breakpoint)` | Media query berbasis `$breakpoints` map |
| `flex($direction, $justify, $align, $gap)` | Shorthand flexbox, dipakai di header, nav, hero-buttons, footer |
| `button-variant($bg, $color, $border, $hover-bg)` | Styling tombol tanpa duplikasi |
| `card-surface($radius)` | Permukaan kartu (card, quick-facts, gallery figure) |

## Perulangan (loop)

1. **`@each`** — `abstracts/_mixins.scss` (di dalam `theme-vars`) & `components/_buttons.scss`
   (generate `.btn-primary`/`.btn-secondary` langsung dari map `$buttons`).
2. **`@for`** — `components/_gallery.scss`, memberi `transition-delay` bertingkat
   ke 6 `figure` pertama di gallery grid.

## Nesting

Kedalaman nesting dijaga maksimal 3 level, contoh:
`.quick-facts { li { &::before { ... } } }` (3 level).

## `@use`, bukan `@import`

Semua partial saling terhubung lewat `@use`, dan `abstracts/_index.scss`
memakai `@forward` supaya file lain cukup menulis:

```scss
@use "../abstracts" as *;
```

## Compile

Sudah pakai **Dart Sass** (`sass` npm package), teruji tanpa error/warning.

```bash
npm install          # install dependency (sass)
npm run build        # -> dist/style.css (expanded, untuk development)
npm run build:min    # -> dist/style.min.css (compressed, untuk produksi)
npm run watch        # auto-compile saat file .scss berubah
```

`index.html` sudah diarahkan ke `dist/style.css` hasil compile.
