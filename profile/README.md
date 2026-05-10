# Input Recovery

We help Canadian businesses recover overpaid GST/HST/QST. Our software audits a company's transaction history, surfaces tax errors with audit-defensible evidence, and produces filing-ready packages — work that historically takes a CRA-trained practitioner weeks.

## Repositories

| Repo | What it does |
|---|---|
| [`recovery-engine`](https://github.com/inputrecovery/recovery-engine) | The audit engine + internal dashboard going forward. Deterministic pipeline (normalize → clean → map → detect) running 69 detector rules across GST/HST/QST/PST. TypeScript end-to-end: Hono backend, React 19 + Tailwind v4 dashboard, SQLite locally / Postgres in cloud. AWS infra via CDK. |
| [`tax_recovery_web_v4`](https://github.com/inputrecovery/tax_recovery_web_v4) | The previous-generation product — currently in production with first customers. Express + MySQL/Drizzle + tRPC, hosted on Manus. Source of truth for detector logic, rate tables, and audit semantics being ported into `recovery-engine`. We're actively migrating off Manus dependencies (auth, LLM proxy, storage). |

## Core principles

These are non-obvious decisions that shape how we build. Worth knowing before proposing changes.

- **Deterministic rules over LLM classification.** Detectors are pure TypeScript functions with citable formulas tied to statutory rates. CRA audit defense requires "this finding fires because §X.Y says Z" — an LLM saying "probably HST overpayment" doesn't survive scrutiny. LLMs are used for advisory questions and OCR field extraction, never for finding detection.
- **Every finding is reviewed by a CRA-trained practitioner before it ships.** Andrew Adolph (ex-CRA) is the human-in-the-loop on every customer delivery. Engine output is a draft, not a submission.
- **Audit defensibility is the product.** Each finding stores the raw values that triggered it, the rule version, the rate applied, and the source row. If we can't reconstruct *why* a finding fired six months later, the recovery is unprovable.
- **Canadian data residency.** Cloud infra runs in `ca-central-1`. Customer transaction data and invoice attachments don't leave Canada.

## Stack at a glance

- **Languages:** TypeScript everywhere
- **Cloud:** AWS (`ca-central-1`), CDK for infra-as-code
- **CI/CD:** GitHub Actions, OIDC federation to AWS (no static credentials)
- **Domain references:** [`PRODUCT_SPEC.md`](https://github.com/inputrecovery/recovery-engine/blob/main/PRODUCT_SPEC.md) and [`DETECTOR_SPEC.md`](https://github.com/inputrecovery/recovery-engine/blob/main/DETECTOR_SPEC.md) in `recovery-engine` are the load-bearing docs

## Team

Small. Engineers plus a CRA-trained tax practitioner reviewing findings. Pre-revenue, first customer in pilot, hiring slowly and intentionally.

## Contact

- Brian Norlander — `brian@inputrecovery.com`
- Website — <https://inputrecovery.com>
