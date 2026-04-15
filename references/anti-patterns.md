# Anti-Patterns and Common Fixes

This file is the "why isn't my deck doing what I expect" reference. Each section pairs a common mistake with the explanation and the fix.

## NextJS App Router hydration errors

**Symptom**: The deck works in dev, but the production build throws a hydration mismatch, or logs `ReferenceError: window is not defined` during server render.

**Cause**: Spectacle uses `window`, `react-spring`, and styled-system at render time. None of these work during Next's server-side rendering in the App Router.

**Fix**: Add `'use client'` at the top of the file that imports Spectacle, or wrap the deck in a client-only component:

```tsx
'use client';

import { Deck, Slide, Heading, DefaultTemplate } from 'spectacle';

export default function DeckPage() {
  return (
    <Deck template={<DefaultTemplate />}>
      <Slide>
        <Heading>Client-rendered</Heading>
      </Slide>
    </Deck>
  );
}
```

Pages Router (`pages/*`) doesn't need this — the whole component tree is already client-rendered.

If you're using `next/dynamic`, you can also disable SSR for the component:

```tsx
import dynamic from 'next/dynamic';

const Deck = dynamic(() => import('./MyDeck'), { ssr: false });
```

## `Markdown` vs `MarkdownSlide` vs `MarkdownSlideSet` confusion

**Symptom**: Markdown content renders as a weird oversized or undersized block, or the deck errors with "cannot be a child of Slide."

**Cause**: The three markdown components have different parent expectations:

- `<Markdown>` → child of `<Slide>`
- `<MarkdownSlide>` → child of `<Deck>` (replaces `<Slide>`)
- `<MarkdownSlideSet>` → child of `<Deck>`, contains multiple slides

Putting a `MarkdownSlide` inside a `Slide` double-wraps it. Putting a `Markdown` directly inside a `Deck` fails because it's not a slide.

**Fix**: Pick the right component per the [markdown-slides.md](markdown-slides.md) table. When in doubt: if you want one markdown thing inside a JSX slide, use `<Markdown>`; if you want one whole slide in markdown, use `<MarkdownSlide>`; if you want several, use `<MarkdownSlideSet>`.

## `highlightRanges` eating too many slide steps

**Symptom**: You expect 3 taps to advance the slide, but it takes 7, and your `Appear` components fire much later than expected.

**Cause**: Every entry in `highlightRanges` is a slide step. `highlightRanges={[[1,3], [5,7], [9,11]]}` adds **three** steps to the slide, not one.

**Fix**: Either shorten `highlightRanges` to only the ranges you actually want to step through, or accept that the `Appear`s come after. If you want a static highlight that doesn't step, pass a single two-number array:

```tsx
// Static — one highlight, no stepping
<CodePane language="js" highlightRanges={[1, 3]}>...

// Stepping — three steps
<CodePane language="js" highlightRanges={[[1, 3], [5, 7], [9, 11]]}>...
```

See [code-panes.md](code-panes.md#line-highlighting-with-highlightranges) for the full model.

## Slides overflowing the viewport / text not scaling

**Symptom**: Long text spills past the edge of the slide, or fills a tiny portion of it.

**Cause**: Spectacle scales each slide to fit the viewport based on the deck's slide dimensions (default `13.66in × 7.68in`). But the *content* inside a slide doesn't auto-shrink. If you cram a 400-word paragraph into a `Text` component with default size, it overflows.

**Fixes**:

1. **Use `FitText` for single-line phrases** that must fit the width — it auto-scales.
2. **Reduce content per slide.** A presentation slide should hold one idea; if you're hitting overflow, split the slide.
3. **Use smaller font sizes** via the theme. A `text` fontSize of `20px` fits much more than `32px`.
4. **Override slide `scaleRatio`** to render the slide larger (which then scales down to fit the viewport). This keeps the proportions but gives more room.
5. **Use `SlideLayout.BigFact`, `.Statement`, or `.Quote`** instead of hand-rolling. They handle sizing correctly for their content type.

## `Appear` components not revealing

**Symptom**: Taps advance to the next slide without showing the `Appear` elements.

**Causes** (in order of frequency):

1. **The `Appear` is nested inside another `Appear` that hasn't activated yet.** Inner `Appear`s never become visible until their parent does, and the parent's "visible" state has to be reached first.

2. **A CSS rule with `!important` overrides the inline styles** Spectacle sets via `react-spring`. Check for `.my-class { opacity: 1 !important; }` or similar.

3. **`animateListItems` is combined with manual `<Appear>` around list items**, causing double-wrapping that eats steps.

4. **Priority ordering is fighting render order.** If you've set `priority` on some participants but not others, the defaults might fire first.

**Fix**: Flatten nested `Appear`s, remove `!important`, pick one of `animateListItems`/manual `Appear`, and reset custom priorities if you're not sure what they're doing.

## Custom fonts not loading

**Symptom**: Typography falls back to a system font (Times, Helvetica, etc.) instead of the one you specified in `theme.fonts`.

**Causes**:

1. **The font file isn't loaded before Spectacle renders.** Browsers can't use a font that hasn't arrived yet. If your `<link rel="stylesheet">` for Google Fonts is below the bundle, the deck renders before the font is available.
2. **The `font-family` name in the theme doesn't match the CSS `font-family` declared by the font.** `"Inter"` and `"Inter, sans-serif"` are different — the former is a family name, the latter is a family list.
3. **The font is loaded but not in the weight/style you're requesting.** `fontWeight: 600` with a font that only has 400 and 700 fails to resolve.
4. **CORS blocks the font file.** Self-hosted fonts on a different origin need `Access-Control-Allow-Origin`.

**Fixes**:

- Load the font via `<link rel="preload" as="font" crossorigin>` in your HTML head.
- Match the `font-family` name exactly as the font file declares it.
- Load all needed weights/styles: `family=Inter:wght@400;600;700`.
- For self-hosted fonts, configure CORS or host them same-origin.

## Theme values not applying

**Symptom**: `backgroundColor="primary"` renders the literal string `primary` (or browser default) instead of the color.

**Causes**:

1. **The theme is missing the key.** `theme.colors.primary` isn't defined, so Spectacle falls back to the raw string which isn't valid CSS.
2. **The theme object is nested wrong.** Styled-system expects `theme.colors.primary`, not `theme.primary.color`.
3. **The theme wasn't passed to `Deck`.** A standalone theme object in a file somewhere has no effect until you do `<Deck theme={theme}>`.
4. **You're using a key name that collides with a CSS value.** A theme key of `'red'` shadows the CSS color `red` — the theme wins, but if you typed `color="red"` expecting the CSS color, you get the theme color.

**Fix**: Check the theme is passed to `Deck`, check the key exists at the right depth, and rename keys that collide with CSS values.

## `Notes` not visible

**Symptom**: `<Notes>Things to say</Notes>` doesn't appear anywhere.

**Cause**: `<Notes>` is intentionally invisible in audience view. It only renders in presenter mode.

**Fix**: Open presenter mode (`⌥/Alt + Shift + P` or `?presenterMode=true`). The notes appear in the presenter panel alongside the current slide.

## Images not loading in `Image` or `SlideLayout.*Image`

**Symptom**: The image slot is empty or shows a broken-image icon.

**Causes**:

1. **The path is wrong** — relative paths resolve against the HTML, not the JS bundle.
2. **The image isn't in the public directory** (Vite: `public/`, webpack: `public/` or `assets/`).
3. **The image is imported incorrectly.** For bundled images:
   ```tsx
   import heroImg from './hero.jpg';
   <Image src={heroImg} />
   ```
   vs. for public-path images:
   ```tsx
   <Image src="/hero.jpg" />
   ```

**Fix**: Pick one strategy. For CLI templates, use `/relative/to/public/root` paths for static assets and `import` for assets colocated with components.

## Per-slide transitions don't seem to animate

**Symptom**: A slide with a custom `transition={...}` either stays stuck, flashes incorrectly, or behaves like the default.

**Causes**:

1. **Incomplete transition object.** You passed `{ from: { opacity: 0 } }` but not `enter` or `leave`. The missing keys default to `{}`, so the slide either starts hidden and never becomes visible or the leave animation is broken.
2. **Animating a property `react-spring` doesn't support.** Arbitrary CSS strings that can't be interpolated (like `margin: '0 auto'`) silently fail.
3. **The per-slide transition fully replaces the deck transition** — you can't merge them. If your deck has a fade and your slide has a slide-up, the slide-up completely replaces the fade.

**Fix**: Always provide all three keys (`from`, `enter`, `leave`), and stick to interpolable properties: `opacity`, `transform`, colors, numeric CSS values.

## `useSteps` never activates

**Symptom**: A component using `useSteps` shows `isActive: false` forever and never advances.

**Cause**: You forgot to render the `placeholder` DOM node. Spectacle uses it as a marker to detect the participant in the rendered output.

**Fix**:

```tsx
function MyComponent() {
  const { isActive, step, placeholder } = useSteps(3);

  return (
    <>
      {placeholder /* ← don't forget this */}
      <div>isActive: {String(isActive)}</div>
    </>
  );
}
```

## TypeScript errors about missing types

**Symptom**: `@types/spectacle` doesn't exist on npm; your IDE complains about types.

**Cause**: Spectacle ships its types directly in the `spectacle` package. There is no separate `@types/spectacle`.

**Fix**: Remove any `@types/spectacle` from `package.json`. Make sure `spectacle` is imported as a regular dependency; types come along for free.

## The deck builds fine but the browser shows a blank page

**Symptom**: Build succeeds, page loads, but no deck renders.

**Causes**:

1. **No slides inside the `Deck`** — an empty `<Deck></Deck>` renders nothing.
2. **An error in a slide's JSX** is being swallowed by error boundaries. Check the browser console.
3. **The root element isn't mounted**, e.g., `document.getElementById('root')` returns null because the DOM id changed.
4. **The `Deck` is rendered inside a component that's conditionally mounted and the condition is false.**

**Fix**: Open devtools, check the console for errors, and verify the root element id matches what your entry file mounts into.

## Performance: slides lagging during advance

**Symptom**: Advancing slides in a large deck feels sluggish, especially with lots of `Appear`s or `CodePane`s with big `highlightRanges` arrays.

**Causes**:

1. **Hundreds of `Appear`s on one slide** — each participant adds overhead.
2. **Very long code blocks with large `highlightRanges`** — Prism re-renders on each range step.
3. **Heavy background images not pre-cached.**
4. **Live React components doing work on every render.**

**Fix**:

- Split busy slides into multiple slides.
- Shorten code blocks to the part you want to discuss.
- Preload background images in the HTML head with `<link rel="preload">`.
- Memoize live components with `React.memo` so they don't re-render on each slide step.
