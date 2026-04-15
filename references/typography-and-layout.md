# Typography, Layout, and Base Props

This file covers Spectacle's text components (`Heading`, `Text`, lists, `Quote`, `Link`, `CodeSpan`), its layout primitives (`Box`, `FlexBox`, `Grid`), tables, and the styled-system base props that every component accepts.

## Typography

All typography components honor the theme by default — they pick colors from `theme.colors`, sizes from `theme.fontSizes`, families from `theme.fonts`, etc. Override by passing CSS values or theme keys as props.

| Component       | Default role                             | Default color | Default alignment | Default `fontSize` |
|-----------------|------------------------------------------|---------------|-------------------|--------------------|
| `Heading`       | Slide title                              | `secondary`   | `center`          | `h1`               |
| `Text`          | Body / paragraph                         | `primary`     | `left`            | `text`             |
| `FitText`       | Auto-scaling text that fills its container | `primary`   | `center`          | `text`             |
| `Link`          | Hyperlink (underlined)                   | `quaternary`  | `left`            | `text`             |
| `Quote`         | Pull quote with a left border accent     | `primary`     | `left`            | `text`             |
| `OrderedList`   | `<ol>`                                   | `primary`     | `left`            | `text`             |
| `UnorderedList` | `<ul>`                                   | `primary`     | `left`            | `text`             |
| `ListItem`      | `<li>` (use inside the lists)            | inherited     | inherited         | inherited          |
| `CodeSpan`      | Inline monospace                         | inherited     | inherited         | `text`             |

### Typical usage

```tsx
<Slide>
  <Heading>Production incident retrospective</Heading>
  <Text>Root cause: DNS. As always.</Text>

  <UnorderedList>
    <ListItem>Detected at 02:14 UTC</ListItem>
    <ListItem>Mitigated at 02:42 UTC</ListItem>
    <ListItem>Resolved at 03:18 UTC</ListItem>
  </UnorderedList>

  <Quote>We should have had a runbook for this.</Quote>
  <Link href="https://example.com/postmortem">Full postmortem</Link>
</Slide>
```

### `FitText` vs `Text`

`FitText` auto-scales its font to fit the container. Use it for short phrases that must fit a specific width — "big fact" numbers, headline phrases — when you don't want to manually pick a size.

```tsx
<FitText>Scale me to the box</FitText>
```

### Headings aren't semantic — they're styled

`Heading` doesn't render an `<h1>` vs `<h2>` based on content. It's always a visual heading with the `h1` font size from your theme. If you want different heading sizes, pass `fontSize` directly:

```tsx
<Heading fontSize="h2">Subheading</Heading>
```

or define more theme keys and reference them:

```ts
const theme = { fontSizes: { h1: '72px', h2: '52px', h3: '36px' } };
```

## Layout primitives: `Box`, `FlexBox`, `Grid`

These are the escape hatch when a `SlideLayout.*` doesn't fit.

### `Box`
A generic block container. Accepts `Space`, `Color`, `Layout`, `Position`, `Border` props.

```tsx
<Box padding={2} backgroundColor="tertiary" borderRadius={8}>
  <Heading>Boxed</Heading>
</Box>
```

### `FlexBox`
A flexbox container. Accepts everything `Box` does plus `Flex` props (`alignItems`, `justifyContent`, `flexDirection`, etc.).

```tsx
<FlexBox height="100%" flexDirection="column" justifyContent="space-between">
  <Heading>Top</Heading>
  <Text>Middle</Text>
  <Text>Bottom</Text>
</FlexBox>
```

### `Grid`
A CSS Grid container. Accepts `Layout`, `Position`, and `Grid` props (`gridTemplateColumns`, `gridGap`, etc.).

```tsx
<Grid gridTemplateColumns="1fr 2fr" gridGap={2}>
  <Text>Label</Text>
  <Text>Value</Text>
  <Text>Another label</Text>
  <Text>Another value</Text>
</Grid>
```

## The base prop system (styled-system)

Every component accepts a set of CSS-property-aliased props. Most importantly, each prop accepts **either a theme key or a raw CSS value**:

```tsx
<Box backgroundColor="primary" />   // theme lookup → theme.colors.primary
<Box backgroundColor="#ff00aa" />   // raw CSS
```

### `Space` props (margin / padding)

`m`, `mt`, `mr`, `mb`, `ml`, `mx`, `my`, and the `p*` equivalents. These accept:

- A **number**, which is an index into `theme.space` (an array). `padding={2}` → `theme.space[2]`.
- A **CSS string**, which is passed through. `padding="24px 8px"`.
- A **theme key**, if you've defined object-style space values.

```tsx
<Text padding={2}>Scaled padding</Text>
<Text padding="24px 8px">Raw CSS padding</Text>
```

Default `theme.space` is `[16, 24, 32]`. Components default to different indices:
- `Slide` → index 2 (32px)
- `Heading` → index 1 (24px)
- `Text`, `ListItem`, `Link`, `Quote`, `CodeSpan`, lists → index 0 (16px)

### `Color` props

- `color`
- `bg` or `backgroundColor`

Both look up in `theme.colors` first, fall back to CSS.

### `Typography` props

`fontFamily`, `fontSize`, `fontWeight`, `lineHeight`, `letterSpacing`, `textAlign`, `fontStyle`.

Sizes and families look up in `theme.fontSizes` / `theme.fonts` first. So `fontSize="h1"` resolves to `theme.fontSizes.h1`.

### `Layout` props

`width`, `height`, `minWidth`, `maxWidth`, `minHeight`, `maxHeight`, `size` (sets both width and height), `display`, `overflow`, `overflowX`, `overflowY`.

**The numeric shortcuts are unusual and worth memorizing:**

- Fractional numbers become percents: `width={1/2}` → `"50%"`, `width={2/3}` → `"66.666...%"`
- Whole numbers become pixels: `width={256}` → `"256px"`
- Strings pass through unchanged: `width="50vw"`, `width="256px"`

This means `width="1/2"` does **not** work — it's a string, not a fraction. Use `width={1/2}`.

### `Flex` props (on `FlexBox`)

`alignItems`, `alignContent`, `justifyContent`, `flexWrap`, `flexBasis`, `flexDirection`, `flex`, `justifySelf`, `alignSelf`, `order`.

### `Grid` props (on `Grid`)

`gridGap`, `gridColumnGap`, `gridRowGap`, `gridColumn`, `gridRow`, `gridAutoFlow`, `gridAutoColumns`, `gridAutoRows`, `gridTemplateColumns`, `gridTemplateRows`, `gridTemplateAreas`, `gridArea`.

### `Position` props

`position`, `zIndex`, `top`, `right`, `bottom`, `left`.

### `Border` props

`border`, `borderWidth`, `borderStyle`, `borderColor`, `borderRadius`, and the `Top`/`Right`/`Bottom`/`Left` variants, plus `borderX` and `borderY`.

## Tables

Tables use a matching set of components: `Table`, `TableHeader`, `TableBody`, `TableRow`, `TableCell`. All accept Space, Color, Layout, Typography, and Border props.

```tsx
<Table>
  <TableHeader>
    <TableRow>
      <TableCell>Metric</TableCell>
      <TableCell>Before</TableCell>
      <TableCell>After</TableCell>
    </TableRow>
  </TableHeader>
  <TableBody>
    <TableRow>
      <TableCell>p95 latency</TableCell>
      <TableCell>820ms</TableCell>
      <TableCell>95ms</TableCell>
    </TableRow>
    <TableRow>
      <TableCell>Error rate</TableCell>
      <TableCell>2.4%</TableCell>
      <TableCell>0.01%</TableCell>
    </TableRow>
  </TableBody>
</Table>
```

Tables default to left-aligned `primary`-colored text. `TableHeader` cells additionally default to `fontWeight: bold`.

## Images

`Image` is a plain `<img>` wrapper that accepts `src`, `Layout`, and `Position` props:

```tsx
<Image src="/logo.png" width={256} />
```

For image-first slides, prefer `SlideLayout.HorizontalImage`, `SlideLayout.VerticalImage`, `SlideLayout.ThreeUpImage`, or `SlideLayout.FullBleedImage` — see [slide-layouts.md](slide-layouts.md).

## Rules of thumb

- **Reach for theme keys, not raw CSS.** `color="primary"` scales; `color="#3498db"` doesn't.
- **Use `FlexBox`, not custom CSS, for centering.** `<FlexBox height="100%" alignItems="center" justifyContent="center">` is the canonical center.
- **Avoid inline `style={{...}}`.** Spectacle's styled-system props compose cleanly with the theme; inline styles opt out of that.
- **Width/height fractions use `{1/2}`, not `"1/2"`.** Strings don't trigger the numeric shortcut.
- **Typography sizes scale via `theme.fontSizes`**, not inline `fontSize` values, if you want the deck to be themeable later.
