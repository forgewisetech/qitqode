# Future Features

Parking lot for planned QitQode features that are documented but deliberately deferred.

This folder lives outside `qode/` on purpose: the CLI fork is MIT-licensed and published, and internal roadmap notes should not ship with it.

Each file describes one feature: what it is, why it is deferred, where the reference implementation lives (usually `external/opencode`, read-only), and QitQode-specific porting notes and constraints.

| Feature | Blocked on | Effort center |
|---|---|---|
| [Image support](image-support.md) | QLEX backend contract work | Backend first, then CLI |
| [Background jobs](background-jobs.md) | Nothing (CLI-side) | CLI |
| [HTTP recorder for tests](http-recorder.md) | Nothing (test infra only) | Test suite |

Rule of thumb: never import from `external/` — it is reference material only. Features must be re-implemented to fit QitQode's feature-first SRP architecture (Effect Service + Bus events + own Hono routes per feature).
