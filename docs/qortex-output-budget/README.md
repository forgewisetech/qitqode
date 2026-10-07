# Qortex output-budget investigation

This directory contains the reproducible evidence for task 780. The gateway
source is not part of this repository, so this is deliberately a probe and an
implementation specification, not a speculative gateway patch.

## Re-run the investigation

The probe requires a real Qortex key in the environment. It never prints the
key:

```sh
QORTEX_KEY="$QORTEX_KEY" bun scripts/qortex-budget-probe.ts \
  --tiers free,fast,economical,default,power \
  --budgets 1200,2400,4800,9600 \
  --repeats 3 \
  --mode both \
  --out docs/qortex-output-budget/measurements/rerun.jsonl
```

Add `--thinking 8192` (stream mode) to send the reasoning reserve the client
now asks for, so a sweep measures the visible room left by the split QitQode
actually requests rather than a request it never sends.

Use `--tools --mode stream` to exercise the native-tool request path. A run
with the built-in prompt considers a result usable only when all 40 numbered
items are present. Custom prompts have no generic completeness predicate; the
probe records the stop and leaves that judgement to the caller.

The JSONL files in `measurements/` are append-only raw observations. They keep
the gateway request ID, so an operator with gateway log access can correlate a
run without trusting a client-side timestamp.

## What the saved runs show

The sample is intentionally small and is evidence of the mechanism, not a
provider-quality benchmark. `EDGE_504_NO_BODY` rows are excluded from budget
outcome counts: the edge returned HTML/plain text without a gateway request
ID, so classifying those as budget exhaustion would be speculation.

| Tier | Reported reasoning overhead | Observed usable floor in this sample | Operational recommendation |
| --- | --- | --- | --- |
| `free` | Buffered median 87%, worst 93%; streaming 83–100% | 4,800 on 2/2 clean streaming runs; 9,600 on the buffered sample | 9,600+ until a larger p95/p99 sample is available |
| `economical` | Buffered median 88%, worst 89%; streaming 2–100% (one low-reasoning outlier) | 4,800 on 2/2 clean streaming runs; 9,600 on the buffered sample | 9,600+ |
| `fast` | No reasoning field reported in buffered observations | 1,200 produced a full answer on one clean run; other low-budget rows included edge 504s | Keep the existing tier limit; validate with clean repeated runs |
| `default` | One observation reported 12%; other rows omitted reasoning details | 2,400 for a full 40-item answer; 1,200 stopped at item 19 | 2,400+ for this prompt |
| `power` | No reasoning field reported in this sample | 2,400; one 1,200 run truncated while another completed | 2,400+ until reasoning usage is exposed and sampled |

The reasoning percentages are `reasoning_tokens / completion_tokens`, not a
claim about wall-clock time. Missing reasoning fields mean “not reported”, not
proof that the model used zero hidden tokens. The minimums above are the
lowest *observed* clean budgets meeting the probe's usability predicate; they
are not guarantees for arbitrary prompts.

The strongest buffered evidence is `free` at 4,800: one complete response and
one `finish_reason: "length"` / `stop_category: "truncated"` response. That
truncated response used all 4,800 completion tokens, of which 4,458 were
reasoning tokens and only 342 were visible. At 9,600, the free observations
completed with 84–88% reported reasoning share. Streaming free and economical
at 1,200–2,400 emitted `ERR_EMPTY_RESULT` when reasoning consumed 100% of the
allocation; economical also emitted `ERR_TRUNCATED_OUTPUT` after visible text
had started.

The tool probe accumulated an incomplete tool-call argument object at
1,000–1,400 tokens. `JSON.parse` failed on the partial argument payload and the
gateway returned a generic `ERR_STREAM` frame. This confirms the client's
mid-object symptom, although the exact provider-side serialization can vary
between native tool and compatibility-tool protocol modes.

## Visible output room at the client's 9,600 / 8,192 split

The client now plans a 9,600-token total on `free` and `economical` and asks
for an 8,192-token reasoning reserve (`opts.thinking.budget_tokens`), which
leaves roughly 1,400 tokens of visible answer on paper. `--thinking` was added
to the probe so a sweep sends exactly that request, and the two runs below
measure what the split actually buys. Raw rows:
`measurements/stream-9600-thinking-8192.jsonl` (short answer) and
`measurements/stream-9600-long-answer.jsonl` (long answer, with and without the
reserve).

Short answer — the built-in 40-item prompt, budget 9,600, reserve 8,192,
3 streaming runs per tier, all complete:

| Tier | Completion tokens | Reasoning tokens | Visible tokens |
| --- | --- | --- | --- |
| `free` | 642 / 600 / 3,463 | 62 / 56 / 2,956 | 580 / 544 / 507 |
| `economical` | 3,050 / 6,045 / 7,925 | 2,515 / 5,526 / 7,355 | 535 / 519 / 570 |

A 40-item answer needs only ~500–580 visible tokens, so the split is not the
constraint there — but note that `economical` spent up to 7,355 reasoning
tokens **while a 8,192 reserve was requested**, i.e. the reserve is not a cap.

Long answer — a 120-item variant of the same prompt (needs ~2,000 visible
tokens), budget 9,600, 2 streaming runs per tier per condition:

| Tier | Reserve sent | Outcome | Reasoning / visible tokens |
| --- | --- | --- | --- |
| `free` | 8,192 | 2× `ERR_TRANSPORT` (socket closed, no gateway record) | — |
| `economical` | 8,192 | 2× `ERR_EMPTY_RESULT` | 9,519 / 81 and 9,540 / 60 |
| `free` | none | 1× complete (120 items), 1× `ERR_TRANSPORT` | 74 / 1,986 |
| `economical` | none | 1× `ERR_TRUNCATED_OUTPUT`, 1× `ERR_EMPTY_RESULT` | 5,737 / 3,863 and 9,545 / 55 |

What this says about the split:

- **The reserve is advisory, not enforced.** With 8,192 requested,
  `economical` still consumed 9,519–9,540 of the 9,600 tokens on reasoning and
  returned nothing visible. The failure mode is identical with the reserve
  omitted, so the reserve neither guarantees the ~1,400 visible tokens nor
  bounds reasoning.
- **The ~1,400 visible figure is not what a caller gets.** Visible room is
  whatever reasoning leaves behind, and on `economical` that ranged from 55 to
  3,863 tokens for the same prompt.
- **9,600 is too small for a long answer on `economical`.** Every long-answer
  run on that tier failed (empty or truncated), with and without the reserve.
  `free` reasoned lightly (4–10% share in most runs) and completed the same
  prompt inside 9,600.

So the 9,600 total, not the 8,192 reserve, is the number that decides whether a
cheap-tier answer survives; raising the reserve would not change any row above.
`ERR_TRANSPORT` rows are recorded as observed — the socket closed with no
gateway request ID, so they are not attributed to budget exhaustion.

## Advisory and client amplifier

The live advisory observed by the probe was:

```text
free:       tierOutputLimit=128000, tierRecommendedStepTokens=32000
fast:       tierOutputLimit=50000,  tierRecommendedStepTokens=40000
```

Those fields describe a raw output ceiling and a step budget; neither reserves
visible-answer headroom or states a reasoning allowance. In QitQode,
`buildQlexBody` in
`qode/packages/qitqode/src/provider/sdk/qlex/index.ts` first applies an
explicit `max_tokens` and clamps it only to the advisory hard limit. If the
caller omitted it, the adapter uses the advisory recommended step budget.
The advisory is process-local and is only available after an earlier stream.
The Qlex model metadata currently exposes a 16,384 output limit, while the
generic output cap is 32,000. Consequently the client can aim at a number
that looks like answer capacity while the gateway spends most of it on
reasoning.

## Request-ID correlation

The three IDs in the original client report are:

```text
7051173f-a863-4c9c-bb5a-7975e996a29f
e058e925-12f4-49a8-8299-e192f3759b05
1c494564-cc1d-4c5d-8223-f09fbf24064e
```

Probe request IDs are recorded in all three JSONL files, including examples
such as `req_03d6b17e-be4e-4629-bb41-2b046b1c4703` (buffered free
truncation), `req_4b56b71c-a511-47f0-9290-8fad943016ed` (streaming
economical zero-output), `req_d13d21b0-e29c-4efb-918c-fc41833f2292`
(streaming economical truncation), and
`req_9bc04270-66c1-4a09-a3d6-28a9368ba8c7` (partial tool arguments).

Gateway log access was not available from this repository or the public API.
Therefore none of the three client IDs, nor the probe IDs, can honestly be
marked “confirmed in gateway logs”. The deployment owner must query the
request records for those IDs and verify: requested total budget, reasoning
tokens, visible tokens, provider finish reason, stream error category, and
billing outcome. A missing record or a record showing an edge-only 504 would
not support the budget diagnosis and must be flagged separately.

## Deliverables

- [`gateway-fix-spec.md`](./gateway-fix-spec.md) — implementation-ready
  gateway change, including admission, advisory, streaming, billing, and
  compatibility behavior.
- [`client-explanation.md`](./client-explanation.md) — concise explanation for
  the reporter.