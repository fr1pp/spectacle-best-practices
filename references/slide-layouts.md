# Slide Layouts — the `SlideLayout.*` helpers

`SlideLayout` is a namespace of pre-built layout components that give you a finished slide in one line. Reach for these first; hand-rolling a slide with `FlexBox` + `Heading` + `Text` only makes sense when no preset fits.

## Why use them

Every `SlideLayout.*` component:

- Is a full replacement for a `<Slide>` — drop it as a child of `<Deck>`, not inside a `Slide`.
- Inherits slide props (`backgroundColor`, `backgroundImage`, `transition`, etc.) via `...slideProps` passthrough.
- Respects your theme automatically — colors, fonts, sizes all come from `theme`.
- Pairs with `*Props` escape hatches when you need to tweak something: `titleProps`, `textProps`, `quoteProps`, etc.

## Picking the right layout

| Intent                                              | Layout                             |
|-----------------------------------------------------|------------------------------------|
| "Just a full-bleed slide with my own content"       | `SlideLayout.Full`                 |
| Centered single block of content                   | `SlideLayout.Center`               |
| Two side-by-side panels                             | `SlideLayout.TwoColumn`            |
| Title + bulleted list                               | `SlideLayout.List`                 |
| A section divider ("Chapter 2")                     | `SlideLayout.Section`              |
| A single bold statement                             | `SlideLayout.Statement`            |
| A huge number or fact                               | `SlideLayout.BigFact`              |
| A quote + attribution                               | `SlideLayout.Quote`                |
| A code walkthrough with one block                   | `SlideLayout.Code`                 |
| Multiple code blocks in columns                     | `SlideLayout.MultiCodeLayout`      |
| Landscape image (hero)                              | `SlideLayout.HorizontalImage`      |
| Portrait image with a bulleted list next to it      | `SlideLayout.VerticalImage`        |
| Three images (one primary, two supporting)          | `SlideLayout.ThreeUpImage`         |
| Image filling the entire slide, no chrome           | `SlideLayout.FullBleedImage`       |

## Full reference

All of these are used like:

```tsx
import { Deck, SlideLayout, DefaultTemplate } from 'spectacle';

<Deck template={<DefaultTemplate />}>
  <SlideLayout.Center>
    <Heading>Hello</Heading>
  </SlideLayout.Center>
</Deck>
```

Note that `SlideLayout.*` components appear as direct children of `<Deck>` — they wrap a `<Slide>` internally. So **do not** put them inside a `<Slide>`.

### `SlideLayout.Full`
A vanilla slide passthrough. Use it when you want slide semantics but will handle the content yourself.

```tsx
<SlideLayout.Full backgroundColor="tertiary">
  <FlexBox height="100%" justifyContent="center" alignItems="center">
    <Heading>Custom layout territory</Heading>
  </FlexBox>
</SlideLayout.Full>
```

### `SlideLayout.Center`
One centered block of children.

```tsx
<SlideLayout.Center>
  <Heading>Welcome</Heading>
  <Text>Press → to begin</Text>
</SlideLayout.Center>
```

### `SlideLayout.TwoColumn`
Left and right panels via `left` and `right` props. Equal widths by default.

```tsx
<SlideLayout.TwoColumn
  left={
    <>
      <Heading>Before</Heading>
      <Text>Slow, manual, error-prone.</Text>
    </>
  }
  right={
    <>
      <Heading>After</Heading>
      <Text>Fast, automated, reliable.</Text>
    </>
  }
/>
```

### `SlideLayout.List`
Title + bulleted list. `items` is an array of `ReactNode`, so you can mix strings and components. `animateListItems` wraps each item in an `Appear`, so they reveal one-by-one on step.

```tsx
<SlideLayout.List
  title="Key results"
  animateListItems
  items={[
    '10x faster builds',
    <Text>50% fewer regressions</Text>,
    '$2M saved on infra',
  ]}
/>
```

Override the title or list visuals with `titleProps` / `listProps`:

```tsx
<SlideLayout.List
  title="Roadmap"
  titleProps={{ color: 'secondary', fontSize: '72px' }}
  listProps={{ color: 'primary' }}
  items={['Q1', 'Q2', 'Q3', 'Q4']}
/>
```

### `SlideLayout.Section`
A section-divider slide ("Chapter 3: The middle game"). Accepts `sectionProps` to tune typography.

```tsx
<SlideLayout.Section sectionProps={{ fontSize: '64px' }}>
  Chapter 3: The middle game
</SlideLayout.Section>
```

### `SlideLayout.Statement`
A single bold centered statement. Good for pacing — use after a busy slide to let the audience breathe.

```tsx
<SlideLayout.Statement statementProps={{ fontSize: '96px' }}>
  This is the only number that matters.
</SlideLayout.Statement>
```

### `SlideLayout.BigFact`
A headline number or short phrase, rendered at 250px by default, with optional supporting text below.

```tsx
<SlideLayout.BigFact factInformation="users in the first week">
  1,000,000
</SlideLayout.BigFact>
```

Shrink the fact or restyle it via `factFontSize`, `factProps`, `factInformationProps`.

### `SlideLayout.Quote`
Quote + attribution. Both required.

```tsx
<SlideLayout.Quote attribution="Grace Hopper">
  The most dangerous phrase in the language is "we've always done it this way."
</SlideLayout.Quote>
```

Use `quoteProps` / `attributionProps` to tune sizes. For very long quotes, bump the font size down — the default is sized for pull quotes, not paragraphs.

### `SlideLayout.Code`
A single code block with an optional title.

```tsx
<SlideLayout.Code
  language="typescript"
  title="The happy path"
>
  {`
  async function getUser(id: string) {
    const user = await db.users.findById(id);
    if (!user) throw new NotFound();
    return user;
  }
  `}
</SlideLayout.Code>
```

Use `codePaneProps` to pass through to the underlying `CodePane` — e.g., `{ highlightRanges: [[2, 4]] }` or `{ theme: tomorrow }`. See [code-panes.md](code-panes.md).

### `SlideLayout.MultiCodeLayout`
Multiple code blocks, each with an optional description. Use for before/after or comparison slides.

```tsx
<SlideLayout.MultiCodeLayout
  title="Before and after"
  numColumns={2}
  codeBlocks={[
    {
      code: `const users = data.filter(u => u.active);`,
      language: 'javascript',
      description: 'Before'
    },
    {
      code: `const users = data.filter(isActive);`,
      language: 'javascript',
      description: 'After'
    }
  ]}
/>
```

### Image layouts

All image layouts require `src` and `alt`. `objectFit` defaults to `cover`; set it to `contain` when the image shouldn't crop.

```tsx
<SlideLayout.HorizontalImage
  src="/images/graph.png"
  alt="Q4 revenue trending up"
  title="Q4 results"
  description="Revenue grew 18% QoQ"
/>

<SlideLayout.VerticalImage
  src="/images/team.jpg"
  alt="The team on launch day"
  position="left"
  title="Launch team"
  listItems={['12 engineers', '3 designers', '1 dog']}
  animateListItems
/>

<SlideLayout.ThreeUpImage
  primary={{ src: '/hero.jpg', alt: 'Hero', position: 'left' }}
  top={{ src: '/thumb1.jpg', alt: 'Detail 1' }}
  bottom={{ src: '/thumb2.jpg', alt: 'Detail 2' }}
/>

<SlideLayout.FullBleedImage
  src="/images/skyline.jpg"
  alt="Night skyline"
/>
```

For a full-bleed image with overlaid text, use `SlideLayout.FullBleedImage` and then absolutely position a `Box` over it via a custom approach, or use a `Slide` with `backgroundImage` and layout children normally — the latter is usually simpler if you need text on top.

## When to escape to a custom layout

Reach for `FlexBox` / `Grid` / `Box` directly (inside a plain `<Slide>`) when:

1. You need more than two columns of mixed content and none of the preset multi-column layouts fit.
2. You want text overlaid on top of an image with custom positioning.
3. You're building a slide that needs absolute positioning or a bespoke grid.
4. A chart or visualization needs to occupy a specific region with other content flowing around it.

See [typography-and-layout.md](typography-and-layout.md) for the primitives.
