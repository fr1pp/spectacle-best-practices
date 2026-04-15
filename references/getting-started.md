# Getting Started with Spectacle

Spectacle is a React-based presentation library that lets you author slides with JSX (or Markdown), live-demo code, and use any React library inside a slide. This file covers installation, picking a starter template, and the minimum viable deck.

## Installation paths

There are two practical ways to start a Spectacle project: use the official CLI, or add `spectacle` to an existing React app. The CLI is the recommended path for a fresh deck because it wires up everything correctly (bundler, entry file, default template, theme).

### Path 1: `create-spectacle` CLI (recommended for a fresh deck)

```bash
npx create-spectacle
# or
pnpm dlx create-spectacle
```

The CLI prompts for a deck name and a template. There are four templates, and the right choice depends on what the user plans to do inside the deck:

| Template                  | Good fit for                                                                                                    | Notes |
|---------------------------|-----------------------------------------------------------------------------------------------------------------|-------|
| **⚡️ One Page**          | Throwaway decks, demos, anything you want in a single HTML file with no build step                             | Uses [htm](https://github.com/developit/htm) instead of JSX — tag-template literal syntax like `html\`<${Deck}>...\`` |
| **📄 Markdown**           | Writer-heavy decks where the author wants to live in markdown and not touch JSX                                | React app + webpack, imports a `.md` file via `MarkdownSlideSet`. Supports md-level layout annotations (`{ "layout": "columns" }`). |
| **🏗 React + Vite**        | The default choice for any "real" deck that needs npm libraries, TypeScript, or interactive demos              | Fast HMR, TypeScript out of the box, best DX for live coding a component mid-slide |
| **🏗 React + webpack**     | Orgs on a webpack-standardized toolchain, or anyone who needs a specific webpack plugin                        | Functionally equivalent to the Vite template, just slower to build |

If the user hasn't said which one they want, **default to React + Vite**. It's the most flexible and the fastest to iterate on.

### Path 2: Manual install into an existing app

```bash
npm add spectacle
# or yarn / pnpm equivalent
```

Then, in any entry or page component:

```tsx
import { Deck, Slide, Heading, DefaultTemplate } from 'spectacle';

function App() {
  return (
    <Deck template={<DefaultTemplate />}>
      <Slide>
        <Heading>Welcome to Spectacle</Heading>
      </Slide>
    </Deck>
  );
}

export default App;
```

That's the entire minimum — a `Deck` wraps everything, a `Slide` wraps one "view," and `DefaultTemplate` provides the full-screen toggle and progress dots overlaid on every slide.

### NextJS App Router — the one caveat

If the user is on NextJS 13+ with the App Router, **Spectacle must render inside a client component.** Spectacle uses `window`, `react-spring`, and styled-system at render time — none of which survive server rendering.

Add the directive at the top of the file that imports Spectacle, or wrap the deck in a client-only component:

```tsx
'use client';

import { Deck, Slide, Heading, DefaultTemplate } from 'spectacle';

export default function DeckPage() {
  return (
    <Deck template={<DefaultTemplate />}>
      <Slide>
        <Heading>Hello from App Router</Heading>
      </Slide>
    </Deck>
  );
}
```

Pages Router (pre-App-Router Next) works without any directive — everything is client-rendered already.

## What you get for free

Once you have the minimum deck running in the browser, you automatically get:

- **Keyboard navigation**: ← / → move between slides
- **The command bar**: ⌘/Ctrl + K surfaces every shortcut
- **Overview mode**: Alt/⌥ + Shift + O shows all slides at once
- **Presenter mode**: Alt/⌥ + Shift + P opens a second view with notes, timer, and upcoming slide
- **Fullscreen toggle**: Alt/⌥ + Shift + F, or the button in `DefaultTemplate`
- **PDF export**: append `?exportMode=true` to the URL and use the browser's "Save as PDF"
- **Printer-friendly mode**: combine with `?exportMode=true&printMode=true` for B&W flattened output

See [presenter-and-export.md](presenter-and-export.md) for more detail on these.

## What doesn't come for free

The default deck is intentionally bare. You'll want to:

1. **Supply a theme** via the `theme` prop on `Deck` — see [themes.md](themes.md) or pick one from [theme-cookbook.md](theme-cookbook.md).
2. **Add presenter notes** with `<Notes>` inside each slide — see [presenter-and-export.md](presenter-and-export.md).
3. **Pick slide layouts** instead of hand-rolling every slide — see [slide-layouts.md](slide-layouts.md).

## Installing add-ons the user may ask about

- **`spectacle-cli`** — the same CLI that powers `create-spectacle`, exposing `spectacle` and `spectacle-boilerplate` commands. Only needed if the user wants to scaffold decks from a script.
- **`spectacle-mdx-loader`** — a webpack MDX loader that lets you write slides in `.mdx` with full JSX access. Use this if the user wants markdown authoring AND custom components, since Spectacle's built-in `MarkdownSlide`/`MarkdownSlideSet` only work with `.md`.

## TypeScript

Types are bundled with the `spectacle` package — no separate `@types/spectacle` exists (and installing one would be wrong). TypeScript is the default language for the CLI templates, so nothing extra is needed.
