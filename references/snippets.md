# Starter Snippets

Copy-pasteable templates for common Spectacle slide patterns. Each one is standalone — drop it into a deck and it works. Use these as starting points, then tweak the theme and content.

## 0. Minimal deck

```tsx
import { Deck, Slide, Heading, DefaultTemplate } from 'spectacle';

export default function App() {
  return (
    <Deck template={<DefaultTemplate />}>
      <Slide>
        <Heading>Hello, Spectacle</Heading>
      </Slide>
    </Deck>
  );
}
```

## 1. Themed deck with four slides

```tsx
import {
  Deck, Slide, SlideLayout, Heading, Text, UnorderedList, ListItem,
  Appear, Notes, DefaultTemplate
} from 'spectacle';

const theme = {
  colors: {
    primary: '#1a1a1a',
    secondary: '#d72a42',
    tertiary: '#f5f3ee',
    quaternary: '#0074d9'
  },
  fontSizes: { h1: '80px', h2: '56px', text: '30px' },
  fonts: {
    header: '"Space Grotesk", sans-serif',
    text: '"Inter", sans-serif'
  },
  space: [16, 24, 40, 56]
};

export default function App() {
  return (
    <Deck theme={theme} template={<DefaultTemplate />}>

      <Slide>
        <Heading>Why we're rewriting it</Heading>
        <Text>Retrospective, April 2026</Text>
        <Notes>Say hello. Thank the team. Don't skip the context.</Notes>
      </Slide>

      <SlideLayout.Section>Context</SlideLayout.Section>

      <Slide>
        <Heading>What changed</Heading>
        <UnorderedList>
          <Appear><ListItem>Traffic 10x</ListItem></Appear>
          <Appear><ListItem>Team split in two</ListItem></Appear>
          <Appear><ListItem>Original architecture hit its limits</ListItem></Appear>
        </UnorderedList>
      </Slide>

      <SlideLayout.BigFact factInformation="the old p95 latency at launch">
        820ms
      </SlideLayout.BigFact>

    </Deck>
  );
}
```

## 2. Title / cover slide

```tsx
<SlideLayout.Center backgroundColor="secondary" textColor="tertiary">
  <Heading fontSize="96px">Production Incidents</Heading>
  <Text>A retrospective — April 2026</Text>
</SlideLayout.Center>
```

## 3. Two-column comparison

```tsx
<SlideLayout.TwoColumn
  left={
    <>
      <Heading>Before</Heading>
      <Text>Manual deploys, 4–6 hours, one person bottlenecked.</Text>
    </>
  }
  right={
    <>
      <Heading>After</Heading>
      <Text>Automated, 12 minutes, anyone on-call can trigger.</Text>
    </>
  }
/>
```

## 4. Animated bullet list (reveal one at a time)

```tsx
<Slide>
  <Heading>What we learned</Heading>
  <UnorderedList>
    <Appear><ListItem>Observability pays for itself</ListItem></Appear>
    <Appear><ListItem>Runbooks are actually useful</ListItem></Appear>
    <Appear><ListItem>Postmortems are a team-building exercise</ListItem></Appear>
    <Appear><ListItem>"Just check Slack" is not a runbook</ListItem></Appear>
  </UnorderedList>
</Slide>
```

## 5. Code walkthrough with highlighted ranges

```tsx
import tomorrow from 'react-syntax-highlighter/dist/cjs/styles/prism/tomorrow';

<Slide>
  <Heading>useSteps, annotated</Heading>
  <CodePane
    language="tsx"
    theme={tomorrow}
    highlightRanges={[
      [1, 2],   // intro
      [3, 5],   // the placeholder
      [6, 10],  // the return
    ]}
  >
    {`
    function useSteps(numSteps, options) {
      const stepId = options.id ?? randomId();

      const placeholder = <div data-step-id={stepId} />;

      return {
        stepId,
        step: computeStep(stepId),
        isActive: computeActive(stepId),
        placeholder
      };
    }
    `}
  </CodePane>
</Slide>
```

## 6. Big fact slide

```tsx
<SlideLayout.BigFact factInformation="requests served last month">
  1.4B
</SlideLayout.BigFact>
```

Tighter font if the number is long:

```tsx
<SlideLayout.BigFact factFontSize="160px" factInformation="daily active users">
  3,847,221
</SlideLayout.BigFact>
```

## 7. Quote slide

```tsx
<SlideLayout.Quote attribution="Grace Hopper">
  The most dangerous phrase in the language is "we've always done it this way."
</SlideLayout.Quote>
```

## 8. Image hero with title

```tsx
<SlideLayout.HorizontalImage
  src="/images/architecture-diagram.png"
  alt="System architecture showing API, queue, and database"
  title="How it fits together"
  description="Three services, one queue, one database."
/>
```

Full-bleed image:

```tsx
<SlideLayout.FullBleedImage
  src="/images/launch-party.jpg"
  alt="The team at the launch party"
/>
```

Portrait image with a bulleted list:

```tsx
<SlideLayout.VerticalImage
  src="/images/dashboard-screenshot.png"
  alt="The observability dashboard"
  position="left"
  title="The new dashboard"
  listItems={[
    'p50, p95, p99 latency',
    'Error rate by service',
    'Queue depth',
    'Live traces'
  ]}
  animateListItems
/>
```

## 9. Section divider

```tsx
<SlideLayout.Section>Chapter 2: The Fix</SlideLayout.Section>
```

Stylized:

```tsx
<SlideLayout.Section sectionProps={{ fontSize: '72px', color: 'secondary' }}>
  Part II — What We Built
</SlideLayout.Section>
```

## 10. Statement slide (single bold idea)

```tsx
<SlideLayout.Statement>
  We never want to be paged for this again.
</SlideLayout.Statement>
```

## 11. Deck-wide template with slide numbers

```tsx
<Deck
  theme={theme}
  template={({ slideNumber, numberOfSlides }) => (
    <FlexBox
      justifyContent="space-between"
      position="absolute"
      bottom={0}
      width={1}
      padding="0 2rem 1rem 2rem"
    >
      <Box>
        <FullScreen />
      </Box>
      <Box>
        <Text fontSize="small" color="primary">
          {slideNumber} / {numberOfSlides}
        </Text>
      </Box>
    </FlexBox>
  )}
>
  {/* slides */}
</Deck>
```

Don't forget to import `FlexBox`, `Box`, `FullScreen`, and `Text` from `spectacle`.

## 12. Stepper — changing a single value

```tsx
<Slide>
  <Heading>Release status</Heading>
  <Stepper alwaysVisible tagName="p" values={['draft', 'staged', 'canary', 'GA']}>
    {(value, step, isActive) => (
      <Text fontSize="h2">
        {isActive ? `Status: ${value}` : 'Status: —'}
      </Text>
    )}
  </Stepper>
</Slide>
```

## 13. Side-by-side code comparison

```tsx
<SlideLayout.MultiCodeLayout
  title="Before vs. after"
  numColumns={2}
  codeBlocks={[
    {
      code: `const result = expensive(input);`,
      language: 'javascript',
      description: 'Runs on every render',
    },
    {
      code: `const result = useMemo(
  () => expensive(input),
  [input]
);`,
      language: 'javascript',
      description: 'Runs only when input changes',
    },
  ]}
/>
```

## 14. Custom transition — fade

```tsx
const fade = {
  from:  { opacity: 0 },
  enter: { opacity: 1 },
  leave: { opacity: 0 }
};

<Deck transition={fade} template={<DefaultTemplate />}>
  {/* slides */}
</Deck>
```

## 15. Presenter notes

```tsx
<Slide>
  <Heading>Q&A</Heading>
  <Text>Any questions?</Text>
  <Notes>
    Common questions to expect:
    - Why not Kafka?
    - What's the rollback plan?
    - How does it handle spikes?

    If asked about cost, say "cheaper than the outage." Don't give numbers.
  </Notes>
</Slide>
```

## 16. Markdown slide embedded in a JSX deck

```tsx
<MarkdownSlide animateListItems>
  {`
  # Quick markdown slide

  Sometimes you just want to type:

  - a list
  - of things
  - without JSX boilerplate
  `}
</MarkdownSlide>
```

## 17. Section of slides written entirely in markdown

```tsx
<MarkdownSlideSet>
  {`
  # Section Intro

  This whole section is authored in markdown.

  ---

  # Another slide

  Still in markdown.

  Notes: Remember to pause after this one.

  ---

  # Last one

  Back to JSX on the next slide.
  `}
</MarkdownSlideSet>
```

## 18. Full-screen code demo slide (no chrome)

```tsx
<Slide backgroundColor="primary" textColor="tertiary">
  <CodePane
    language="typescript"
    theme={tomorrow}
    showLineNumbers={false}
  >
    {`
    export async function handler(req: Request): Promise<Response> {
      const body = await req.json();
      const result = await process(body);
      return Response.json(result);
    }
    `}
  </CodePane>
</Slide>
```
