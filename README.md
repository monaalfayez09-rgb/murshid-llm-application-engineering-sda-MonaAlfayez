# Murshid Mini — Mona Alfayez

### LLM Application Engineering · SDA-AIE-213

Build one notebook. Extend it across four days. Test and measure the result.

Murshid Mini is a small bilingual service-assistant project created for the LLM Application Engineering course. It demonstrates the application work around a model-shaped interface: input validation, structured routing, a read-only tool, guardrails, regression tests, and caching. The core project uses a deterministic FakeClient (mode=mock) with fixed answers. Separate optional homework notebooks explore a real local model and related engineering exercises.

## What you will build

The core assistant answers selected Arabic and English service questions and checks the status of fictional applications through a controlled, read-only lookup. It returns a trace ID and timing information, asks for missing details, refuses unsupported actions, tests expected behavior, and caches eligible public answers. All core records are synthetic.

## Start here

1. Open [my cumulative Murshid notebook](notebooks/murshid_MonaAlfayez.ipynb) in Google Colab.
2. Select a Python 3 runtime with CPU/no hardware accelerator.
3. Run the cells from the top in order. After a runtime reset, rerun the earlier cells before continuing.
4. Compare the printed checks with [my saved core results](results/core/) and download newly generated files from Colab before disconnecting.

The core notebook uses Python's standard library. It needs no API key, paid service, GPU, model download, or extra package installation. The optional real-model notebooks have their own setup and require internet access for packages and model weights.

## Four-day roadmap

| Day | What I added | My result |
| --- | --- | --- |
| 1 · Foundations | Synthetic FAQ directory, FakeClient, input checks, and a response contract | [Baseline results](results/core/baseline_results.json) |
| 2 · Structured tools | Validated tickets, a read-only status lookup, and a demo ownership check | [Day 2 results](results/core/day2_results.json) |
| 3 · Guardrails and tests | A controlled pipeline, input/output checks, and ten golden cases | [Day 3 results](results/core/day3_results.json) |
| 4 · Measure and finish | A public-answer cache, regression checks, measurements, and export | [Day 4 results](results/core/day4_results.json) · [Final report](results/core/final_report.json) |

These four stages extend the same notebook. Day 4 completes the core project.

## How the core application works

A question is validated and converted into a ticket describing its service, city, reference number, and whether clarification is needed. The ticket is checked before routing. General service questions use FakeClient to return a fixed answer. Application-status questions use a read-only lookup against synthetic records. The demo session identity is supplied separately from the question; claiming an identity in a question does not grant access. Cancellation requests are refused. Status requests are excluded from the shared public-answer cache.

## Data and example behavior

The [synthetic data file](results/core/synthetic_data.json) contains teaching fixtures, not real service records. For example, REF-1001 belongs to demo-user-1 and has status under_review; REF-1002 belongs to demo-user-2. The ownership check prevents demo-user-1 from receiving the other user's status. An application-status question without a reference asks for the reference number before any status lookup.

## Tests and measured results

My [final report](results/core/final_report.json) records 10 of 10 selected mock cases passing. The cases include normal answers, clarification, an out-of-scope question, refusals, and malformed output. A correct refusal is a passing result when the request should be rejected.

In the controlled cache experiment, three repeated public questions made three mock-client calls without caching and one with caching. The hit sequence was false, true, true, for a 66.7% hit rate. The core made zero external model API calls. See [additional metrics](results/core/metrics.json). Trace IDs and Python execution times can differ between runs; hypothetical cost values are examples, not billed charges.

## Optional homework

Each exercise below has its own notebook, separate from the cumulative core project.

| Day | Practice | Notebook | Saved result |
| --- | --- | --- | --- |
| 1 | Generate one answer with a real local model | [Real-model notebook](homework/day1_real_model_MonaAlfayez.ipynb) | [Live demo result](results/Day1/live_demo_result.json) |
| 2 | Validate and dispatch structured tool calls | [Tools notebook](homework/day2_tools_MonaAlfayez.ipynb) | Printed in notebook |
| 3 | Catch expected input errors and correct the input | [Errors notebook](homework/day3_errors_MonaAlfayez.ipynb) | Printed in notebook |
| 4 | Evaluate three real-model FAQ answers | [Evaluation notebook](homework/day4_evaluation_MonaAlfayez.ipynb) | [Evaluation result](results/Day4/evaluation_result.json) |

The Day 4 real-model report records three answers containing both “reference” and “number.” That keyword indicator is a narrow check, so each generated answer still needs manual review for meaning and unsupported claims.

## Limitations and next steps

FakeClient is a finite mapping, not a live language model. The core does not use real authentication, real application data, or a deployed service. Its guards and demo identity are teaching controls. Ten mock cases and three English real-model questions do not establish general accuracy, Arabic-language quality, or production readiness.

A useful next step is to test more varied English and Arabic questions, including cases where the context does not support an answer, and review each response manually. A real service would also require proper authentication, privacy controls, broader evaluation, and operational testing.

## Course attribution

Created by **Mona Alfayez** for **LLM Application Engineering — SDA-AIE-213**, taught by **Meaad Al-Marri** in the **SDAIA Academy** training context.

[Course learning repository](https://github.com/almiyead-rgb/murshid-llm-application-engineering-sda-aie-213) · [SDAIA Academy on GitHub](https://github.com/SDAIAAcademy)
