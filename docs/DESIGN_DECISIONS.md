# Design decisions & Q&A

The trade-offs behind CASSANDRA, and answers to the questions reviewers ask most often.

## In one paragraph

CASSANDRA is a prompt-injection firewall for applications built on language models. Before untrusted
text (a user message, an email, a web page, a tool result) reaches the model, it undoes common
obfuscation, matches known attack techniques with signature rules, and scores the text with a small ML
classifier. The two scores combine into one explainable verdict: allow, flag or block. After the model
answers, an output guard checks the reply for leaked secrets and exfiltration links. The same engine
powers a REST API, a CLI, a Python library, an interactive explainer and a red-team arena.

## Key decisions

| Decision | Alternative considered | Why |
|---|---|---|
| Two layers: rules + classifier | Classifier only | Rules are precise and name the technique; the classifier catches paraphrases. They fail differently, which is what layered defence needs |
| Noisy-OR combination | Weighted average; a stacked meta-model | Either layer alone can raise the alarm, agreement increases confidence, every verdict stays explainable, nothing extra to train |
| Normalise before detecting | Detect on raw text | One normaliser defeats zero-width, look-alike, leetspeak and encoding tricks for every detector at once |
| Logistic regression on hashed n-grams | A fine-tuned transformer | ~1 ms on a CPU, free to host, tiny and auditable; the generalisation gap is a data problem a bigger model would inherit |
| Weights as plain arrays | pickle / joblib | Loading a pickle can execute arbitrary code, not acceptable in a security tool |
| Check the output as well as the input | Input filtering only | Every input filter can be bypassed; the output guard catches the leak anyway |
| Honest evaluation (per source, leave-one-dataset-out) | A single headline accuracy | In-distribution scores flatter every model; unseen-dataset scores are what a user should expect |
| One engine behind every surface | Separate logic for API, CLI and UI | A verdict can't differ depending on where it was requested |
| Explainer-first web app | A dashboard | A dashboard shows numbers; an explainer makes a reviewer understand the problem and trust the tool |
| Simulated arena guardian | A real LLM | Free, fast, deterministic, and it still understands obfuscated text like a real model |
| Single worker, in-memory state | Redis from day one | Right-sized for the use case at ~260 requests per second per worker; documented as a limit |

## What changed during the build

| Earlier version | Final version |
|---|---|
| A dark "security console" dashboard | An interactive explainer plus restyled tools with light and dark themes |
| Rate limiting trusted the first `X-Forwarded-For` entry (client-controlled) | Counts trusted proxy hops from the right; ignores the header by default |
| Every visitor shared one rate-limit bucket behind the hosting proxy | The deploy declares its single proxy hop |
| Request size checked only after the body was in memory | Body-size limit enforced while streaming |
| One HTTP call per retrieved document | Batch endpoint, one classifier pass (2.7× faster on 20 documents) |
| CI referenced a GitHub Action tag that didn't exist | Every action pinned to a commit SHA |
| CI type-checked against Python 3.11 while running 3.12, and failed on NumPy's newer stubs | The checker targets the running interpreter; CI tests 3.11 and 3.12 |
| The scanner didn't announce results to screen readers | A hidden status line reads the verdict, risk and finding count |
| Hand-written converters between engine and API models | Mapped automatically from attributes |
| An always-true health field, unused normaliser fields, an unused error class | Removed in the final audit |

The lesson: measure before claiming, and remove what doesn't earn its place. Every number in the
README comes from a reproducible run, and the explainer shows a real miss on purpose.

## Common questions

**Why not just use a large model as the classifier?**
Cost and honesty. A transformer needs a GPU or a paid API, and the leave-one-dataset-out results show
the weak spots come from the training data: a bigger model trained on the same four datasets would
inherit them. The engine keeps a swappable interface so a transformer can be compared on the same
splits later.

**How do you handle false positives?**
In design: rules are precise and low-severity findings can't block on their own. In the data: benign
role-play prompts are included as hard negatives. In measurement: the false-positive rate is reported
per layer and per unseen dataset, and the jailbreak-dataset result (74% false alarms without its benign
prompts) is shown rather than hidden. In product: `flag` exists so borderline cases go to review
instead of being blocked.

**Can it be bypassed?**
Yes, and the explainer shows one. No input filter is complete. That's why the output is checked too,
and why the documentation tells integrators to keep the model's permissions minimal and require
confirmation for irreversible actions.

**What does "explainable" mean here?**
Every verdict lists the per-layer scores, the exact span of text that matched, the technique, its
OWASP and MITRE ATLAS IDs, and any obfuscation that was removed first.

**Does it store what users send?**
No. Text is processed in memory and never written to disk or logs; only verdicts and scores are logged.

**How would you take it to production?**
Shared rate-limit state for multiple replicas, an optional transformer model evaluated on the same
splits, multilingual and adversarially paraphrased training data, image digest pinning, and
integration middleware for popular LLM SDKs.

**Why is the source private?**
It contains the exact detection rules and the training pipeline. Publishing them would mainly help
someone tune bypasses. I'm happy to walk through the code live or grant temporary access.
