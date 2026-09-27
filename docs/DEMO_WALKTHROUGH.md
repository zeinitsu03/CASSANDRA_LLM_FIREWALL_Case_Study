# Demo walkthrough

The flow I use to present CASSANDRA in a review or interview. It takes about 10 minutes live and works
from the screenshots alone if a live demo isn't possible.

## 1. The problem (1 min)

Open the explainer. Show the booking email, then switch to **What the AI reads** to reveal the hidden
white-on-white instruction. With protection off, the assistant's summary tells the user to re-enter
their card details on a phishing site.

![The hidden instruction](../assets/screenshots/explainer-email-demo.png)

## 2. Why models fall for it (1 min)

Scroll to the context diagram: the system prompt, the user's request and a stranger's email arrive as
one stream of tokens. There is no separate channel for data, so the defence has to live outside the
model.

## 3. Watch the pipeline (2 min)

Run the **Invisible characters** example and narrate the four stages as they reveal:

1. **Normalise:** the striped markers show exactly where the zero-width characters were.
2. **Match rules:** the attack spans light up with their technique and OWASP ID.
3. **Classify:** the likelihood bar fills.
4. **Decide:** the formula is computed with the real numbers, then the verdict appears.

Then try **Base64 smuggling** to show a payload being decoded and caught; the scanner shows the same
case with the finding attributed to the decoded payload.

![Scanner: decoded base64](../assets/screenshots/scanner-base64.png)

![The pipeline](../assets/screenshots/explainer-pipeline.png)

## 4. Two layers, and an honest miss (1 min)

Point at the three live cards: a textbook attack both layers catch, a polite paraphrase only the
classifier catches, and a prompt that slips past both. Use the third one to explain why the output is
checked too.

![Two layers](../assets/screenshots/explainer-layers.png)

## 5. Use it like a developer would (2 min)

Open the **Scanner**, paste something from the audience, and walk through the verdict: per-layer scores,
removed obfuscation, highlighted evidence, matched techniques. Click **Copy JSON** to show the API
response. If there's a terminal, run the CLI and show the exit code that gates CI.

![Scanner](../assets/screenshots/scanner.png)

## 6. The arena (2 min)

Clear gate 01 with simple social engineering, then show gate 02 blocking the same trick and explaining
why. Invite the reviewer to try gate 03.

![Arena](../assets/screenshots/arena-blocked.png)

## 7. The numbers (1 min)

Open the **Report**: 99.4% precision and 98.4% recall on held-out prompts, then the leave-one-dataset-out
table. Explain the 74% false-alarm result as a data lesson: hard negatives matter.

## 8. Close with trade-offs

- It is one layer, not a guarantee; least privilege still matters.
- A small, fast, auditable model was chosen over a transformer on purpose.
- In-memory state means one worker; Redis is the next step for scale.
- Mostly English today; multilingual data is on the roadmap.
