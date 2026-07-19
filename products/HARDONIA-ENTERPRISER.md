# 🤖 Hardonia Enterpriser — Local-First AI Drafting Suite

> **Sovereign AI for regulated work.** Every pipeline runs on *your* hardware. No cloud. No API keys. No per-token billing.

The Hardonia Enterpriser suite is a set of local-first AI drafting products built on the
**AIR engine** — a minimal declarative pipeline language that compiles to runnable bundles
for a local Ollama model. Each product targets a regulated vertical that cannot send
privileged data to third-party AI clouds.

---

## The Products

| Product | Vertical | Price | Pipelines | What it drafts |
|---------|----------|-------|-----------|----------------|
| **[Sentinel Note](https://aiautomatedsystems.ca/p/sentinel-note)** | Clinical | $297 one-time | 3 | SOAP notes, billing QA, referral summaries |
| **[OpsDraft](https://aiautomatedsystems.ca/p/ops-draft)** | Legal / Municipal | $197 one-time | 3 | Matter memos, permit letters, board minutes |
| **[LedgerDraft](https://aiautomatedsystems.ca/p/ledger-draft)** | Finance | $197 one-time | 3 | Client letters, reconciliation summaries, grant reports |
| **[HRDraft](https://aiautomatedsystems.ca/p/hr-draft)** | HR / Policy | $197 one-time | 3 | Policy drafts, offer letters, incident summaries |
| **[Hardonia Enterpriser](https://aiautomatedsystems.ca/p/hardonia-enterpriser)** | All-access | $497 one-time | 12 | Every pipeline above, one license |

**Managed install:** $149/mo flat — we set it up on your hardware, cancel anytime. No new infrastructure.

---

## Why local-first

| Concern | Cloud AI suites | Hardonia Enterpriser |
|---------|-----------------|----------------------|
| Data residency | Patient/client/HR data leaves the building | Stays on your hardware |
| Billing | Per-token, per-seat (unpredictable) | Flat one-time or flat monthly |
| Auditability | Opaque | Local audit log per draft |
| Compliance | Requires DPA + risk review | Inherent (no third party) |

---

## The AIR Engine

A pipeline is a short, readable file:

```air
pipeline "sentinel-note-soap" {
  model ollama/llama3-8b-instruct
  intake = load("./intake/{matter_id}.txt")
  soap = llm(prompt="You are a medical scribe. Draft a SOAP note from the intake.
                     Do not invent findings. Missing info -> 'Not documented.'
                     Intake:
                     {{intake}}")
  audit = log("soap-draft-audit")
  out = export("./notes/{matter_id}-soap.json")
}
```

Compile and run locally:

```bash
python3 compiler/air_compiler.py pipelines/soap-note.air --out /tmp/soap.json
python3 runtime/air_runtime.py /tmp/soap.json --root . --intake ./intake/ACME001.txt
```

No network calls. No credentials. Output + audit log written to your disk.

---

## Architecture

```
hardonia-enterpriser/
├── compiler/air_compiler.py     # .air -> runnable JSON bundle
├── runtime/air_runtime.py       # executes bundle against local Ollama
├── bundles/
│   ├── sentinel-note/           # 3 clinical pipelines
│   ├── ops-draft/               # 3 legal/municipal pipelines
│   ├── ledger-draft/            # 3 finance pipelines
│   └── hr-draft/                # 3 HR pipelines
└── scripts/suite-doctor.sh      # buy->deliver readiness self-check
```

Shared engine, zero duplication. Adding a 5th vertical is ~10 minutes: three `.air`
files, one Stripe price, one DB row.

---

## Verification

Every product passes `suite-doctor.sh`:

- Product page renders (200)
- Buy button → Stripe (302)
- Checkout session creates (`cs_live_`)
- Signed download returns the bundle (200)
- DB catalog row present
- AIR compile + dry-run clean

Fulfillment is proven end-to-end: a real purchase fires the Stripe webhook →
`hardonia-checkout-api` validates the signature → records the purchase → issues a
signed download URL. No human in the loop.

---

## License

Products are commercial. The AIR engine design and pipeline patterns are documented
here for transparency; the bundled models and runtime are licensed per the storefront.
