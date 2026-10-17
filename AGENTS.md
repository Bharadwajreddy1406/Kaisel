# Kaisel — Agent Engineering Guidelines

## Mission

Kaisel is a message broker implemented entirely in Go.

The goals are:

1. Learn systems programming, Go, networking, OS concepts,
   concurrency, and distributed systems.
2. Build a correct, reliable, measurable message broker.
3. Maintain explainable engineering decisions and benchmarks.

## Engineering philosophy

Build → Test → Break → Understand → Improve → Benchmark.

Correctness precedes performance optimization.
Evidence precedes claims.
Simplicity precedes abstraction.

## Developer ownership

This is an educational systems-engineering project.

For architecture or foundational algorithms:

- Explain the problem and design alternatives first.
- Identify invariants, failure modes, and trade-offs.
- Do not implement until explicitly asked.
- Prefer guiding questions when the developer is
  attempting the implementation independently.

For scaffolding, tooling, documentation, and repetitive
work, implementation may proceed when requested.

Never hide an important design choice inside generated code.

## Implementation rules

- Use idiomatic Go.
- Prefer the standard library when sufficient.
- Introduce dependencies only with justification.
- Keep package boundaries intentional.
- Do not introduce abstractions without a real use case.
- Make resource ownership and lifecycle explicit.
- Document concurrency assumptions.
- Handle errors deliberately.
- Avoid global mutable state unless justified.
- Do not silently change protocol semantics.

## Testing

Every meaningful behavior change needs appropriate tests.

Include, where applicable:

- Normal behavior
- Invalid inputs
- Boundary conditions
- Concurrent operations
- Cancellation and shutdown
- Failure and recovery behavior

Use deterministic tests when possible.
Avoid arbitrary sleeps for synchronization.

Never claim a test passed unless it actually ran.

## Performance

Do not claim performance without measurement.

Record:

- Hardware and Go version
- Workload definition
- Message sizes
- Concurrency level
- Throughput and latency distribution
- Resource usage
- Benchmark methodology

Separate measured results from hypotheses.

## Documentation

Record architectural decisions under docs/adr/.
Keep learning notes under docs/learning/.
Record experiments under docs/benchmarks/.
Keep progress, current milestones, and next steps under docs/tracking/.
Keep README.md focused on the project description and AGENTS.md on standing
engineering guidelines.

For significant architectural changes, explain:

- Problem
- Alternatives
- Decision
- Trade-offs
- Verification strategy

## Codex behavior

Before significant implementation:

1. Inspect relevant code and documentation.
2. Summarize intended changes.
3. State assumptions and risks.
4. Define acceptance tests.

After implementation:

1. Run applicable verification.
2. Report results accurately.
3. Identify remaining risks.
4. Summarize files changed.
5. Suggest documentation updates when warranted.

Do not perform unrelated refactoring.
Do not add speculative features.
Do not rewrite passing tests merely to make
an incorrect implementation appear correct.
