# Documentation

Everything about CASSANDRA, from a two-minute overview to the evaluation details. The source code is
private; these documents describe what it does, how it was built and why.

## Where to start

| If you are… | Read | Time |
|---|---|---|
| **A recruiter or hiring manager** | The [README](../README.md), then the [Product tour](PRODUCT_TOUR.md) | 5 min |
| **An engineer reviewing the build** | [Architecture](ARCHITECTURE.md), [Detection engine](DETECTION_ENGINE.md), [Testing and quality](TESTING_AND_QUALITY.md) | 20 min |
| **A security reviewer** | [Security design](SECURITY_DESIGN.md), [Detection engine](DETECTION_ENGINE.md), [Model and evaluation](ML_EVALUATION.md) | 25 min |
| **A data scientist** | [Model and evaluation](ML_EVALUATION.md) | 15 min |
| **Watching a live demo** | [Demo walkthrough](DEMO_WALKTHROUGH.md) | 10 min |
| **New to the topic** | [Glossary](GLOSSARY.md), then the [Product tour](PRODUCT_TOUR.md) | 10 min |

## All documents

| Document | Contents |
|---|---|
| [Product tour](PRODUCT_TOUR.md) | Every page and feature, with screenshots: landing page, explainer, scanner, arena, report, themes, mobile |
| [Architecture](ARCHITECTURE.md) | Components, request flow, HTTP pipeline, engine internals, frontend, deployment, scaling |
| [Detection engine](DETECTION_ENGINE.md) | Normalisation, the full rule catalogue, the classifier, scoring with worked examples, the output guard |
| [Model and evaluation](ML_EVALUATION.md) | Data, features, training, tuning, every metric, generalisation, error analysis, reproducibility |
| [Security design](SECURITY_DESIGN.md) | Threat model for protected apps and for CASSANDRA itself, controls, hardening roadmap |
| [Interfaces: API, CLI, library](INTERFACES.md) | Every endpoint with real requests and responses, the CLI and its exit codes, library usage |
| [UI and UX design](UI_UX_DESIGN.md) | Why an explainer, the design system, colour validation, motion, accessibility, the redesign |
| [Testing and quality](TESTING_AND_QUALITY.md) | Test suites per module, end-to-end checks, fuzzing, benchmarks, CI/CD, static analysis |
| [Deployment and operations](DEPLOYMENT.md) | Docker, Compose hardening, Render hosting, releases, configuration, operations |
| [Design decisions & Q&A](DESIGN_DECISIONS.md) | Trade-offs, what changed during the build, answers to common questions |
| [Demo walkthrough](DEMO_WALKTHROUGH.md) | The 10-minute demo script |
| [Roadmap and limitations](ROADMAP.md) | Known limits and what comes next, in priority order |
| [Glossary](GLOSSARY.md) | Every term used in these documents, in plain language |
