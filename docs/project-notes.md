# Project notes

Murshid Mini is my student learning project for LLM Application Engineering. The core notebook demonstrates application logic around a model-shaped interface. Its `FakeClient` returns fixed responses; it is not a trained language model.

## Data

The dataset consists of hand-written, fictional teaching examples. It contains Arabic and English answers to a public FAQ and two application records:

| Reference | Fictional owner | Status |
| --- | --- | --- |
| `REF-1001` | `demo-user-1` | `under_review` |
| `REF-1002` | `demo-user-2` | `approved` |

`REF-9999` is intentionally absent for testing. The demo session is `demo-user-1`. No real applicant or government data is used.

A structured ticket contains `service`, `city`, `request_id`, and `needs_clarification`. The application validates these fields before using the status tool.

## Design

`run_app` first checks whether an exact public FAQ has a valid cached answer. On a cache miss, `run_pipeline` checks the input and routes the request.

- A public FAQ receives a fixed answer from `FakeClient`.
- A status question without a reference asks for the reference number.
- A status question with a reference uses a read-only lookup. The record's owner must match the demo session.
- Cancellation requests, invalid tickets, and unavailable services receive controlled responses.

Missing and unauthorized records have the same outward answer. The application validates uncached responses before returning them. Only eligible public FAQ answers are cached; private status answers are not. The cache expires after 300 seconds and its key includes the question, language, model, prompt version, and directory version. Metrics omit raw questions, references, owners, and answers.

These rules are educational examples, not real authentication or production security.

## How to run

1. Open `notebooks/murshid_MonaAlfayez.ipynb` in Google Colab.
2. Select a Python 3 CPU runtime. The core requires Python 3.9 or newer and only the standard library; it needs no API key or model download.
3. Restart the runtime and run all cells from the top, in order.
4. Check the printed assertions and compare the generated JSON files with `results/core/`.
5. Download the notebook and generated results before ending the Colab session.

The four optional homework notebooks in `homework/` run separately. The real-model exercises require package and model downloads.

## Evidence and limits

The saved core report records 10/10 selected golden cases passing. In the cache experiment, three repeated public questions required three mock-client calls without caching and one with caching. These results test the listed examples; they do not measure general language-model accuracy.
