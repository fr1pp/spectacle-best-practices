# Deck, Slide, Templates, and Transitions

The two components you can't live without are `<Deck>` and `<Slide>`. `Deck` wraps the entire presentation and carries the theme, template, transition, and auto-play settings. `Slide` wraps a single view. Templates are overlays that render on every slide.

## `Deck` — the root component

```tsx
import { Deck, Slide, DefaultTemplate } from 'spectacle';

<Deck
  theme={customTheme}
  template={<DefaultTemplate />}
  transition={slideTransition}
  autoPlay={false}
>
  {/* slides go here */}
</Deck>
```

### Props

| Prop               | Type                                    | Default              | Purpose                                                                                     |
|--------------------|-----------------------------------------|----------------------|---------------------------------------------------------------------------------------------|
| `theme`            | styled-system theme object              | Spectacle default    | Colors, fonts, sizes, spacing — see [themes.md](themes.md)                                  |
| `template`         | `ReactNode` or render function          | none                 | Overlay shown on every slide (controls, progress, branding)                                 |
| `transition`       | `{ from, enter, leave }`                | slide-in-from-right  | Default transition applied to every slide (overridable per `Slide`)                         |
| `autoPlay`         | `boolean`                               | `false`              | Turn on timed auto-advance                                                                  |
| `autoPlayLoop`     | `boolean`                               | `false`              | When auto-playing, loop back to slide 1 at the end                                          |
| `autoPlayInterval` | `number` (ms)                           | `1000`               | How long between auto-advances                                                              |
| `backgroundImage`  | `string`                                | none                 | A backdrop image shown behind all slides                                                    |
| `pageSize`         | `string`                                | `"13.66in 7.68in"`   | Print-mode page size (CSS `@page size` values)                                              |
| `pageOrientation`  | `"landscape"` or `"portrait"`           | `"landscape"`        | Print-mode orientation                                                                      |
| `printScale`       | `number`                                | `0.959`              | Print-mode scale factor; leave alone unless PDF export is mis-fitting                       |

The `pageSize`/`pageOrientation`/`printScale` props only matter for PDF export — they're no-ops in normal and presenter view. Only Chrome/Chromium fully honor custom page sizes; Firefox/Safari still export but will ignore the custom size.

## `Slide` — one slide

```tsx
<Slide
  backgroundColor="primary"
  textColor="white"
  transition={{
    from: { opacity: 0 },
    enter: { opacity: 1 },
    leave: { opacity: 0 }
  }}
>
  <Heading>Hello</Heading>
</Slide>
```

### Props

| Prop                 | Type                              | Purpose                                                                 |
|----------------------|-----------------------------------|-------------------------------------------------------------------------|
| `backgroundColor`    | `string` (theme key or CSS)       | Slide-level background                                                  |
| `backgroundImage`    | `string`                          | Slide-level background image (URL)                                      |
| `backgroundOpacity`  | `number` (0–1)                    | Dims the background image                                               |
| `backgroundPosition` | `string`                          | CSS `background-position` value                                         |
| `backgroundRepeat`   | `string`                          | CSS `background-repeat` value                                           |
| `backgroundSize`     | `string`                          | CSS `background-size` (default is usually `cover`)                      |
| `textColor`          | `string`                          | Default text color for this slide (cascades to children)                |
| `scaleRatio`         | `number`                          | Override the fit-to-viewport scale for this slide                       |
| `slideNum`           | `number`                          | Force a specific slide number (used by some templates)                  |
| `template`           | render function                   | Per-slide template override (rare; use deck template unless needed)     |
| `transition`         | `{ from, enter, leave }`          | Overrides the deck transition for this slide                            |

Any child of a `Slide` is a slide's content. Typical children: `Heading`, `Text`, `Box`, `FlexBox`, `Image`, `CodePane`, `Notes`, `Appear`, `Stepper`, or one of the `SlideLayout.*` variants (which themselves are pre-built slide-layout wrappers — use them as children of `Deck`, not `Slide`; see [slide-layouts.md](slide-layouts.md)).

## Templates — deck-wide overlays

A template is a "master" layer that renders on top of every slide. It's where controls, page numbers, and branding live. Spectacle ships `DefaultTemplate`, which gives you the fullscreen toggle and animated progress dots out of the box.

### The easy path: `DefaultTemplate`

```tsx
import { Deck, DefaultTemplate } from 'spectacle';

<Deck theme={theme} template={<DefaultTemplate />}>
  {/* slides */}
</Deck>
```

`DefaultTemplate` accepts a single prop, `color`, for tinting its controls:

```tsx
<Deck template={<DefaultTemplate color="#f06" />}>
```

### Custom templates

Pass a render function that receives `{ slideNumber, numberOfSlides }` and returns JSX:

```tsx
<Deck
  template={({ slideNumber, numberOfSlides }) => (
    <FlexBox
      justifyContent="space-between"
      position="absolute"
      bottom={0}
      width={1}
    >
      <Box padding="0 1em">
        <FullScreen />
      </Box>
      <Box padding="1em">
        <Progress />
        Slide {slideNumber} of {numberOfSlides}
      </Box>
    </FlexBox>
  )}
>
```

Typical ingredients for a custom template: `<FullScreen />` (fullscreen button), `<Progress />` or `<AnimatedProgress />` (dot progress indicator), `<FlexBox>` for positioning, and plain text for slide numbers or branding.

**Template position is absolute.** Use `position="absolute"` with `top` / `bottom` / `left` / `right` props to pin the overlay — otherwise it collides with slide content.

## Transitions

A transition is a plain object with three keys defining CSS styles at three moments:

```ts
const fadeTransition = {
  from:  { opacity: 0 },
  enter: { opacity: 1 },
  leave: { opacity: 0 }
};
```

- `from`: styles right before the slide enters
- `enter`: styles while the slide is in view
- `leave`: styles as the slide exits

Spectacle uses `react-spring` under the hood to interpolate between these states, so any CSS property that `react-spring` can animate works (transforms, opacity, colors, dimensions).

### Applying a transition

**Deck-wide:**

```tsx
<Deck transition={fadeTransition} template={<DefaultTemplate />}>
```

**Per-slide (overrides the deck-wide one):**

```tsx
<Slide
  transition={{
    from:  { opacity: 0, transform: 'rotate(45deg)' },
    enter: { opacity: 1, transform: 'rotate(0)' },
    leave: { opacity: 0, transform: 'rotate(315deg)' }
  }}
>
```

Useful transition recipes:

```ts
// Fade
{ from: { opacity: 0 }, enter: { opacity: 1 }, leave: { opacity: 0 } }

// Slide up
{
  from:  { opacity: 0, transform: 'translateY(50px)' },
  enter: { opacity: 1, transform: 'translateY(0)' },
  leave: { opacity: 0, transform: 'translateY(-50px)' }
}

// Zoom
{
  from:  { opacity: 0, transform: 'scale(0.8)' },
  enter: { opacity: 1, transform: 'scale(1)' },
  leave: { opacity: 0, transform: 'scale(1.2)' }
}
```

### Why a custom transition isn't animating

- The `from`/`enter`/`leave` keys must be exact — typos like `begin` or `start` silently do nothing.
- `react-spring` animates numbers and colors, not arbitrary CSS strings. `transform` must be a single string (not an array), and values like `translate(10px, 20px)` work but `margin: '10px auto'` does not.
- A per-slide transition fully replaces the deck-wide one — if you pass `{ from: { opacity: 0 } }` without `enter` and `leave`, the slide will start hidden and stay hidden. Provide all three keys.

## Auto-play

For kiosk-style decks that advance themselves:

```tsx
<Deck
  autoPlay
  autoPlayLoop
  autoPlayInterval={5000}
  template={<DefaultTemplate />}
>
```

- `autoPlay` turns on timed advance
- `autoPlayLoop` makes it restart from slide 1 at the end
- `autoPlayInterval` is in milliseconds; default is `1000` (which is usually too fast — 5000–10000 is more realistic for a kiosk deck)

Keyboard nav still works while auto-play is on, so a user tapping ← or → will interrupt the timer and take manual control until the next tick.
