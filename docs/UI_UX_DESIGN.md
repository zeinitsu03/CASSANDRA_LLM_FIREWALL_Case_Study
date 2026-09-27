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
do anything. The third iteration put a product landing page in front of the story and moved the
interface to a true-black theme, borrowing from the pages developers and security teams judge products
by: a live product in the hero (Lakera, Raycast), a pure `#000` canvas with a white primary action
(Vercel), and a code block showing how little integration takes (Resend).

It also borrowed their decoration: gradient headings, glows, a pointer-following spotlight, a stat-tile
banner and a grid of icon cards. That turned out to be a mistake, and version 4 undid it.

## Version 4: removing the template tells

Reviewed cold, version 3 looked generated. So I checked it against published lists of the patterns
that make a site read as AI-generated
([Developers Digest](https://www.developersdigest.tech/blog/ai-design-slop-and-how-to-spot-it),
[The Fountain Institute](https://www.thefountaininstitute.com/blog/signs-vibe-coded-ui),
[21st.dev](https://21st.dev/blog/website-not-look-ai-generated)) and fixed every one that applied:

| Tell | What version 3 had | What replaced it |
|---|---|---|
| Purple-blue gradient text, serif italic accent word | "*before*" in a blue-violet gradient; gradient-filled headings | Solid headings; hierarchy from size and weight only |
| Glows and coloured shadows | Glows behind the hero, the product card and the buttons | None. Depth comes from 1px borders and contrast |
| Badge above the headline | A pill reading "Prompt-injection firewall for LLM apps" | Removed; the headline says it |
| Stat banner row | Four stat tiles under the hero | One sentence of evidence in the hero, with the numbers read live from the model card |
| Identical feature cards with an icon on top | Six icon cards in a 3×2 grid | A specification list: a short name and one specific, checkable paragraph per capability |
| Coloured stripe on blocks | Bronze or status-coloured left edges in four places | A tinted background, a dashed outline for hidden text, or plain hairlines |
| Pills and rounded everything | Pill buttons, pill tabs, pill chips | 6px controls, 10px panels, 4px chips; underline tabs and navigation |
| The same icon set everywhere | Lucide icons on every card and button | No icon library; words instead |
| Decorative background | A masked grid pattern behind the hero | Plain black |
| Numbered steps | "01–05" labels over each explainer section | Removed; the headings carry the story |
| Generic copy | "Undoes obfuscation", "Runs anywhere" | Claims with numbers: five decoded payloads, four languages, 19,548 prompts, a 32-document batch |

The deeper change is one rule for colour: **colour is evidence**. The interface is ink on paper (white
on black by default), and colour appears only where it means something: teal, amber and red for
verdicts, bronze for hidden or decoded text. A visitor learns in seconds that anything coloured is
something the engine found. The rules are written down in a `DESIGN.md` next to the frontend code,
so later changes don't drift back to template defaults.

The light theme stays one click away, and the choice is remembered in the browser.

## Principles

1. **Show, then tell.** Every claim in the prose has a live demo beside it.
2. **Never contradict the model.** Verdicts, scores and metrics come from the API or the model card;
   explanatory sentences are derived from the scores rather than hard-coded.
3. **Explain every number.** Meters have plain-language hints ("known techniques", "learned patterns"),
   stat tiles say what the metric means, and the scoring formula is one click away.
4. **Be honest about failure.** The explainer shows a prompt that beats both layers, and the report shows
   the weakest generalisation results.
5. **Every element earns its place.** No gradients, glows, emoji or icon-per-item; colour only where it
   is evidence.

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
dark ink. Interaction (buttons, links, tabs, focus) uses ink rather than a brand hue; colour is reserved
for verdicts and, in bronze, for hidden text. Each theme has its own palette, chosen per theme rather
than inverted.

| Token | Black theme (default) | Light theme |
|---|---|---|
| Page | `#000000` | `#fbfaf7` |
| Panel | `#0c0c0e` | `#ffffff` |
| Hairline border | `#232328` | `#e2ded4` |
| Text / secondary / muted | `#f2f2f3` / `#b0b0b8` / `#8e8e98` | `#17191e` / `#4c525d` / `#626873` |
| Interaction | the text colour | the text colour |
| Hidden text | bronze `#d9a45a` | bronze `#9a6417` |

Muted text was darkened or lightened until it clears WCAG AA (4.5:1) on both backgrounds.

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
stylesheet; the whole CSS is about 10 KB gzipped, with no CSS framework and no icon library.

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
