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
| Assumed the visitor knew what prompt injection is | A six-part story that starts with an ordinary email |
| Numbers without context | Each number sits next to a sentence explaining it, derived from the live data |
| Generic visual style | An editorial design system with a serif reading face and a mythological motif |
| Static screens | Live demos: reveal hidden text, run the pipeline, compare the layers |

## Principles

1. **Show, then tell.** Every claim in the prose has a live demo beside it.
2. **Never contradict the model.** Verdicts, scores and metrics come from the API or the model card;
   explanatory sentences are derived from the scores rather than hard-coded.
3. **Explain every number.** Meters have plain-language hints ("known techniques", "learned patterns"),
   stat tiles say what the metric means, and the scoring formula is one click away.
4. **Be honest about failure.** The explainer shows a prompt that beats both layers, and the report shows
   the weakest generalisation results.
5. **Every element earns its place.** No decorative gradients, glows or emoji.

## Design system

### Typography

| Role | Typeface | Why |
|---|---|---|
| Reading (prose, headlines) | Source Serif 4 | Long-form readability and an editorial voice |
| Interface (labels, buttons, tables) | IBM Plex Sans | Clear at small sizes, technical in character |
| Data (code, rule IDs, metrics in tables) | IBM Plex Mono | Aligned digits and unambiguous characters |

All fonts are self-hosted, so the strict Content-Security-Policy allows no third-party origins.

### Colour

A warm paper background with ink-coloured text, one accent (Aegean blue) for interaction and bronze for
the myth motif and "hidden" things. Light and dark themes each have their own palette, chosen per theme
rather than inverted.

Verdict colours carry meaning, so they were **validated for colour-vision deficiency** with a palette
checker (lightness band, chroma, perceptual distance under protanopia, deuteranopia and tritanopia,
contrast against the surface):

| Status | Light theme | Dark theme |
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
stylesheet; the whole CSS is under 9 KB gzipped, with no CSS framework.

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
