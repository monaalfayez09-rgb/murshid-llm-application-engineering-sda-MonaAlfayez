# Murshid Mini — Project Notes

Murshid Mini is my student learning project for LLM Application Engineering (SDA-AIE-213). The core notebook demonstrates application logic around a model-shaped interface. `FakeClient` returns fixed responses; it is deterministic mock code, not a trained or live language model.

## Data

The dataset is `synthetic-v1`: hand-written, fictional teaching fixtures. It contains an Arabic and an English answer to a public service question and two application records:

| Reference | Fictional owner | Status |
| --- | --- | --- |
| `REF-1001` | `demo-user-1` | `under_review` |
| `REF-1002` | `demo-user-2` | `approved` |

`REF-9999` is intentionally absent for a negative test. The demo session is `demo-user-1`. These are not real applicants or government records. The data exported by the core notebook is in [`synthetic_data.json`](../results/core/synthetic_data.json).

An extracted ticket has four fields:

| Field | Meaning |
| --- | --- |
| `service` | `service_info`, `application_status`, or `other` |
| `city` | Recognized city or `unknown` |
| `request_id` | A reference such as `REF-1001`, or `None` |
| `needs_clarification` | Boolean indicating that a status request lacks a reference |

The record fields `owner` and `status` support the read-only status lookup. The session user is a trusted test fixture supplied separately from the question.

## Design

`run_app` first checks for a valid cached answer to one of the exact public FAQ questions. On a miss, it calls `run_pipeline`, which guards the input, builds a visible prompt artifact, routes the request, validates the response, and adds a trace ID and timing. The prompt artifact is for teaching; `FakeClient` does not execute it.

- A public FAQ receives a fixed answer from `FakeClient`.
- A status request without a reference asks for the reference number without calling the status tool.
- A status request with a reference uses `check_status`, a read-only lookup that checks the fictional record owner against the demo session.
- Missing and unauthorized records produce the same outward answer, although their internal result codes differ.
- Cancellation requests, invalid tickets, blocked inputs, and unavailable services receive controlled responses.

Only successful answers to the exact public FAQ questions can enter the cache. Status answers are never cached. Cache entries expire after 300 seconds; keys include the question, language, model, prompt version, and directory version. Metrics record codes, cache hits, call counts, and latency without storing raw questions, references, owners, or answers. These mechanisms are educational controls, not production authentication or security.

## How to run

1. Open the [cumulative core notebook](../notebooks/murshid_MonaAlfayez.ipynb) in Google Colab.
2. Select a Python 3 CPU runtime. The core requires Python 3.9 or newer and only the standard library. It needs no API key, GPU, paid API, or model download.
3. Restart the runtime and run every cell from the top in order. Later days depend on definitions from earlier cells.
4. Inspect the printed assertions and compare the generated JSON files with [`results/core`](../results/core/).
5. Download the notebook and generated files before the Colab session ends. The optional homework notebooks run separately; the real-model exercises require package and model downloads.

## Results and interpretation

The supplied [`final_report.json`](../results/core/final_report.json) records **10 of 10 selected golden cases passing** (`pass_rate: 100.0`) in `mode=mock`. The cases cover the two public FAQs, an owned record, a missing record, a missing reference, an out-of-scope question, an unauthorized record, a blocked instruction, blank input, and an invalid ticket. A correct refusal counts as a pass when refusal is the expected behavior.

| Cache experiment | Recorded result |
| --- | ---: |
| Mock-client calls without cache | 3 |
| Mock-client calls with cache | 1 |
| Cache-hit sequence | `false`, `true`, `true` |
| Hit rate | 66.7% |
| Actual external inference API calls | 0 |

The report also includes a **hypothetical** cost example: 1,000 input tokens at $2 per million and 400 output tokens at $4 per million give $0.0036 per request. This is only an arithmetic illustration. The core made no external inference API calls and incurred no measured API charge. The numbers above describe the recorded run, not a guaranteed result for every environment.

## Limitations and next steps

The core uses no live language model or real authentication. Routing is finite and rule-based, and the guards cover selected patterns only. Passing 10 mock cases does not establish general language-model accuracy, comprehensive security, or production readiness. Future work could add more varied Arabic and English cases, review outputs semantically, and test new edge conditions without weakening the existing checks.
