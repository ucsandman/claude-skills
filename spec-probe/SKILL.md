---
name: spec-probe
description: "Run before implementing anything non-trivial: surface omitted edge cases and must-NOT constraints into a verification-tiered TASK_CONTRACT.md, so the verifier has something to check against. Use when starting a feature, a bugfix with unclear intended behavior, or any task where a reviewer would have to guess what correct means. Also use when asked to spec-probe, write a task contract, or check what the spec left out."
---

# Spec probe

A verifier catches only what the spec named. When an obligation was never stated and no
convention determines it, the comparison is vacuous — any behavior is "consistent with the
spec," and the verifier passes the defect at ~93% confidence. Measured: 100% confident false
pass with the edge omitted, 98% catch once the same edge is written into the spec
(n=210, Wilson 95% CI). Re-running a bigger model never closes that gap; a ~30x cost
increase buys no additional recovery.

So do not strengthen the reader. Widen the artifact, before code exists.

Output is one file: `TASK_CONTRACT.md` at the repo root (or beside the feature's plan).
Schema and worked example: `references/contract-schema.md`. Read it before writing one.

## Steps

1. **Enumerate requirements.** One line each, from the request plus whatever spec exists.
   Terse prose is the failure mode here — a requirement with no discernible shape yields
   no edges. Expand a one-liner into its actual obligation before classifying it.

2. **Shape-classify each requirement** as one or more of: `numeric-range`, `collection`,
   `text`, `stateful`, `io`. Do this yourself as a reading task. Do NOT delegate it to a
   regex or a keyword table: a deterministic classifier under-fires on roughly half the
   applicable edges. If you delegate it to a subagent, that subagent is sonnet or better.

3. **Run the eight-category edge checklist** against each shape. Ask every one out loud;
   a category you skip is a category you cannot later claim was dismissed:

   | Category | The question |
   |---|---|
   | boundaries | What happens exactly at min, max, zero, one? |
   | adjacency / touching | Do two things that merely touch count as overlapping? |
   | empty / degenerate | Empty list, empty string, single element, all-equal elements? |
   | encoding | Bytes vs code points vs graphemes; normalization; case; locale? |
   | ordering / stability | Is input order meaningful? Are ties broken, and how? |
   | precision / overflow | Float rounding mode, tie-breaking, integer width, currency? |
   | idempotency | Run it twice — same result? Same side effects? |
   | concurrency | Two callers at once; retries; partial failure; ordering across writers? |

   `idempotency` on text-shaped work and `precision` tie-breaking are known blind spots of
   this checklist. Ask them even when the shape does not obviously invite them.

4. **Disposition every raised edge** — `specify`, `backstop`, `dismiss` (with a recorded
   reason), or `defer` (an explicit assumption the planner owns). An edge with no
   disposition is not a finished contract.

   Tag `non_inferable: true` only when the correct behavior genuinely cannot be recovered
   from the spec's *remaining* obligations plus general programming knowledge. This tag is
   the load-bearing part of the whole mechanism, and its precision is load-bearing too: a
   false tag makes a strong verifier over-abstain on a real, spec-determined bug about a
   third of the time. Do not spray it.

5. **Prohibition probe, two stages.** Shape classification says what a requirement should
   handle; it is silent on what the feature must not become.
   - *Stage 1, recall.* Per requirement, adversarially: **"what could this feature silently
     become that the author would not want, but the spec does not forbid?"** Over-generate.
     Framing matters — this phrasing reaches the product-values prohibitions that a
     role-played design review misses about half the time.
   - *Stage 2, precision.* One pass over the candidates, keeping only genuine values and
     safety prohibitions. Drop routine engineering must-nots ("must not leak memory").
     Expect roughly nine candidates to collapse to two or three.

   Prohibitions are not hard because they are negative — a verifier catches a violation
   just as well under "must" as under "must NOT." They are hard because they quantify over
   the whole repository while any one review samples part of it. So every prohibition that
   can carry a `repo_check` gets one: a grep, an AST rule, a lint rule, a test that walks
   the tree. A prohibition with no repo-wide check is a judgment-tier item, and say so.

6. **Assign a verification tier to every item.** `test` if a machine can decide it
   (negative test, lint, AST rule, type). `judgment` otherwise. A `test`-tier item names
   its check as a runnable command.

7. **Back-translate, to catch an inverted contract.** Hand the decomposed `must_haves`
   for one requirement — and nothing else — to a fresh reader, and have it reconstruct what
   the requirement was for. Compare to the original. Intent survives decomposition only when
   it is explicitly encoded as a rule; a holistic obligation left as pure mechanism drifts,
   and in the worst measured case inverted into the exact anti-goal the product forbade.
   Run this on every requirement whose point is a stance rather than a mechanism. When it
   drifts, the fix is to route the stance to a `judgment`-tier must_have, not to write a
   denser mechanism.

8. **Write `TASK_CONTRACT.md`** and keep it next to the work, in version control. The
   contract is the artifact every later stage reads. A one-shot clarifying question asked
   in chat is not a contract; it evaporates.

## Verify time

The contract is consumed, not just filed:

- `test`-tier items **hard-gate**. If the check cannot be run, that is a failure, not a skip.
- `judgment`-tier items and anything tagged `non_inferable` that was not resolved to
  `specify` return **`insufficient_spec` → human_needed**. The verifier abstains instead of
  emitting a confident green.
- The abstention must be driven by the tag in the contract, never by asking the model to
  abstain when it feels unsure. Self-assessed uncertainty fires on whatever ambiguity the
  model happens to notice and never on the actual blind spot — the model cannot feel it.
  Exogenous tagging routes correctly even when the verifier cites the wrong reason, which
  is exactly the property a blind spot needs.
- Whoever reads `non_inferable` tags is opus-class. The weakest tier is flag-deaf: measured
  0% honest abstention under the same tag that moves opus to 65%.
- Never ship self-disconfirmation ("now argue against your own verdict") as a control. It is
  inert on a capable model and can talk it out of a correct catch.

## What this does not do

It does not replace held-out tests. On genuine blind spots — edges this checklist fails to
name *and* that the spec's other obligations do not imply — a verifier still false-passes
most of the time even when the edge is surfaced. The held-out oracle stays. Keep writing it.

Do not build an intermediate representation to compile contracts from. It was measured and
it does not pay: across a 48-requirement spec, exactly one requirement showed the defect
such a layer repairs, and the layer grows an informally-specified language of its own.
