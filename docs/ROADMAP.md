# Roadmap and limitations

## Known limitations

| Limitation | Impact | Mitigation today |
|---|---|---|
| No input filter is complete | A carefully phrased injection can pass | The output guard catches leaks anyway; documentation tells integrators to keep model permissions minimal |
| Mostly English training data | Weaker detection in other languages | Rules cover German, Spanish and French instruction overrides |
| Generalisation gaps on unseen data styles | Lower recall on new attack styles; more false alarms on unusual benign prompts | `flag` sends borderline cases to review; metrics are published per dataset |
| In-memory state | One worker per instance | ~260 requests per second per worker is enough for the intended use |
| Simulated arena guardian | Not a real LLM | It understands obfuscation like a real model, so bypass techniques transfer |
| Swagger UI loads from a CDN | `/api/docs` is exempt from the strict CSP | Can be blocked at the proxy; the app itself makes no third-party requests |

## Roadmap, in priority order

1. **More diverse benign data.** The evaluation shows hard negatives are the biggest lever for false
   alarms.
2. **Multilingual training data**, attacks and benign prompts alike.
3. **Adversarial paraphrase generation** to harden the classifier against rephrasings.
4. **A transformer classifier** behind the same interface, compared on exactly the same splits, kept only
   if it earns its cost.
5. **Shared state (Redis)** for multi-replica deployments.
6. **Drop-in middleware** for popular LLM SDKs, so integrating is one line.
7. **A persistent arena leaderboard** and a library of community-submitted bypasses feeding back into
   training data.
