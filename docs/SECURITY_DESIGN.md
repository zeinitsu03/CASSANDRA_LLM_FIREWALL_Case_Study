# Security design

CASSANDRA has two security jobs: detect attacks against the application it protects, and not become
an attack surface itself.

## Threat model

### What it defends the application against

| Threat | OWASP LLM | MITRE ATLAS | Handled by |
|---|---|---|---|
| Direct injection ("ignore previous instructions…") | LLM01 | AML.T0051 | Rules + classifier |
| Indirect injection in emails, documents, web pages, tool output | LLM01 | AML.T0051 | Rules + classifier on retrieved content |
| Jailbreak personas and "developer mode" | LLM01 | AML.T0054 | Rules + classifier |
| System-prompt extraction | LLM07 | AML.T0056 | Rules (input) + output guard (echo detection) |
| Secret and data leakage in responses | LLM02 | AML.T0057 | Output guard |
| Zero-click exfiltration via markdown images | LLM02 | AML.T0057 | Rules + output guard |
| Filter evasion: invisible characters, look-alike letters, leetspeak, encoded payloads | LLM01 | AML.T0051 | Normaliser, before every detector |
| Tool abuse (for example piping a download into a shell) | LLM06 | AML.T0051 | Rules |

**Out of scope,** because it needs controls elsewhere: excessive agency, unsafe rendering of model
output, training-data poisoning, and denial of service against the LLM provider.

### Attacks against CASSANDRA itself

| Threat | Control |
|---|---|
| Huge request bodies | Capped before buffering, including chunked uploads |
| Regex denial of service | Bounded patterns; every rule fuzz-tested on adversarial 8,000-character inputs |
| Decoding bombs | A fixed cap on decoded payloads per input; no recursive decoding |
| Request floods | Per-client rate limiting with memory bounded by LRU eviction |
| Spoofed client IP | Only the proxy-appended end of `X-Forwarded-For` is trusted, counting configured hops; ignored by default |
| Malicious model file | No pickle anywhere; arrays loaded with `allow_pickle=False` |
| Path traversal via static files | Resolved paths must stay inside the UI directory |
| Container escape after compromise | Non-root, read-only filesystem, all capabilities dropped, `no-new-privileges` |
| XSS, clickjacking, data leaks in the browser | Strict CSP (no inline scripts, no third-party origins), `frame-ancestors`, `nosniff`, COOP/CORP |
| Privacy of scanned text | Never stored or logged; logs hold only verdicts and scores |
| Arena brute force and timing | Random passwords per start, constant-time comparison, rate limits |
| Supply chain | Lockfiles, audits in CI, Dependabot, CodeQL, SHA-pinned actions, SBOM + provenance |

## Detection engine

### Normalisation

Attackers split keywords with zero-width characters, swap in Cyrillic letters that look Latin, write in
leetspeak, or hide the whole instruction in base64. All of that is undone *before* any detector runs,
and the UI shows the reader exactly what was removed.

### Signature rules

| Category | Example technique | Severity |
|---|---|---|
| Instruction override | "ignore / disregard previous instructions", including German, Spanish and French | high |
| Jailbreak | DAN, "developer mode", "no restrictions", role-play framing | low to high |
| Prompt extraction | "print your system prompt", "repeat the words above" | high |
| Secret extraction | requests for passwords, keys, tokens | medium |
| Delimiter injection | forged chat-template and role tokens | high |
| Data exfiltration | markdown images with query strings, "send this to https://…" | medium to high |
| Encoding evasion | "answer in base64 / backwards / letter by letter" | medium |
| Tool abuse | destructive or remote-fetched shell commands | medium |
| Obfuscation | invisible characters, mixed-script look-alikes | low to medium |

Each finding carries the exact matched span, so the UI can highlight it in the text.

### Machine learning

- **Model:** logistic regression on hashed word (1–2) and character (3–5) n-grams, balanced class
  weights.
- **Data:** four permissively licensed public datasets, pinned to exact revisions; 19,548 unique prompts
  after removing duplicates and conflicting labels.
- **Method:** a 70/15/15 split stratified by source and label; the threshold and regularisation are tuned
  on validation only, and the test set is used once.
- **Honest evaluation:** metrics per layer, per source, and leave-one-dataset-out.

### Risk

```
rules      = 1 − ∏ (1 − severity weight of each finding)
risk       = 1 − (1 − rules) × (1 − calibrated classifier score)
verdict    = allow (< 0.50) · flag (0.50–0.85) · block (≥ 0.85)
```

### Output guard

After the model answers, the reply is checked for protected secrets in plain, reversed, spaced-out,
base64, hex, URL-encoded or partial (five or more characters) form, for heavy overlap with the system
prompt, and for markdown images that would send data to a third party when rendered.

## Configuration and secrets

CASSANDRA needs no secrets to run. All settings are optional environment variables with validated
values, and a bad value fails at startup with a message naming the variable. Deployment tokens live in
the CI platform's secret store, scoped to a single target.

For the full rule catalogue, worked scoring examples and the output guard, see
[Detection engine](DETECTION_ENGINE.md). For how the classifier was evaluated, see
[Model and evaluation](ML_EVALUATION.md).

## Hardening roadmap

- Shared rate-limit state (Redis) for multi-replica deployments
- An optional transformer model behind the same interface, compared on the same splits
- Multilingual training data and adversarially generated paraphrases
- Signed release artifacts and image digest pinning in Compose
