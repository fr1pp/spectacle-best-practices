# Markdown-Authored Slides

Spectacle supports three related-but-distinct markdown components. The naming is a documented gotcha — pick the right one for your situation.

## The three components

| Component          | Lives inside     | Contains            | Produces                 |
|--------------------|------------------|---------------------|--------------------------|
| `Markdown`         | `<Slide>`        | Markdown string     | Inline markdown block    |
| `MarkdownSlide`    | `<Deck>`         | Markdown string     | A single full slide      |
| `MarkdownSlideSet` | `<Deck>`         | Markdown string w/ `---` delimiters | Multiple slides |

### `Markdown` — an inline block inside a slide

Use when you want a chunk of markdown embedded inside an otherwise-JSX slide. Great for mixing a written paragraph with interactive React components.

```tsx
<Slide>
  <Markdown>
    {`
    # Urql

    A highly customizable and versatile GraphQL client
    `}
  </Markdown>
  <LiveDemo /> {/* a React component */}
</Slide>
```

### `MarkdownSlide` — one full slide in markdown

Use when one specific slide is easier to write in markdown but the rest of the deck is JSX.

```tsx
<Deck template={<DefaultTemplate />}>
  <Slide><Heading>Intro</Heading></Slide>

  <MarkdownSlide animateListItems>
    {`
    # Use Markdown to write a slide

    This is a single slide composed using Markdown.

    - It uses the \`animateListItems\` prop so...
    - its list items...
    - will animate in, one at a time.
    `}
  </MarkdownSlide>

  <Slide><Heading>Next</Heading></Slide>
</Deck>
```

### `MarkdownSlideSet` — many slides in one markdown blob

Use when you want to author several slides (or the entire deck) in markdown. The `---` (three dashes on their own line) delimits slides inside the blob.

```tsx
<MarkdownSlideSet>
  {`
  # Slide 1

  Here's some content.

  ---

  # Slide 2

  Using the \`---\` delimiter creates a new slide.

  Notes: Presenter notes go here — only visible in presenter mode.
  `}
</MarkdownSlideSet>
```

`MarkdownSlideSet` is also what the **Markdown** `create-spectacle` template uses: it imports a `.md` file as a string and passes it to `<MarkdownSlideSet>`.

## Props (all three components)

| Prop               | Type      | Purpose                                                                 |
|--------------------|-----------|-------------------------------------------------------------------------|
| `children`         | `string`  | The markdown content                                                    |
| `componentProps`   | `object`  | Props to pass to components rendered from markdown tags (e.g. `color`)  |
| `animateListItems` | `boolean` | Wrap list items in `Appear` so they reveal one per tap                  |
| `Layout` props     | —         | Width, height, display                                                  |
| `Position` props   | —         | Position, top, left, right, bottom                                      |

`componentProps` is useful when you want to style all the typography in a markdown slide at once:

```tsx
<MarkdownSlide componentProps={{ color: 'secondary' }}>
  {`
  # All text on this slide is secondary-colored

  Including this paragraph.
  `}
</MarkdownSlide>
```

## Presenter notes in markdown

Inside any markdown content, a line starting with `Notes:` is interpreted as presenter notes for the current slide. The notes render only in presenter mode; the audience view treats them as invisible.

```markdown
# What to say

The first question is whether this is worth doing at all.

Notes: Pause here and look at the room. If nobody reacts, skip to slide 8.
```

## Markdown-level layout annotations (`.md` files only)

When you're authoring a deck as a `.md` file (as in the CLI's Markdown template), you can annotate slide delimiters with a JSON config to pick a layout:

```md
--- { "layout": "columns" }

::section

![Gastly](gastly.png)

::section

![Haunter](haunter.png)

---

# Regular slide
```

Two layout types are supported:

- `"columns"`: splits content into columns by `::section` delimiters. Each section becomes one column.
- `"center"`: vertically and horizontally centers all content in the slide.

**Annotations only work in `.md` files.** They don't work in `.mdx` (where you'd just use JSX directly) or when passing markdown as a string to `<MarkdownSlideSet>` inside JSX (there, you're using `MarkdownSlideSet`'s simpler format, which doesn't support annotations).

### Center layout example

```md
--- { "layout": "center" }

# Gengar

Gengar is a dark purple, bipedal Pokémon with a roundish body.
```

Under the hood this wraps the slide's content in:

```tsx
<FlexBox justifyContent="center" alignItems="center" height="100%">
  {content}
</FlexBox>
```

### Columns layout example

```md
--- { "layout" : "columns" }

::section

![Gastly](gastly.png)

::section

![Haunter](haunter.png)
```

Under the hood:

```tsx
<FlexBox flexDirection="row" alignItems="start" flex={1}>
  {sectionsArray}
</FlexBox>
```

## v7 migration — if the user is on an old version

Before Spectacle v7, there was only a single `<Markdown>` component that did three different things depending on where it was used. The current split is:

| Old (pre-v7)                          | New (v7+)              |
|---------------------------------------|------------------------|
| `<Slide><Markdown /></Slide>`         | `<Slide><Markdown /></Slide>` (unchanged) |
| `<Markdown />` as a full slide        | `<MarkdownSlide />`    |
| `<Markdown containsSlides />`         | `<MarkdownSlideSet />` |

If you see `containsSlides`, the user is reading outdated docs — rename to `MarkdownSlideSet` and drop the prop.

## When to pick markdown vs JSX slides

**Pick JSX** when:
- The deck has interactive React components (live demos, charts, custom interactions)
- You need per-slide custom layouts that don't match a built-in preset
- You want fine-grained control over `Appear` / `Stepper` placement
- The deck has lots of code walkthroughs with `highlightRanges`

**Pick Markdown** when:
- Content is mostly text and bulleted lists
- Non-technical authors will edit it
- You want presenter notes inline with slide content
- The deck is writer-heavy and React interactivity isn't needed

You can mix: write most slides in JSX and drop `<MarkdownSlide>` in wherever a slide is simple enough. The choice is per-slide, not per-deck.

## Gotchas

- **Escape backticks inside template strings.** Markdown often contains backtick-fenced code, and if you're passing the markdown as a JS template literal (`{`...`}`), inner backticks need escaping: `` \`useSteps\` ``.
- **`Notes:` must be on its own line** (or starting its own line). Inline `Notes:` in the middle of a paragraph isn't picked up.
- **Do not nest `<MarkdownSlide>` inside `<Slide>`.** `MarkdownSlide` is a `Slide` replacement, not a child of one.
- **`MarkdownSlideSet` requires exact `---` on its own line** to delimit slides. Indented or trailing-space dashes are treated as content.
- **`.md` layout annotations are whitespace-sensitive.** The JSON blob must be on the same line as the `---`, with a single space separating them: `--- { "layout": "center" }`.
