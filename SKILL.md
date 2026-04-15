---
name: spectacle-best-practices
description: Use when writing, reviewing, or debugging Spectacle (FormidableLabs / Nearform) React presentation decks — any mention of Spectacle, create-spectacle, Deck, Slide, SlideLayout, CodePane, Appear, Stepper, MarkdownSlide, MarkdownSlideSet, presenter mode, or exporting slides to PDF. Also triggers on React-based slide decks or "how do I make a presentation in React" even when Spectacle isn't named explicitly, since Spectacle is the go-to library. Covers setup, theming, animations, code panes, markdown slides, transitions, presenter/export modes, and common pitfalls for Spectacle v10.
---

# Spectacle Best Practices

This skill is a reference map for building presentations with [Spectacle](https://github.com/FormidableLabs/spectacle), the React-based presentation library by FormidableLabs / Nearform. It targets Spectacle v10.2.3 and defaults to React/JSX authoring (richer than Markdown mode for live demos, custom layouts, and interactive steppers), but cross-references the Markdown path when it's the better fit.

## How to use this skill

Spectacle is a small-surface library but has sharp edges — styled-system theme keys, an unusual step-sequence model, a templating layer that looks like slots, and several "same name, different meaning" components (`Markdown`/`MarkdownSlide`/`MarkdownSlideSet`). When helping the user, skim the activity table below, open the relevant reference(s), and apply what you learn. Don't guess — the reference files contain exact prop names, import paths, and working examples.

A few load-bearing principles to keep in mind while you read:

- **Prefer built-in `SlideLayout.*` over hand-rolled flexbox** whenever a match exists. It's fewer lines and respects the deck's theme automatically.
- **Reach for `Appear` or `Stepper` before `useSteps`.** The hook exists for escape-hatch cases; the wrappers cover ~95% of needs.
- **Theme via the `theme` prop**, not per-component hardcoded colors. Spectacle's styled-system mapping is the whole reason it looks consistent.
- **Write slides in JSX unless the user has a reason to stay in Markdown.** JSX gets you live React components, richer animations, and typed props. Markdown authoring is great for writer-heavy decks but loses the interactive features.
- **NextJS App Router users must render Spectacle inside a client component** (`'use client'` directive), otherwise hydration errors will bite.

## Activity-Based Reference Guide

Consult these references based on what you're actually doing:

### Starting a new deck or project

| Activity                                                         | Reference                                                  |
|------------------------------------------------------------------|------------------------------------------------------------|
| Bootstrapping a new Spectacle project (CLI, templates, install)  | [getting-started.md](references/getting-started.md)        |
| Picking between one-page / markdown / Vite / webpack templates   | [getting-started.md](references/getting-started.md)        |
| Setting up Spectacle inside an existing React/Next.js/Vite app   | [getting-started.md](references/getting-started.md)        |
| Writing the first `Deck` + `Slide` + `DefaultTemplate`           | [deck-and-slides.md](references/deck-and-slides.md)        |
| Grabbing a copy-pasteable starter deck                           | [snippets.md](references/snippets.md)                      |

### Structuring slides

| Activity                                                                      | Reference                                                |
|-------------------------------------------------------------------------------|----------------------------------------------------------|
| Choosing a layout for a new slide (title, two-column, quote, big fact, etc.)  | [slide-layouts.md](references/slide-layouts.md)          |
| Using `SlideLayout.TwoColumn`, `.BigFact`, `.Quote`, `.Statement`, `.Section` | [slide-layouts.md](references/slide-layouts.md)          |
| Image-heavy slides (`HorizontalImage`, `VerticalImage`, `FullBleedImage`)     | [slide-layouts.md](references/slide-layouts.md)          |
| Hand-rolling a layout with `FlexBox` / `Grid` / `Box` when no preset fits     | [typography-and-layout.md](references/typography-and-layout.md) |
| Typography: `Heading`, `Text`, `FitText`, lists, `Quote`, `Link`              | [typography-and-layout.md](references/typography-and-layout.md) |
| Tables with `Table` / `TableHeader` / `TableRow` / `TableCell`                | [typography-and-layout.md](references/typography-and-layout.md) |
| Deck-wide templates (footers, slide numbers, branding overlays)               | [deck-and-slides.md](references/deck-and-slides.md)      |

### Code, animation, and stepping

| Activity                                                                 | Reference                                                       |
|--------------------------------------------------------------------------|-----------------------------------------------------------------|
| Syntax-highlighted code with `CodePane`, picking a Prism theme           | [code-panes.md](references/code-panes.md)                       |
| Line-by-line code reveals with `highlightRanges`                         | [code-panes.md](references/code-panes.md)                       |
| Multiple code blocks on one slide (`SlideLayout.MultiCodeLayout`)        | [code-panes.md](references/code-panes.md)                       |
| Revealing bullets / elements one-at-a-time with `Appear`                 | [animations.md](references/animations.md)                       |
| Stepping through a sequence of values with `Stepper`                     | [animations.md](references/animations.md)                       |
| Building a custom animated component with `useSteps`                     | [animations.md](references/animations.md)                       |
| Per-slide or per-deck transition effects (slide, fade, custom)           | [deck-and-slides.md](references/deck-and-slides.md)             |

### Theming and visuals

| Activity                                                                     | Reference                                                  |
|------------------------------------------------------------------------------|------------------------------------------------------------|
| Writing a custom theme (`colors`, `fontSizes`, `space`, `fonts`, etc.)       | [themes.md](references/themes.md)                          |
| Understanding the scaled `space` array and default margin indices            | [themes.md](references/themes.md)                          |
| Using a ready-made theme (corporate, dark, playful, editorial)               | [theme-cookbook.md](references/theme-cookbook.md)          |
| Styled-system base props (`Space`, `Color`, `Layout`, `Flex`, `Border`, etc.)| [typography-and-layout.md](references/typography-and-layout.md) |
| Backgrounds, full-bleed images, slide `backgroundImage`/`backgroundColor`    | [slide-layouts.md](references/slide-layouts.md)            |
| Customizing `DefaultTemplate` color or building your own template            | [deck-and-slides.md](references/deck-and-slides.md)        |

### Markdown-authored slides

| Activity                                                                     | Reference                                                  |
|------------------------------------------------------------------------------|------------------------------------------------------------|
| Mixing a block of markdown inside a JSX slide (`Markdown`)                   | [markdown-slides.md](references/markdown-slides.md)        |
| Authoring a whole slide as markdown (`MarkdownSlide`)                        | [markdown-slides.md](references/markdown-slides.md)        |
| Authoring many slides in one markdown blob (`MarkdownSlideSet`)              | [markdown-slides.md](references/markdown-slides.md)        |
| Presenter notes from markdown (`Notes:` marker)                              | [markdown-slides.md](references/markdown-slides.md)        |
| Column / center layouts inside `.md` files (JSON delimiter annotations)      | [markdown-slides.md](references/markdown-slides.md)        |
| Migrating from pre-v7 `<Markdown containsSlides />`                          | [markdown-slides.md](references/markdown-slides.md)        |

### Presenting, exporting, and delivery

| Activity                                                                     | Reference                                                  |
|------------------------------------------------------------------------------|------------------------------------------------------------|
| Keyboard shortcuts and the command bar (⌘/Ctrl + K)                          | [presenter-and-export.md](references/presenter-and-export.md) |
| Turning on presenter mode (notes, timer, next slide)                         | [presenter-and-export.md](references/presenter-and-export.md) |
| Exporting the deck to PDF (`?exportMode=true`)                               | [presenter-and-export.md](references/presenter-and-export.md) |
| Printer-friendly B&W output (`?exportMode=true&printMode=true`)              | [presenter-and-export.md](references/presenter-and-export.md) |
| Authoring presenter notes with `<Notes>`                                     | [presenter-and-export.md](references/presenter-and-export.md) |
| Auto-playing / looping decks                                                 | [deck-and-slides.md](references/deck-and-slides.md)        |

### Troubleshooting and pitfalls

| Activity                                                                     | Reference                                                  |
|------------------------------------------------------------------------------|------------------------------------------------------------|
| NextJS App Router hydration errors                                           | [anti-patterns.md](references/anti-patterns.md)            |
| `Markdown` vs `MarkdownSlide` vs `MarkdownSlideSet` confusion                | [anti-patterns.md](references/anti-patterns.md), [markdown-slides.md](references/markdown-slides.md) |
| `highlightRanges` eating too many slide steps                                | [anti-patterns.md](references/anti-patterns.md), [code-panes.md](references/code-panes.md) |
| Slides overflowing the viewport / text not scaling                           | [anti-patterns.md](references/anti-patterns.md)            |
| `Appear` components not revealing (priority / ordering issues)               | [anti-patterns.md](references/anti-patterns.md), [animations.md](references/animations.md) |
| Custom fonts not loading                                                     | [anti-patterns.md](references/anti-patterns.md)            |
| Theme values not applying (wrong key, wrong level of nesting)                | [anti-patterns.md](references/anti-patterns.md), [themes.md](references/themes.md) |

## Starter snippets

When the user needs a quick copy-paste, jump straight to [snippets.md](references/snippets.md) — it contains standalone examples for: minimal deck, themed deck, title slide, two-column content slide, code walkthrough slide, big-fact slide, image slide, animated bullet list, and a deck-wide template with slide numbers.

## Non-obvious things worth knowing

- **Every `highlightRanges` entry is a step.** A `CodePane` with `highlightRanges={[[1,3], [5,7]]}` adds two steps to that slide's sequence. This matters for `Appear` ordering and for estimating "how many taps to advance this slide."
- **`space` is an array, and `Slide` defaults to index 2 (32px), `Heading` to index 1 (24px), `Text` to index 0 (16px).** Overriding `theme.space` with fewer than 3 entries silently pushes components to `0`.
- **Numeric `width`/`height` props**: fractions (`1/2`) become percents; integers become pixels; strings pass through. So `width={256}` is `256px`, `width="256"` is invalid CSS, and `width="1/2"` does not work — use `width={1/2}`.
- **`Notes` only renders in presenter mode.** It's a silent `null` in the audience view, so "my notes aren't showing up" is usually "you didn't open presenter mode."
- **`.md` layout annotations (`{ "layout": "columns" }`) only work in `.md` files, not `.mdx`.** In MDX you compose JSX directly.
- **TypeScript ships with the package.** No separate `@types/spectacle` needed — in fact, installing one would be wrong.

When in doubt, read the actual reference file rather than guessing from memory — Spectacle has enough small footguns that recalling "roughly how it works" leads to subtle bugs.
