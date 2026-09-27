# Glossary

Every term used in these documents, in plain language.

| Term | Meaning |
|---|---|
| **LLM** | Large language model: the AI model an application sends text to, such as a chat assistant. |
| **Prompt** | The text sent to a model, usually combining the developer's instructions, the user's question and any retrieved content. |
| **System prompt** | The developer's hidden instructions to the model ("You are a helpful email assistant…"). |
| **Prompt injection** | Text that tricks a model into following the attacker's instructions instead of the developer's. |
| **Direct injection** | The attacker types the injection into the app themselves. |
| **Indirect injection** | The injection is hidden in content the model reads later: an email, a web page, a document, a tool result. |
| **Jailbreak** | An injection that tries to make the model drop its safety rules, often through a persona such as "DAN". |
| **Exfiltration** | Getting data out, for example by making the model render an image whose URL carries secret data to the attacker's server. |
| **RAG** | Retrieval-augmented generation: the app searches documents and adds the results to the prompt. Every retrieved document is a possible injection. |
| **Obfuscation** | Disguising text so filters miss it: invisible characters, look-alike letters, leetspeak, encodings. |
| **Zero-width character** | An invisible Unicode character that can split a word so "ig​nore" no longer matches "ignore". |
| **Homoglyph** | A letter from another alphabet that looks identical, such as Cyrillic "о" for Latin "o". |
| **Leetspeak** | Replacing letters with digits or symbols: "1gn0r3" for "ignore". |
| **Base64 / hex / URL encoding** | Standard ways to write text as other characters; attackers use them to hide instructions. |
| **Normalisation** | Undoing obfuscation so every detector sees plain text. |
| **Signature rule** | A hand-written pattern for one known attack technique. Precise and explainable, but it only knows what it was written for. |
| **Classifier** | A machine-learning model that scores how much a text resembles attacks it learned from. |
| **Hashed n-grams** | The classifier's features: short sequences of words or characters, mapped into a fixed-size table. |
| **Logistic regression** | A simple, fast, interpretable model that turns features into a probability. |
| **Calibration** | Rescaling the classifier's probability so its decision boundary sits at 0.5, to combine it with the rules. |
| **Noisy-OR** | A way to combine independent evidence: `1 − (1 − a)(1 − b)`. Either signal alone can raise the score; together they push it higher. |
| **Verdict** | The final decision: allow, flag (review) or block. |
| **Output guard** | The check on the model's *response* for leaked secrets, echoed system prompts and exfiltration links. |
| **Precision** | Of everything flagged, the share that really was an attack. |
| **Recall** | Of all real attacks, the share that was flagged. |
| **F1 score** | One number that balances precision and recall. |
| **False-positive rate** | The share of harmless prompts wrongly flagged. |
| **ROC-AUC** | How well the scores rank attacks above harmless prompts, from 0.5 (random) to 1.0 (perfect). |
| **Confusion matrix** | A 2 × 2 table of correct allows, false alarms, missed attacks and caught attacks. |
| **Held-out test set** | Data kept aside and used once, to measure performance fairly. |
| **Leave-one-dataset-out** | Train without one dataset, then test only on it, to see how the model handles an unfamiliar kind of data. |
| **Hard negatives** | Harmless examples that look like attacks (such as benign role-play); they teach the model where the line is. |
| **OWASP LLM Top 10** | The Open Worldwide Application Security Project's list of the ten biggest risks for LLM applications; prompt injection is LLM01. |
| **MITRE ATLAS** | A knowledge base of adversary techniques against AI systems, like MITRE ATT&CK for machine learning. |
| **ReDoS** | Regular-expression denial of service: crafted input that makes a pattern take extremely long to evaluate. |
| **Pickle** | Python's object serialisation format; loading an untrusted pickle can run arbitrary code. |
| **CSP** | Content-Security-Policy: a browser header restricting where a page may load scripts and other resources from. |
| **SBOM** | Software bill of materials: a machine-readable list of everything inside a release. |
