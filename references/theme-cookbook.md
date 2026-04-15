# Theme Cookbook

Four ready-to-use themes for common deck moods. Each is a full `theme` object — drop it into `<Deck theme={theme}>` and you're done. Feel free to tweak.

All themes define `colors` (at least the four canonical keys: `primary`, `secondary`, `tertiary`, `quaternary`), `fontSizes`, `fonts`, and `space`. They all assume the font files are available; see the note at the end for font-loading.

## 1. Corporate Clean

Light background, restrained palette, serif headings. Good for financial results, quarterly updates, executive briefings.

```ts
const corporateCleanTheme = {
  colors: {
    primary: '#1f2937',      // text
    secondary: '#0f172a',    // headings
    tertiary: '#f8fafc',     // accent bg
    quaternary: '#2563eb'    // links / highlights
  },
  fontSizes: {
    h1: '72px',
    h2: '52px',
    h3: '36px',
    text: '28px',
    small: '20px'
  },
  fonts: {
    header: '"Source Serif Pro", Georgia, serif',
    text: '"Inter", -apple-system, sans-serif',
    monospace: '"JetBrains Mono", Menlo, monospace'
  },
  space: [16, 24, 32, 48]
};
```

Typical slide backgrounds: use `backgroundColor="tertiary"` for light accent slides, leave default white for most content.

## 2. Midnight Dev

Dark background, high-contrast code-friendly palette. Good for technical talks, engineering reviews, launch-announcement keynotes.

```ts
const midnightDevTheme = {
  colors: {
    primary: '#e6e6e6',      // text on dark
    secondary: '#ffd400',    // headings, accents
    tertiary: '#0b0f1a',     // deep background
    quaternary: '#4fc3f7'    // links / highlights
  },
  fontSizes: {
    h1: '80px',
    h2: '56px',
    h3: '36px',
    text: '30px',
    small: '22px'
  },
  fonts: {
    header: '"Space Grotesk", "Helvetica Neue", sans-serif',
    text: '"Inter", sans-serif',
    monospace: '"Fira Code", "JetBrains Mono", monospace'
  },
  space: [16, 24, 40, 56],
  backdropStyle: {
    position: 'fixed',
    top: 0,
    left: 0,
    width: '100vw',
    height: '100vh',
    backgroundColor: '#0b0f1a'
  }
};
```

All slides start dark automatically because `backdropStyle` sets the page background to `#0b0f1a`. For a section divider, pair with `<Slide backgroundColor="secondary">` for a sudden yellow flash.

## 3. Playful Pastel

High-saturation bright palette, rounded feel, sans-serif everything. Good for design talks, internal kickoffs, community meetups.

```ts
const playfulPastelTheme = {
  colors: {
    primary: '#2b1a46',      // deep purple text
    secondary: '#ff6b9d',    // hot pink headings
    tertiary: '#fff6e5',     // cream background
    quaternary: '#7c3aed'    // violet links
  },
  fontSizes: {
    h1: '88px',
    h2: '60px',
    h3: '40px',
    text: '32px',
    small: '24px'
  },
  fonts: {
    header: '"Fraunces", "Didot", serif',
    text: '"DM Sans", "Helvetica Neue", sans-serif',
    monospace: '"IBM Plex Mono", monospace'
  },
  space: [16, 28, 40, 64],
  backdropStyle: {
    position: 'fixed',
    top: 0,
    left: 0,
    width: '100vw',
    height: '100vh',
    backgroundColor: '#fff6e5'
  }
};
```

Combine with transitions that use `transform: scale(...)` or `translateY` for an even more playful feel.

## 4. Editorial

Magazine-style feel, big serifs, lots of whitespace, muted colors. Good for long-form talks, retrospectives, storytelling-heavy decks.

```ts
const editorialTheme = {
  colors: {
    primary: '#2d2d2d',
    secondary: '#8b0000',    // deep red accent
    tertiary: '#faf8f3',     // paper background
    quaternary: '#8b0000'    // same as secondary for link cohesion
  },
  fontSizes: {
    h1: '96px',
    h2: '64px',
    h3: '40px',
    text: '28px',
    small: '20px'
  },
  fonts: {
    header: '"Playfair Display", Georgia, serif',
    text: '"EB Garamond", Georgia, serif',
    monospace: '"IBM Plex Mono", monospace'
  },
  space: [16, 32, 56, 80],
  lineHeights: {
    text: 1.6,
    heading: 1.1
  },
  backdropStyle: {
    position: 'fixed',
    top: 0,
    left: 0,
    width: '100vw',
    height: '100vh',
    backgroundColor: '#faf8f3'
  }
};
```

The wider `space` scale means more breathing room on each slide, which matches the editorial feel.

## Combining with custom transitions

Themes and transitions are independent, but they pair well. For example, Editorial looks great with a slow fade:

```tsx
const slowFade = {
  from:  { opacity: 0 },
  enter: { opacity: 1 },
  leave: { opacity: 0 }
};

<Deck theme={editorialTheme} transition={slowFade} template={<DefaultTemplate />}>
```

Midnight Dev pairs well with a subtle slide-and-fade:

```tsx
const slideAndFade = {
  from:  { opacity: 0, transform: 'translateY(40px)' },
  enter: { opacity: 1, transform: 'translateY(0)' },
  leave: { opacity: 0, transform: 'translateY(-40px)' }
};
```

## Font loading

These themes reference Google Fonts (Inter, JetBrains Mono, Fraunces, Playfair Display, EB Garamond, etc.). The simplest way to load them is a `<link>` in your HTML entry point:

```html
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&family=JetBrains+Mono&display=swap" rel="stylesheet">
```

Or via `@import` in a CSS file that's loaded before the deck renders:

```css
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap');
```

For production decks, self-host the font files to avoid a CDN dependency and FOUT on first load. Common pitfalls around fonts are in [anti-patterns.md](anti-patterns.md#custom-fonts-not-loading).

## Picking a starting point

| Audience / mood                             | Start with           |
|---------------------------------------------|----------------------|
| Investors, board, quarterly results         | Corporate Clean      |
| Engineers, technical talks, conferences     | Midnight Dev         |
| Designers, community, kickoffs              | Playful Pastel       |
| Long-form talks, storytelling, retros       | Editorial            |

All four are designed to work with the default `DefaultTemplate`. If you build a custom template, pass `color="primary"` (or another theme key) to keep the overlay controls in sync with your palette.
