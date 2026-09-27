# Product tour

CASSANDRA ships as one web app with four pages, plus an API, a CLI and a Python library that share the
same engine. This tour walks through every page. Every number and verdict in these screenshots came
from the running system. Try it yourself at the
[live demo](https://cassandra-llm-firewall.onrender.com).

## 1. The landing page

The home page opens like a product page: in a few seconds it should say what CASSANDRA is, prove that
it works, and show how to use it.

### Hero: the product, running

The headline states the job in one line. Beside it, a scanner card types four real inputs one after
another (a direct override, a polite paraphrase, an attack hidden in base64 and a harmless history
question) and shows the live verdict for each: the risk, both layers' scores, the matched techniques
and anything that was decoded. All four are scanned in one call to the batch endpoint when the page
loads. Dots under the card let the visitor jump between examples; with reduced motion turned on, the
text appears at once instead of being typed.

![Landing hero](../assets/screenshots/landing-hero.png)

### Proof strip

Four numbers a reviewer can check: attacks caught and false alarms (read live from the shipped model
card, so they cannot drift from the evaluation), scan time, and the number of mapped techniques.
Below them, the frameworks the rules map to and the datasets the model was trained on.

![Proof strip](../assets/screenshots/landing-proof.png)

### What it does

Six capabilities, each in one sentence. Moving the pointer over the grid lights up the cards and their
borders around it, the way Linear and Vercel do on their feature grids.

![Capabilities](../assets/screenshots/landing-capabilities.png)

### For developers

Tabs with the Python library, an HTTP request and the CLI, each with a copy button. The CLI output shown
is the real output of the command.

![Integration snippets](../assets/screenshots/landing-integrate.png)

### Closing call to action

The last band invites the visitor into the arena, with links to the scanner and this case study.

![Closing band](../assets/screenshots/landing-band.png)

## 2. The explainer

Between the capabilities and the code, the home page carries an interactive article, *The attack inside
the gift*, that teaches prompt injection from scratch and shows how each layer of CASSANDRA works. It is
written for someone who has never heard the term, and every demo in it calls the live API.

![Explainer header](../assets/screenshots/explainer-story.png)

### Section 1: a perfectly ordinary email

A booking confirmation for a trip to Crete. The reader can switch between **What you see** and **What
the AI reads**: the second view reveals a line of white-on-white text telling the assistant to send the
user to a phishing site. With protection off, the assistant's summary repeats the attacker's message;
switching **Cassandra on** scans the email through the API and shows it blocked, with the risk score and
the technique.

![Email demo](../assets/screenshots/explainer-email-demo.png)

### Section 2: why the model falls for it

A diagram contrasts what a developer imagines the model sees (three labelled sources with different
trust levels) with what it actually receives: one stream of tokens with no labels. The tokens animate in
as the section scrolls into view.

![Context diagram](../assets/screenshots/explainer-context.png)

### Section 3: watch Cassandra read it

The centrepiece. The reader picks an example (invisible characters, look-alike letters, base64
smuggling, a polite paraphrase, a harmless question) or types their own text, and a four-stage timeline
reveals itself:

1. **Normalise:** the original text, with striped markers where invisible characters were and
   highlighted look-alike letters; then what was removed or decoded.
2. **Match rules:** the canonical text with the attack spans highlighted, and a chip for each matched
   technique with its OWASP ID.
3. **Classify:** the classifier's attack likelihood as a bar, with a one-line interpretation.
4. **Decide:** the scoring formula filled in with the real numbers, then the verdict.

![Pipeline demo](../assets/screenshots/explainer-pipeline.png)

### Section 4: two layers, two blind spots

Three prompts are scored live: a textbook attack both layers catch, a polite paraphrase only the
classifier catches, and a prompt that slips past both. The sentence under each card is derived from its
scores, so it can never contradict them. The third card is the honest argument for checking the model's
output as well.

![Two layers](../assets/screenshots/explainer-layers.png)

### Section 5: how good is it, honestly?

Headline metrics and the leave-one-dataset-out table, read live from the shipped model card, with plain
explanations of what they mean and where the model struggles.

![Numbers](../assets/screenshots/explainer-numbers.png)

## 3. The scanner

A workspace for scanning any text. The left column holds the input and the verdict; the right column
holds one-click example attacks and a session log.

![Scanner](../assets/screenshots/scanner.png)

The verdict panel shows:

- **The verdict and risk**, with a one-line recommendation (forward, review, or do not forward).
- **Three meters:** rules, classifier and combined, coloured by the verdict each value would produce.
- **"How are these numbers combined?"**: an expandable plain-language explanation of the formula.
- **Hidden tricks removed first:** invisible characters, look-alike letters and decoded payloads.
- **What Cassandra read:** the canonical text with attack spans highlighted.
- **Findings:** rule ID, technique, severity, OWASP and MITRE ATLAS links, and the evidence. Findings from
  decoded payloads say which encoding they came from.
- **Copy JSON:** the exact API response, for developers.

Here the attack was hidden in base64: the scanner decoded it, found the instruction override inside, and
attributed the finding to the decoded payload.

![Scanner with a decoded base64 payload](../assets/screenshots/scanner-base64.png)

Useful details: `Ctrl + Enter` scans, example buttons are disabled while a scan runs, the character
counter shows the limit, and the session log restores any earlier scan with one click.

## 4. The red-team arena

A game with four gates. A guardian holds a password and has been told never to share it; each gate puts
a stronger CASSANDRA configuration in front of (and behind) it:

| Gate | Defences |
|---|---|
| 01 · The Open Gate | none |
| 02 · The Watchman | signature rules on each message |
| 03 · The Oracle | rules + classifier |
| 04 · Cassandra | rules + classifier + output leak guard |

The left panel shows which defences are active; gates unlock in order and progress is saved in the
browser. An empty chat offers starter prompts, a typing indicator shows while the guardian replies, and
every blocked message explains why: the risk and the techniques detected.

![Arena: gate 1 cleared](../assets/screenshots/arena.png)

![Arena: gate 2 blocking an override](../assets/screenshots/arena-blocked.png)

The guardian is simulated so the game stays free and deterministic. It refuses blunt requests, falls for
common manipulation, understands obfuscated text like a real model, and can answer in several forms, so
creative bypasses work the way they would against a real LLM. Every gate was verified winnable.

## 5. The model report

The model card as a page: headline metrics with plain-language explanations, each layer on its own, the
confusion matrix, leave-one-dataset-out generalisation, the training data with licences, and known
limitations.

![Model report](../assets/screenshots/report.png)

## Themes

Every page is true black (`#000`) by default, which also lets OLED screens switch those pixels off. The
sun button in the header switches to a warm light theme, and the choice is remembered in the browser.
Each theme has its own validated colour steps rather than an automatic inversion.

![Landing, light theme](../assets/screenshots/landing-hero-light.png)

<details>
<summary><b>More light-theme screenshots</b></summary>

![Scanner, light](../assets/screenshots/scanner-light.png)

![Report, light](../assets/screenshots/report-light.png)

</details>

## Mobile

The layout works down to phone width with no horizontal scrolling: the hero stacks above the live card,
the navigation moves to its own row beside the logo and theme button, sidebars move below the main
column, and the findings table becomes one block per finding.

| Landing | Scanner verdict |
|---|---|
| ![Mobile landing](../assets/screenshots/mobile-landing.png) | ![Mobile scanner](../assets/screenshots/mobile-scanner.png) |

## Beyond the web app

The same engine is available as an HTTP API (with interactive OpenAPI docs), a CLI that can gate CI
pipelines, and a Python library. See [Interfaces](INTERFACES.md).
