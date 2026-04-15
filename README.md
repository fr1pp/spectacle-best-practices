# spectacle-best-practices

A [Claude Code skill](https://docs.claude.com/en/docs/agents/skills) for building presentations with [Spectacle](https://github.com/FormidableLabs/spectacle), the React-based presentation library by FormidableLabs / Nearform. Targets Spectacle **v10.2.3**.

## What it does

When you ask Claude Code to write, review, or debug a Spectacle deck, this skill activates and gives Claude the exact prop names, import paths, layout helpers, and gotchas for the library — so you get code that actually compiles instead of well-intentioned hallucinations of `prism-react-renderer` or nested `Stepper`/`highlightRanges` confusion.

It defaults to React/JSX authoring (richer than Markdown mode for live demos and custom layouts) but cross-references the Markdown path when that's the better fit.

## What it covers

The [`SKILL.md`](SKILL.md) is an activity-based map; each entry points into a topic file under [`references/`](references/):

| Topic | File |
|-------|------|
| Installation, CLI templates, NextJS App Router caveat | [getting-started.md](references/getting-started.md) |
| `Deck`, `Slide`, templates, transitions, auto-play | [deck-and-slides.md](references/deck-and-slides.md) |
| `SlideLayout.*` helpers (Center, TwoColumn, BigFact, Quote, Code, image layouts…) | [slide-layouts.md](references/slide-layouts.md) |
| `Heading`, `Text`, `Box`, `FlexBox`, `Grid`, tables, base props | [typography-and-layout.md](references/typography-and-layout.md) |
| `CodePane` with syntax themes and `highlightRanges` | [code-panes.md](references/code-panes.md) |
| `Appear`, `Stepper`, `useSteps` — picking the right animation primitive | [animations.md](references/animations.md) |
| Theme system (colors, fontSizes, scaled `space` array, custom templates) | [themes.md](references/themes.md) |
| Four ready-to-use themes: Corporate Clean, Midnight Dev, Playful Pastel, Editorial | [theme-cookbook.md](references/theme-cookbook.md) |
| `Markdown`, `MarkdownSlide`, `MarkdownSlideSet`, `.md` layout annotations | [markdown-slides.md](references/markdown-slides.md) |
| Keyboard shortcuts, presenter mode, PDF export, kiosk/auto-play | [presenter-and-export.md](references/presenter-and-export.md) |
| Common pitfalls: `window is not defined`, `highlightRanges` stepping, font loading, nested `Appear` | [anti-patterns.md](references/anti-patterns.md) |
| Copy-pasteable starter code for 18 common slide patterns | [snippets.md](references/snippets.md) |

## Installation

### Option 1: install from GitHub

```bash
cd ~/.claude/skills
git clone https://github.com/fr1pp/spectacle-best-practices.git
```

Claude Code picks up skills from `~/.claude/skills/` automatically on the next session.

### Option 2: install as a `.skill` archive

If you have a `.skill` file, drop it into `~/.claude/skills/` and Claude Code will install it on start.

## Validation

The skill was evaluated with four realistic test prompts against a no-skill baseline (4 evals × 2 variants, graded on 28 objective assertions):

| Configuration | Pass rate |
|---------------|-----------|
| With skill    | **100% (28/28)** |
| Baseline      | 82.8% (23/28) |
| Delta         | **+17.2%** |

The two most load-bearing wins:

- **Code walkthrough slide**: the baseline hallucinated `prism-react-renderer` (Spectacle actually uses `react-syntax-highlighter`) and wrapped `CodePane` in a `<Stepper>` — which double-steps against `highlightRanges`' built-in stepping. The with-skill output used the correct library and `highlightRanges` directly.
- **Next.js App Router debug**: the baseline insisted `'use client'` alone was insufficient and prescribed splitting into a Client Component + `next/dynamic` with `ssr: false`. The with-skill output followed Spectacle's own docs and used just `'use client'`.

## License

MIT — see [LICENSE](LICENSE).

## Not affiliated with

FormidableLabs, Nearform, or Anthropic. This is a community skill. Spectacle itself is MIT-licensed by FormidableLabs; all credit for the library goes to its maintainers.
