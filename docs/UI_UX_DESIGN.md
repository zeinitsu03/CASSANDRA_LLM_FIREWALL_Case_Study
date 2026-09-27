# UI and UX design

The web app's job is to make a reviewer *understand* prompt injection and *trust* the tool, in a few
minutes, without prior knowledge. This document explains how the design serves that.

## From dashboard to explainer

The first version was a dark "security console": monospace everything, uppercase labels in boxed panels,
a single accent colour. It worked, but it looked like every generated dashboard and explained nothing:
a visitor saw numbers without knowing why they mattered.

It was replaced by an **interactive explainer** (in the tradition of long-form visual essays) followed by
the tools:

| Problem with the dashboard | What replaced it |
|---|---|
| Assumed the visitor knew what prompt injection is | A five-part story that starts with an ordinary email |
| Numbers without context | Each number sits next to a sentence explaining it, derived from the live data |
| Generic visual style | An editorial design system with a serif reading face and a mythological motif |
| Static screens | Live demos: reveal hidden text, run the pipeline, compare the layers |

## Version 3: a product landing page, in true black

The explainer taught well but opened like an essay: a visitor had to scroll before seeing the product
do anything. The third iteration puts a product landing page in front of the story and moves the whole
interface to a true-black theme.

### Research

I studied the landing pages developers and security teams judge products by, and took one idea from
each:

| Site | What it does well | What CASSANDRA took |
|---|---|---|
| [Lakera](https://www.lakera.ai) | A security product that proves itself with a live demo and hard numbers | The live scanner card in the hero and the proof strip under it |
| [Linear](https://linear.app) | Near-black surfaces, hairline borders, restrained accent, headings with a soft gradient | Gradient headings, 1-pixel top highlights on cards, a pointer-following spotlight on the feature grid |
| [Vercel](https://vercel.com) | Pure `#000` with high-contrast white, a white primary button | The true-black canvas and the white-on-black primary action |
| [Resend](https://resend.com) | Leads with a code block showing how little code is needed | "Add it in a few lines": copyable Python, HTTP and CLI tabs |
| [Raycast](https://www.raycast.com) | Shows the real product UI, not an illustration | The hero card is the real engine, not a mock-up |
| [Stripe](https://stripe.com) | A closing band that turns attention into action | The dark call-to-action band leading to the arena |

### Why true black

- **It fits the subject.** A security tool reads as serious on black; the Aegean-blue accent and the
  red, amber and teal verdicts stand out without shouting.
- **It saves power.** On OLED screens `#000` pixels are switched off.
- **Depth without shadows.** Shadows are invisible on black, so elevation comes from hairline borders,
  a faint white gradient on cards, a 1-pixel highlight along their top edge, and soft coloured glows
  behind the product card and the calls to action.

The light theme was kept, but as a choice rather than a default: a button in the header switches
themes and the browser remembers it. The status colours were re-validated against the new, darker card
surface (`#0a0a0b`).

### What the landing page is made of

| Section | Purpose |
|---|---|
| Hero | One-line promise, two actions (scanner, arena), and the live scanner card cycling through four real inputs |
| Proof strip | Recall, false-alarm rate, scan time and technique count, read from the model card |
| Capabilities | Six one-sentence features with icons, lit by a spotlight that follows the pointer |
| The story | The five-part explainer, unchanged, now framed as "how it works, step by step" |
| For developers | Copyable integration code in three forms |
| Closing band | The invitation to the arena |

## Principles

1. **Show, then tell.** Every claim in the prose has a live demo beside it.
2. **Never contradict the model.** Verdicts, scores and metrics come from the API or the model card;
   explanatory sentences are derived from the scores rather than hard-coded.
3. **Explain every number.** Meters have plain-language hints ("known techniques", "learned patterns"),
   stat tiles say what the metric means, and the scoring formula is one click away.
4. **Be honest about failure.** The explainer shows a prompt that beats both layers, and the report shows
   the weakest generalisation results.
5. **Every element earns its place.** One accent colour, glows only behind the live product and the
   calls to action, no emoji.

## Design system

### Typography

| Role | Typeface | Why |
|---|---|---|
| Reading (prose, headlines) | Source Serif 4 | Long-form readability and an editorial voice |
| Interface (labels, buttons, tables) | IBM Plex Sans | Clear at small sizes, technical in character |
| Data (code, rule IDs, metrics in tables) | IBM Plex Mono | Aligned digits and unambiguous characters |

All fonts are self-hosted, so the strict Content-Security-Policy allows no third-party origins.

### Colour

The default theme is true black with near-white text; the alternative is a warm paper background with
ink-coloured text. Both use one accent (Aegean blue) for interaction and bronze for the myth motif and
"hidden" things. Each theme has its own palette, chosen per theme rather than inverted.

| Token | Black theme (default) | Light theme |
|---|---|---|
| Page | `#000000` | `#fbfaf7` |
| Card | `#0a0a0b` + a faint white gradient | `#ffffff` |
| Hairline border | `#1f1f24` | `#e6e2d9` |
| Text / secondary / muted | `#ededef` / `#a1a1aa` / `#7a7a84` | `#17191e` / `#545a66` / `#8b909a` |
| Accent | `#8ea2ff` | `#2f4bd0` |

Verdict colours carry meaning, so they were **validated for colour-vision deficiency** with a palette
checker (lightness band, chroma, perceptual distance under protanopia, deuteranopia and tritanopia,
contrast against the surface):

| Status | Light theme | Black theme |
|---|---|---|
| allow | `#0f8a7a` | `#1d9e8f` |
| flag | `#b07a0e` | `#bd8a1e` |
| block | `#d23f5a` | `#ea4f6a` |

The first palette tried (green/amber/red) failed: green and amber were nearly indistinguishable for
protanopes. The final teal/amber/red passes, and because red and amber sit close under deuteranopia,
**colour is never used alone**: every verdict also carries a text label and a glyph (✓ ! ✕).

### Components

A deliberately small set: a page layout, a card panel, a risk meter, a status badge, a verdict report
and a findings table, plus the explainer's section and figure layout. Each has its own co-located
stylesheet; the whole CSS is about 11 KB gzipped, with no CSS framework. Icons come from Lucide.

### The signature details

- A brand mark of an eye (the seer) inside a shield (the firewall).
- A Greek meander band as a section divider.
- Numbered sections in bronze monospace, like an essay's figure references.

## Motion

Animation is used only where it explains something:

| Animation | What it communicates |
|---|---|
| Pipeline stages revealing one by one, with a filling progress rail | The order in which the engine works |
| Tokens appearing one by one in the context diagram | Many sources becoming one stream |
| Meters and bars growing to their value | Magnitude |
| Highlights fading in on attack spans | Where the evidence is |
| Verdict badge "stamping" in | The decision |
| Typing indicator in the arena | The guardian is responding |
| Inputs typed into the hero's scanner card | The product working on real attacks, one after another |
| Spotlight following the pointer across the capability cards | Which feature is under the reader's attention |

All motion is CSS, runs once when a section scrolls into view, and is **disabled entirely** when the
operating system's reduced-motion setting is on (the pipeline then shows all stages immediately).

## Accessibility

- Semantic structure: landmarks, headings in order, tables with header cells, a skip link.
- Everything works by keyboard, with visible focus rings; the scanner scans on `Ctrl + Enter`.
- Tabs, switches and meters use the correct ARIA roles and states.
- Live regions announce scan results, pipeline stages and arena replies to screen readers.
- Status is never conveyed by colour alone.
- Tests query the interface by role and label, as assistive technology does.

## Responsive behaviour

Tested at desktop and at a 390-pixel phone width with no horizontal scrolling on any page. Sidebars move
below the main content, the arena's chat stacks its input, and the findings table becomes one block per
finding instead of a sideways-scrolling table.
