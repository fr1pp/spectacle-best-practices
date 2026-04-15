# Animations — `Appear`, `Stepper`, and `useSteps`

Spectacle has a single unifying concept for in-slide animation: **the slide step sequence**. Every slide has an ordered list of "steps," and each tap of the `→` key advances to the next step within the current slide. When all steps are exhausted, the next tap advances to the next slide.

`Appear`, `Stepper`, `useSteps`, and `CodePane`'s `highlightRanges` are all ways to add participants to this step sequence.

## Mental model

1. A `Slide` starts at step 0.
2. Every participant (`Appear`, `Stepper`, `highlightRanges` range, manual `useSteps`) takes one or more slots in the sequence, in the order they render.
3. Advancing the slide fires each participant in turn.
4. `Appear` and `Stepper` animate via `react-spring` between an `inactiveStyle` and an `activeStyle`.

**Key implication**: if you put an `Appear` after a `CodePane` with three `highlightRanges` on the same slide, the `Appear` fires on the **fourth** tap (after the three highlight transitions play), not the second.

## `Appear` — the 90% use case

`Appear` wraps its children and reveals them on one step. It's the right choice for "show this bullet, then this one, then this one."

```tsx
<Slide>
  <Heading>Why we're moving off the monolith</Heading>
  <UnorderedList>
    <Appear><ListItem>Builds take 40 minutes</ListItem></Appear>
    <Appear><ListItem>One dependency update breaks five teams</ListItem></Appear>
    <Appear><ListItem>Deploys block on a six-hour integration test</ListItem></Appear>
  </UnorderedList>
</Slide>
```

### Props

| Prop            | Type                  | Default                | Purpose                                       |
|-----------------|-----------------------|------------------------|-----------------------------------------------|
| `children`      | `ReactNode`           | —                      | The element(s) being revealed                 |
| `tagName`       | `string`              | `'div'`                | Container element type                        |
| `className`     | `string`              | —                      | CSS class on the container                    |
| `activeStyle`   | style object          | `{ opacity: 1 }`       | Styles when revealed                          |
| `inactiveStyle` | style object          | `{ opacity: 0 }`       | Styles before reveal                          |
| `priority`      | `number`              | render order           | Fine-grained ordering override                |
| `id`            | `string`              | auto-generated         | Debug/test identifier                         |

### Customizing the reveal

The default reveal is a fade. To get a slide-up-and-fade:

```tsx
<Appear
  activeStyle={{ opacity: 1, transform: 'translateY(0)' }}
  inactiveStyle={{ opacity: 0, transform: 'translateY(24px)' }}
>
  <Text>Rising into view</Text>
</Appear>
```

Scale-in:

```tsx
<Appear
  activeStyle={{ opacity: 1, transform: 'scale(1)' }}
  inactiveStyle={{ opacity: 0, transform: 'scale(0.8)' }}
>
  <Heading>Zooming in</Heading>
</Appear>
```

Both keys are interpolated via `react-spring`, so any `react-spring`-compatible CSS can be animated.

### `Appear` wraps, it doesn't replace

`Appear` renders a container element (defaults to `<div>`) around its child. If you're putting `Appear` inside a `<ul>`, set `tagName="li"` so the DOM remains valid:

```tsx
<UnorderedList>
  <Appear tagName="li"><span>First</span></Appear>
  <Appear tagName="li"><span>Second</span></Appear>
</UnorderedList>
```

Or — simpler — wrap the `ListItem` itself:

```tsx
<UnorderedList>
  <Appear><ListItem>First</ListItem></Appear>
</UnorderedList>
```

Both work. Pick whichever reads more naturally in your layout.

## `Stepper` — stepping through values

`Stepper` is what you reach for when a single element should change through a sequence of values on successive taps. The length of `values` determines how many steps the `Stepper` consumes.

```tsx
<Stepper values={['draft', 'review', 'approved', 'shipped']}>
  {(value, step, isActive) =>
    isActive ? `Status: ${value}` : 'Status: —'
  }
</Stepper>
```

### Render function arguments

1. **The current value** (element of `values`, or `undefined` when inactive).
2. **The step index** relative to this `Stepper` (0 to `values.length - 1`, or `-1` when inactive).
3. **`isActive`** — whether the slide has reached this participant at all.

### Props

| Prop            | Type                  | Default                | Purpose                                            |
|-----------------|-----------------------|------------------------|----------------------------------------------------|
| `values`        | `T[]`                 | —                      | Sequence of values, one per step                   |
| `children`      | render function       | —                      | Called per step with `(value, step, isActive)`     |
| `render`        | render function       | —                      | Alternative to `children`                          |
| `tagName`       | `string`              | `'div'`                | Container element type                             |
| `className`     | `string`              | —                      | CSS class on the container                         |
| `activeStyle`   | style object          | `{ opacity: 1 }`       | Styles when any step is active                     |
| `inactiveStyle` | style object          | `{ opacity: 0 }`       | Styles before the first step                       |
| `alwaysVisible` | `boolean`             | `false`                | Force `activeStyle` always — use for persistent UI |
| `priority`      | `number`              | render order           | Ordering override                                  |
| `id`            | `string`              | auto-generated         | Debug/test identifier                              |

### `alwaysVisible` — the "show the text while it changes" case

By default, `Stepper` uses the inactive fade until it reaches step 0, meaning the element is invisible until the first tap. If you want the element to be visible from the start of the slide and just change its content on each tap, pass `alwaysVisible`:

```tsx
<Stepper alwaysVisible tagName="p" values={['foo', 'bar', 'baz']}>
  {(value, step, isActive) =>
    isActive ? `Current: ${value}` : 'Current: (waiting)'
  }
</Stepper>
```

### Multiple steppers on one slide

Each `Stepper` participates independently and advances in render order. Suppose:

```tsx
<Slide>
  <Stepper alwaysVisible values={['foo', 'bar']}>
    {(v, s, a) => a ? `First: ${v}` : 'First: —'}
  </Stepper>
  <Stepper alwaysVisible values={['baz', 'quux']}>
    {(v, s, a) => a ? `Second: ${v}` : 'Second: —'}
  </Stepper>
</Slide>
```

The tap sequence is:

| Tap | First stepper | Second stepper |
|-----|---------------|----------------|
| 0   | —             | —              |
| 1   | foo           | —              |
| 2   | bar           | —              |
| 3   | bar           | baz            |
| 4   | bar           | quux           |

The first stepper advances fully before the second starts.

## `useSteps` — the escape hatch

You only need `useSteps` when `Appear` and `Stepper` don't give you enough control — typically a custom animation that needs to coordinate state across multiple components, or a chart that should redraw on step.

```tsx
import { useSteps } from 'spectacle';

function AnimatedChart() {
  const { isActive, step, placeholder } = useSteps(3);

  return (
    <>
      {placeholder /* required — Spectacle uses this to track the participant */}
      <Chart highlight={isActive ? step : null} />
    </>
  );
}
```

### Arguments

```ts
useSteps(numSteps: number, options?: {
  id?: string;       // debug/test id
  priority?: number; // ordering override
})
```

### Return value

- `stepId`: the participant's id (randomly generated unless you passed `options.id`).
- `step`: the current relative step (`-1` before activation, then `0` to `numSteps - 1`).
- `isActive`: whether the sequence has reached this participant (equivalent to `step >= 0`).
- `placeholder`: a DOM node you **must** render in your component's output. Spectacle uses it to detect the participant in the DOM. Forgetting to render it means the participant never activates.

### Priority — when order matters

Participants activate in render order by default. `priority` overrides this — lower priority values activate first:

```tsx
const earlyBird = useSteps(1, { priority: -1 });  // activates before any default participant
const lateArrival = useSteps(1, { priority: 10 }); // activates after defaults
```

Use this rarely; it usually signals a layout that should be restructured instead.

## Picking between `Appear`, `Stepper`, and `useSteps`

| Need                                                                 | Pick        |
|----------------------------------------------------------------------|-------------|
| Reveal an element on one tap                                         | `Appear`    |
| Reveal several elements in order, each on a tap                      | Multiple `Appear`s |
| Change a value through a sequence on each tap                        | `Stepper`   |
| Coordinate animation across multiple components / custom logic       | `useSteps`  |
| Highlight different lines of a code block across taps                | `CodePane` with `highlightRanges` (see [code-panes.md](code-panes.md)) |

## Anti-patterns

- **Don't nest `Appear` inside another `Appear`.** The inner one never activates — its parent is still faded out.
- **Don't forget the `placeholder` when using `useSteps`.** Without it the step never fires.
- **Don't style `Appear` with CSS that contradicts its inline styles.** Spectacle sets `opacity` and `transform` via `react-spring`; a heavy-handed CSS `opacity: 1 !important` breaks it.
- **Don't mix per-item `Appear` with `SlideLayout.List`'s `animateListItems` prop.** Pick one. Using both double-wraps and skips steps.
