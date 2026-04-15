# Presenting, Exporting, and Delivery

Spectacle decks are just React apps, so you run them in the browser and control them with the keyboard. Presenter mode, PDF export, and B&W print mode all come for free — you just need to know the URL parameters and keyboard shortcuts.

## Keyboard shortcuts

These work in both audience and presenter views.

| Shortcut                                              | Action                              |
|-------------------------------------------------------|-------------------------------------|
| `→`                                                   | Next slide (or next step)           |
| `←`                                                   | Previous slide (or previous step)   |
| `⌘/Ctrl` + `K`                                        | Open the command bar                |
| `⌥/Alt` + `Shift` + `O`                               | Toggle overview mode                |
| `⌥/Alt` + `Shift` + `P`                               | Toggle presenter mode               |
| `⌥/Alt` + `Shift` + `F`                               | Toggle fullscreen mode              |

### The command bar

`⌘/Ctrl + K` opens a searchable command palette showing every available shortcut and navigation option. This is the discoverability surface — if a user asks "how do I do X in Spectacle," opening the command bar is often the fastest path to the answer.

### Overview mode

`⌥/Alt + Shift + O` switches to a grid view showing every slide at once. Click any slide to jump to it. Useful for navigating a long deck during Q&A.

### Presenter mode

`⌥/Alt + Shift + P` opens presenter mode, which displays:

- The current slide
- The next slide
- Presenter notes (anything inside `<Notes>` on the current slide)
- A timer

The intended workflow is to open presenter mode in one browser window (on your laptop screen) and the regular deck URL in another window (on the projector / external display). You control both from your laptop — keyboard navigation advances both in sync.

You can also trigger presenter mode via URL: append `?presenterMode=true` to the deck URL.

## Writing presenter notes

Add `<Notes>` as a child of any slide. The content renders only in presenter mode:

```tsx
<Slide>
  <Heading>Urql</Heading>
  <Text>A highly customizable and versatile GraphQL client</Text>
  <Notes>
    Urql is a GraphQL client that exposes a set of React components and hooks.
    Emphasize that it was designed to be easier to use than Apollo.
    Pause here to gauge the room's familiarity with GraphQL.
  </Notes>
</Slide>
```

`<Notes>` accepts a string of children. For markdown-authored slides, use the `Notes:` marker on its own line inside the markdown content — see [markdown-slides.md](markdown-slides.md#presenter-notes-in-markdown).

**`<Notes>` is a null in audience view.** It renders nothing, so you can stash arbitrary prose inside it without worrying about layout.

## Query parameters

Append these to the deck URL with `?param=true`, combined with `&`.

| Parameter       | Purpose                                                                                 |
|-----------------|-----------------------------------------------------------------------------------------|
| `exportMode`    | Flattens the deck into a single scrollable page so the browser can "Save as PDF"        |
| `printMode`     | Turns the deck into a printer-friendly black-and-white layout (use with `exportMode`)   |
| `presenterMode` | Opens presenter view directly (alternative to the keyboard shortcut)                    |

Combine them with `&`:

```
http://localhost:3000/?exportMode=true&printMode=true
```

### Exporting to PDF

The export flow is:

1. Start the dev server (`npm run dev`, `vite`, etc.) or build and serve.
2. Open the deck with `?exportMode=true`. The deck flattens into one long scrollable page where each slide is a fixed-aspect region.
3. Use the browser's Print dialog → "Save as PDF."
4. **Chrome/Chromium produces the best fit.** It fully honors Spectacle's custom `pageSize` CSS. Firefox and Safari will export but won't match the slide size exactly.

### Sizing the exported PDF

The `pageSize`, `pageOrientation`, and `printScale` props on `<Deck>` control how the PDF fits the page:

- `pageSize` defaults to `"13.66in 7.68in"` (16:9 widescreen at standard slide size).
- `pageOrientation` defaults to `"landscape"`.
- `printScale` defaults to `0.959`. This is the ratio that makes the slide content fit cleanly inside the page bounds. **Leave it alone unless the PDF is visibly clipped or under-filled.**

If you need a different paper size (US Letter, A4), pass a matching `pageSize`:

```tsx
<Deck
  template={<DefaultTemplate />}
  pageSize="11in 8.5in"
  pageOrientation="landscape"
>
```

### Black-and-white handouts

For a printer-friendly version:

```
?exportMode=true&printMode=true
```

This strips backgrounds and recolors text to B&W. Useful for classroom handouts or archival printouts.

## Auto-play / kiosk mode

For unattended decks (trade show booths, looping demos in lobbies):

```tsx
<Deck
  autoPlay
  autoPlayLoop
  autoPlayInterval={8000}
  template={<DefaultTemplate />}
>
```

- `autoPlay` starts the timed advance.
- `autoPlayLoop` restarts from slide 1 when the deck reaches the end.
- `autoPlayInterval` is in milliseconds. The default `1000` (1 second) is almost always too fast; 5000–10000 is a reasonable kiosk pace.

Keyboard input still works while auto-play is on — a stray key press interrupts the timer. For a true "cannot be interrupted" kiosk, you'd need to hide the keyboard or wrap the deck in a `pointer-events: none` layer, which is outside Spectacle's scope.

## Deploying a deck

Since Spectacle decks are just React apps, any static host works: Vercel, Netlify, Cloudflare Pages, GitHub Pages, S3 + CloudFront, plain `nginx`. The build output is plain HTML + JS + CSS.

Common variants:

- **Vite template** → `npm run build` → `dist/` → deploy `dist/`
- **Webpack template** → `npm run build` → `build/` or `dist/` → deploy that folder
- **One-page template** → the single HTML file is the whole deck; host it anywhere

There's no server-side rendering — the deck hydrates on the client. If you embed Spectacle inside a larger app (e.g., a Next.js site), see [getting-started.md](getting-started.md#nextjs-app-router--the-one-caveat) for the `'use client'` requirement.

## Live-sharing a deck during a talk

Two options:

1. **Share your browser window** over Zoom/Meet/Teams, run the deck locally, control with the keyboard. Simplest.
2. **Deploy the deck** (e.g., Vercel), send the link, and control it from your laptop — audience sees it in their own browser.

Option 2 is nice for async viewers but you lose presenter mode's "next slide preview" feature since the audience sees exactly what you see. For hybrid setups, run the audience URL on a projector and presenter mode on your laptop.
