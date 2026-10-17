---
name: systems-design-review
description: Review proposed Kaisel designs for correctness,
  concurrency, failure behavior, performance trade-offs,
  and testability before implementation.
---

# Systems Design Review

When reviewing a Kaisel subsystem:

1. Define its purpose and external contract.
2. Identify state ownership and invariants.
3. Identify concurrency and lifecycle boundaries.
4. Compare at least two feasible designs.
5. Describe failure modes and recovery behavior.
6. Identify performance and memory implications.
7. Propose tests that could falsify the design.
8. Recommend the simplest defensible design.

Do not modify production code during this review.

Distinguish:

- Established facts
- Assumptions
- Hypotheses requiring experiments

End with unresolved questions and a concrete
verification strategy.
