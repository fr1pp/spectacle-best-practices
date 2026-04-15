# Code Panes — `CodePane` and friends

`CodePane` is the syntax-highlighted code block. It's a wrapper around [react-syntax-highlighter](https://github.com/react-syntax-highlighter/react-syntax-highlighter) preconfigured for Spectacle, with support for line highlighting that steps with slide advances.

## Basic usage

```tsx
import { Slide, CodePane } from 'spectacle';

<Slide>
  <CodePane language="javascript">
    {`
    function greet(name) {
      return \`Hello, \${name}!\`;
    }
    `}
  </CodePane>
</Slide>
```

A few things happen automatically:

- Whitespace is trimmed and indent is normalized, so you can indent your code naturally inside the JSX string.
- Long lines wrap, preserving indent.
- Line numbers show by default (`showLineNumbers` defaults to `true`).
- Overflow scrolls.

## Props

| Prop              | Type                                    | Default          |
|-------------------|-----------------------------------------|------------------|
| `children`        | `string` (required)                     | —                |
| `language`        | `string` (e.g., `"javascript"`, `"python"`) | —            |
| `theme`           | Prism theme object                      | `vs-dark`        |
| `highlightRanges` | `number[]` or `number[][]`              | —                |
| `showLineNumbers` | `boolean`                               | `true`           |
| `Layout` props    | —                                       | —                |
| `Position` props  | —                                       | —                |

## Picking a language

`language` is passed straight to `react-syntax-highlighter`. Common values: `javascript`, `jsx`, `typescript`, `tsx`, `python`, `ruby`, `go`, `rust`, `java`, `c`, `cpp`, `csharp`, `sql`, `json`, `yaml`, `bash`, `shell`, `html`, `css`, `scss`, `diff`, `markdown`, `graphql`, `dockerfile`. The full language list comes from Prism. If highlighting looks wrong, the language key probably doesn't match what Prism expects.

## Picking a theme

Prism themes are exported from Spectacle as `codePaneThemes`, or you can import any theme from `react-syntax-highlighter/dist/cjs/styles/prism/`:

```tsx
import { CodePane } from 'spectacle';
import tomorrow from 'react-syntax-highlighter/dist/cjs/styles/prism/tomorrow';
import oneDark from 'react-syntax-highlighter/dist/cjs/styles/prism/one-dark';
import githubLight from 'react-syntax-highlighter/dist/cjs/styles/prism/one-light';

<CodePane language="typescript" theme={tomorrow}>
  {`const answer: number = 42;`}
</CodePane>
```

Popular choices:

| Theme              | Mood                  | Import                  |
|--------------------|-----------------------|-------------------------|
| `vs-dark` (default)| Neutral dark          | built-in                |
| `tomorrow`         | Warm dark             | `tomorrow`              |
| `one-dark`         | Atom One Dark         | `one-dark`              |
| `one-light`        | Atom One Light        | `one-light`             |
| `nord`             | Cool blue-grey dark   | `nord`                  |
| `dracula`          | Vivid purple dark     | `dracula`               |
| `ghcolors`         | GitHub light          | `ghcolors`              |
| `solarized-dark`   | Solarized             | `solarized-dark`        |

For a light theme on dark slides (or vice versa), match the theme to the slide's `backgroundColor` rather than the deck default.

## Line highlighting with `highlightRanges`

This is the feature that makes `CodePane` great for live walkthroughs. `highlightRanges` takes an array describing which lines to highlight at each step of the slide's step sequence.

### Shapes

```tsx
// Single range: highlight lines 1–3 from the start, always.
<CodePane language="js" highlightRanges={[1, 3]}>
  ...
</CodePane>

// Multiple ranges: step through them one at a time.
<CodePane language="js" highlightRanges={[[1, 3], [6, 8], [10, 15]]}>
  ...
</CodePane>

// Mixed ranges and single lines.
<CodePane language="js" highlightRanges={[[1, 3], 5, [6, 8], [10, 15], 20]}>
  ...
</CodePane>
```

### How it steps

**Every range in `highlightRanges` is a slide step.** So a `CodePane` with `highlightRanges={[[1,3], [5,7], [9,11]]}` adds three steps to the slide. The first tap highlights 1–3, the second tap highlights 5–7, the third tap highlights 9–11, and the fourth tap moves to the next slide.

This matters for ordering with `Appear` and `Stepper`: if you have an `Appear` after the `CodePane` on the same slide, the `Appear` fires after all the code highlights have played out.

If you want the highlights to behave as "start here, don't step" — just a static visual cue on one section — pass a single two-number array: `highlightRanges={[1, 3]}`.

### Example: a walkthrough slide

```tsx
<Slide>
  <Heading>How `useSteps` works</Heading>
  <CodePane
    language="tsx"
    theme={tomorrow}
    highlightRanges={[
      [1, 3],     // intro: the function signature
      [4, 6],     // the placeholder DOM node
      [7, 9],     // the return object
      [10, 15],   // the consumer side
    ]}
  >
    {`
    function useSteps(numSteps, options) {
      const stepId = options.id ?? randomId();
      const slide = useContext(SlideContext);

      const placeholder = <div data-step-id={stepId} />;

      return {
        stepId,
        step: computeStep(slide, stepId),
        isActive: computeActive(slide, stepId),
        placeholder
      };
    }

    // Consumer:
    const { step, isActive, placeholder } = useSteps(3);
    return (
      <>{placeholder}{isActive ? 'yes' : 'no'}</>
    );
    `}
  </CodePane>
</Slide>
```

## Multi-code slides

For comparing two or more blocks of code side-by-side, use `SlideLayout.MultiCodeLayout` rather than nesting two `CodePane`s in a `FlexBox` — it handles sizing, descriptions, and column count automatically.

```tsx
<SlideLayout.MultiCodeLayout
  title="Naive vs. memoized"
  numColumns={2}
  codeBlocks={[
    {
      code: `const result = expensive(input);`,
      language: 'javascript',
      description: 'Runs on every render',
    },
    {
      code: `const result = useMemo(() => expensive(input), [input]);`,
      language: 'javascript',
      description: 'Runs only when input changes',
    },
  ]}
/>
```

Each `codeBlocks[i]` accepts everything a `CodePane` does (including `highlightRanges`, `theme`, `showLineNumbers`), plus `description` and `descriptionProps`.

## Single-code slides

If a whole slide is "one code block with a title," `SlideLayout.Code` is shorter than building it yourself:

```tsx
<SlideLayout.Code
  language="rust"
  title="Ownership, briefly"
  codePaneProps={{
    theme: tomorrow,
    highlightRanges: [[1, 4], [5, 8]],
  }}
>
  {`
  fn main() {
    let s = String::from("hello");
    takes_ownership(s);
    // s is invalid here
  }
  fn takes_ownership(s: String) {
    println!("{}", s);
  }
  `}
</SlideLayout.Code>
```

## Inline code

For inline code within prose, use `CodeSpan`:

```tsx
<Text>
  Call <CodeSpan>useSteps(3)</CodeSpan> inside a component to register it.
</Text>
```

`CodeSpan` is a typography component — it defaults to the monospace font from `theme.fonts.monospace` and inherits text color.

## Rules of thumb

- **Don't skip `language`.** Without it Prism falls back to "text" and nothing gets colored.
- **Use template literals for the `children` string** — it preserves newlines and lets you indent naturally inside JSX.
- **Count your `highlightRanges` entries** when planning a slide. They each consume a step.
- **Theme the code block to match the slide**, not the deck default. A dark theme on a light slide looks jarring.
- **Import Prism themes from `react-syntax-highlighter/dist/cjs/styles/prism/`**, not `esm/`, for the widest bundler compatibility.
