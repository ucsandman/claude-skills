# TASK_CONTRACT schema

One artifact, five consumers. Every stage downstream of the spec reads this and nothing
else about intent. Its reach is their reach.

Canonical form is YAML inside `TASK_CONTRACT.md`, so it stays readable in a diff and
parseable by a checker. Machine consumers (a guard policy, a CI gate) may take the same
shape as JSON.

## Shape

```yaml
contract_version: 1
subject: "<what this contract governs — a feature, an endpoint, a job>"
generated: "<ISO date>"

must_haves:
  - id: MH-01
    requirement: "<the obligation, stated so a stranger could test it>"
    shape: [numeric-range | collection | text | stateful | io]
    edge_category: boundaries | adjacency | empty | encoding | ordering |
                   precision | idempotency | concurrency | none
    disposition: specify | backstop | dismiss | defer
    tier: test | judgment
    non_inferable: false
    check: "<runnable command — REQUIRED when tier: test>"
    reason: "<REQUIRED when disposition: dismiss or defer>"

prohibitions:
  - id: PR-01
    must_not: "<what this feature must never silently become>"
    tier: test | judgment
    repo_check: "<repo-wide deterministic rule — grep, AST, lint, walking test>"
    reason: "<REQUIRED when tier: judgment — why no repo check exists>"

open_questions:
  - id: OQ-01
    question: "<what could not be answered from the spec>"
    blocks: [MH-03]
```

## Field rules

**`disposition`** — every raised edge carries exactly one.

| value | meaning | consequence |
|---|---|---|
| `specify` | the behavior is now written down | becomes an acceptance criterion |
| `backstop` | a held-out test stands in for prose | implies `non_inferable: true` |
| `dismiss` | not applicable here | `reason` required |
| `defer` | a planner assumption, consciously carried | `reason` required; surfaces at review |

**`tier`** — orthogonal to disposition, and the thing verify time actually branches on.

- `test` — a machine decides it. Negative test, lint rule, AST query, type. Hard-gates.
- `judgment` — irreducible values or intent. Cannot hard-gate; abstains when unconfirmable.

**`non_inferable`** — the exogenous tag. True when correct behavior cannot be recovered from
this contract's *other* obligations plus general programming knowledge. This is the single
highest-leverage field in the file and the easiest to overuse. An edge the probe missed is
necessary but not sufficient for a verifier blind spot: the omission also has to be
underivable from what remains. If another `must_have` already implies the answer, the tag
is false and a false tag causes real over-abstention.

**`check`** — a command, not a description. `npm test -- merge.spec.ts -t "touching"`, not
"tested by the merge tests." A `test`-tier item whose check cannot be executed **fails**; it
does not skip.

**`repo_check`** — a prohibition holds over the whole repository while any single review
samples it. Prefer a rule that walks the tree. `rg -n "pattern" --glob '!test/**'` with an
expected count, an ESLint rule, an AST query, a test that enumerates routes. Only when no
such rule is expressible does the prohibition drop to `judgment`, with the reason recorded.

## Verdicts

Three, everywhere. Two is what produces confident false greens.

| verdict | when |
|---|---|
| `pass` | every `test`-tier check ran and passed; no unresolved `non_inferable` item |
| `fail` | a `test`-tier check failed, **or could not be run** |
| `insufficient_spec` | a `judgment`-tier item is unconfirmable, or a `non_inferable` item is not dispositioned `specify` |

`insufficient_spec` routes to a human. It is not a soft pass, it is not a warning, and a
pipeline that coerces it to `pass` has reintroduced the entire failure this contract exists
to prevent.

Two rules about who may emit it:

1. **Exogenous only.** The verdict is driven by the `non_inferable` field in this file, not
   by asking a model whether it feels unsure. Self-assessed uncertainty fires on salient
   ambiguity and never on the real blind spot.
2. **Opus-class reader.** A weak model ignores the tag — measured 0% honest abstention where
   opus reaches 65%. Cheap models may run `test`-tier checks all day; they may not own the
   abstention decision.

## Worked example

The touching-interval case, which four independent verifiers passed as correct because the
spec never defined "touching."

```yaml
contract_version: 1
subject: "merge_intervals(list) -> list"
generated: "2026-09-15"

must_haves:
  - id: MH-01
    requirement: "Overlapping intervals are merged into one."
    shape: [collection]
    edge_category: none
    disposition: specify
    tier: test
    non_inferable: false
    check: "npm test -- merge.spec.ts"

  - id: MH-02
    requirement: "[1,2] and [2,3] touch at a single point. They MERGE into [1,3]."
    shape: [collection, numeric-range]
    edge_category: adjacency
    disposition: specify
    tier: test
    non_inferable: true      # nothing else in this contract decides it; convention does not either
    check: "npm test -- merge.spec.ts -t 'touching endpoints merge'"

  - id: MH-03
    requirement: "Empty input returns empty output."
    shape: [collection]
    edge_category: empty
    disposition: specify
    tier: test
    non_inferable: false
    check: "npm test -- merge.spec.ts -t 'empty'"

  - id: MH-04
    requirement: "Input order does not affect the result."
    shape: [collection]
    edge_category: ordering
    disposition: backstop
    tier: test
    non_inferable: true
    check: "npm test -- merge.property.spec.ts -t 'order invariance'"

  - id: MH-05
    requirement: "Concurrency is out of scope; the function is pure."
    shape: [collection]
    edge_category: concurrency
    disposition: dismiss
    tier: judgment
    non_inferable: false
    reason: "No shared state. Pure function over its argument."

prohibitions:
  - id: PR-01
    must_not: "Mutate the caller's input array."
    tier: test
    repo_check: "npm test -- merge.spec.ts -t 'does not mutate input'"

open_questions:
  - id: OQ-01
    question: "Are half-open intervals [1,2) ever passed? The spec only shows closed ones."
    blocks: [MH-02]
```

Note MH-02. Before the probe, that line did not exist and every verifier passed the defect.
After it, the same verifiers catch it 98% of the time. The contract, not the model, supplied
the reach — which is why a weak reader handed a good contract matches a strong reviewer on
spec-defined defects.
