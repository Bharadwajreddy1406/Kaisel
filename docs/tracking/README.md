# Project tracking

## Current milestone

Day −01: foundational design and repository review. Keep this milestone limited
to documentation and scaffolding; do not implement broker functionality.

## Next steps

1. Review the draft [messaging contract](../contract.md) and proposed ADRs 004–007
   in the [decision ledger](../decisions.md).
2. When implementation is requested, establish Go tooling and CI, then build
   the in-process queue with meaningful acceptance tests.
3. Add a local transport after the queue contract passes. Measure the queue as
   soon as it exists; establish baselines before setting performance targets.

## Learning and validation backlog

These are future exercises, not implementation work for the current milestone.

- Trace failure timelines across admission, response, removal, processing, and
  process exit. Identify where loss, duplication, and unknown outcomes occur.
- Study Go synchronization, cancellation, channel close behavior, and slice
  ownership. Use small standalone exercises to understand mutex versus owner
  goroutine trade-offs without building two broker implementations.
- Validate memory accounting: payload length versus retained backing arrays,
  metadata costs, temporary concurrent copies, and garbage-collection behavior.
- Define a benchmark workload before setting targets: payload distribution,
  producer/consumer concurrency, queue capacity, and consumer speed. Once code
  exists, record throughput, latency percentiles, rejected publishes,
  allocations, retained memory, and shutdown behavior with the Go version and
  machine details. Measure rejection separately from successful operations.
- Before networking, learn stream framing and partial I/O. Compare HTTP, which
  supplies framing, with raw TCP, which adds protocol-design work. Test slow or
  disconnected clients before claiming bounded network resource usage.
