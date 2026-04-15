# Theme System

Spectacle's theme system is built on [styled-system](https://github.com/styled-system/styled-system). A theme is a 2-level-deep object of labeled keys and CSS property values that you pass to `<Deck>`. Every styled component reads from this theme first when you pass it a label, and falls back to raw CSS otherwise.

## The minimum theme

```ts
const theme = {
  colors: {
    primary: '#000',
    secondary: '#333',
    tertiary: '#f5f5f5',
    quaternary: '#06c'
  },
  fontSizes: {
    h1: '72px',
    h2: '52px',
    text: '28px'
  }
};

<Deck theme={theme} template={<DefaultTemplate />}>
```

Anything you omit falls back to Spectacle's built-in default theme, so a deck with `theme={}` works fine — you only override what you want to customize.

## How label → value lookup works

Each base prop reads from a specific theme key. When you pass a string value, Spectacle checks whether it's a key in the matching theme section, and uses the mapped value if so; otherwise it treats it as raw CSS.

```tsx
<Box backgroundColor="primary" />   // reads theme.colors.primary
<Box backgroundColor="#ff00aa" />   // raw CSS hex
<Text fontSize="h1" />              // reads theme.fontSizes.h1
<Text fontSize="48px" />            // raw CSS
```

This means **theme keys must not collide with valid CSS values**. A theme key named `'red'` would shadow the CSS color `red` — don't do it.

## Theme keys and what they map to

| Theme Key        | Maps to CSS Properties                                                          |
|------------------|---------------------------------------------------------------------------------|
| `space`          | `margin`, `padding`, `grid-gap`                                                 |
| `colors`         | `color`, `background-color`, `border-color`                                     |
| `sizes`          | `width`, `height`, `min-width`, `max-width`, `min-height`, `max-height`         |
| `fontSizes`      | `font-size`                                                                     |
| `borders`        | `border`, `border-top`, `border-right`, `border-bottom`, `border-left`          |
| `borderWidths`   | `border-width`                                                                  |
| `borderStyles`   | `border-style`                                                                  |
| `radii`          | `border-radius`                                                                 |
| `fonts`          | `font-family`                                                                   |
| `fontWeights`    | `font-weight`                                                                   |
| `lineHeights`    | `line-height`                                                                   |
| `letterSpacings` | `letter-spacing`                                                                |
| `shadows`        | `box-shadow`, `text-shadow`                                                     |
| `zIndices`       | `z-index`                                                                       |

Plus two presentation-level overrides:

| Theme Key       | Type                  | Purpose                                                                                        |
|-----------------|-----------------------|------------------------------------------------------------------------------------------------|
| `backdropStyle` | `React.CSSProperties` | Styles for the backdrop (the edge-to-edge area behind your slides). Defaults to `{ position: 'fixed', top: 0, left: 0, width: '100vw', height: '100vh' }`. |
| `Backdrop`      | `React.ElementType`   | Replace the default backdrop element. Defaults to `'div'`. Use for custom animated backgrounds. |

## The `space` array — scaled spacing

`space` is special: it's usually an **array** of integers, used as a scale for margins, paddings, and gaps. When a component receives `padding={2}`, that number is an index into this array.

```ts
const theme = {
  space: [16, 24, 32]  // index 0 = 16, index 1 = 24, index 2 = 32
};
```

With this theme, `padding={0}` is `16px`, `padding={1}` is `24px`, `padding={2}` is `32px`.

### Default margin indices per component

Spectacle components pick defaults from the `space` array:

| Component              | Default `space` index | Default value with `[16, 24, 32]` |
|------------------------|-----------------------|-----------------------------------|
| `Slide`                | 2                     | 32px                              |
| `Heading`              | 1                     | 24px                              |
| `Text`                 | 0                     | 16px                              |
| `OrderedList`          | 0                     | 16px                              |
| `UnorderedList`        | 0                     | 16px                              |
| `ListItem`             | 0                     | 16px                              |
| `Link`                 | 0                     | 16px                              |
| `Quote`                | 0                     | 16px                              |
| `CodeSpan`             | 0                     | 16px                              |

**If you override `theme.space` with fewer than 3 entries, any component defaulting to a higher index silently falls back to `0`.** So a theme with `space: [20]` makes `Slide`s collapse to 0 padding. Always provide at least 3 entries.

You can also pass `space` as an object (styled-system supports both), but the array form is more common in Spectacle decks and it's what most examples use.

## Custom fonts

Spectacle ships with its own defaults, but a branded deck usually wants brand typography. Load the font via `@font-face` (or Google Fonts / CDN), then register it in `theme.fonts`:

```ts
const theme = {
  fonts: {
    header: '"Inter", sans-serif',
    text:   '"Inter", sans-serif',
    monospace: '"JetBrains Mono", monospace'
  }
};
```

Typography components default to:

- `Heading` → `fontFamily: header`
- `Text`, `FitText`, `Link`, `Quote`, lists → `fontFamily: text`
- `CodeSpan` → `fontFamily: monospace`

Getting the font to actually load is a separate task — see [anti-patterns.md](anti-patterns.md#custom-fonts-not-loading) for the common pitfalls (ordering, self-hosted vs CDN, FOUT).

## Default font size keys

The built-in theme uses these keys:

| Key          | Purpose                           |
|--------------|-----------------------------------|
| `h1`         | `Heading` default                 |
| `h2`         | Smaller heading (manual usage)    |
| `h3`         | Even smaller (manual usage)       |
| `text`       | `Text`, `FitText`, lists, `Link`  |

Add more if you want a full scale: `h1`, `h2`, `h3`, `h4`, `text`, `small`, etc.

## Default color keys

The built-in theme defines:

- `primary` — main text / foreground
- `secondary` — heading color, used by `Heading` and `Quote` border
- `tertiary` — accent
- `quaternary` — link color, used by `Link`

When defining your own theme, name the four canonical colors (so components pick them up automatically) and add more as needed:

```ts
const theme = {
  colors: {
    primary: '#1a1a1a',
    secondary: '#d72a42',
    tertiary: '#fff9e6',
    quaternary: '#0074d9',
    // extras
    accent: '#ffd400',
    muted: '#999'
  }
};
```

## Deck templates — render-prop overlays

A `template` is a layer that renders on every slide. The template prop accepts either a `ReactNode` (simplest case: `<DefaultTemplate />`) or a function of `{ slideNumber, numberOfSlides }` that returns JSX. See [deck-and-slides.md](deck-and-slides.md#templates--deck-wide-overlays) for details; the relevant bit here is that a template is **not** part of the theme object — it's a separate prop.

## Putting it all together

```tsx
import { Deck, Slide, Heading, Text, DefaultTemplate } from 'spectacle';

const theme = {
  colors: {
    primary: '#f5f5dc',
    secondary: '#ffd400',
    tertiary: '#1a1a1a',
    quaternary: '#06c'
  },
  fontSizes: {
    h1: '84px',
    h2: '60px',
    text: '32px'
  },
  fonts: {
    header: '"Space Grotesk", sans-serif',
    text: '"Inter", sans-serif'
  },
  space: [16, 24, 32, 48]
};

<Deck theme={theme} template={<DefaultTemplate color="primary" />}>
  <Slide backgroundColor="tertiary">
    <Heading>Themed</Heading>
    <Text>Everything flows from one object.</Text>
  </Slide>
</Deck>
```

For ready-to-use starting points, see [theme-cookbook.md](theme-cookbook.md).
