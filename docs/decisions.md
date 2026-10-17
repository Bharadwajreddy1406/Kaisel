# Architecture decisions

Accepted choices come from the initial design discussion: the user selected a
shared work queue and delegated the failure guarantee and progression approach.
Other details remain proposed. Update a decision's status when it is reviewed;
do not treat a draft as an implemented guarantee.

| ADR | Status | Choice | Alternatives and trade-offs |
| --- | --- | --- | --- |
| 001 — Messaging model | Accepted | One shared work queue with competing consumers. | Pub/sub needs subscriber backlogs; a replay log needs retention and cursors. Both expand the first contract. |
| 002 — Failure guarantee | Accepted by delegation | Volatile storage and destructive receive; no acknowledgments or redelivery. | Volatile acknowledgments add ownership, retries, and duplicates but still lose state on restart. Durable acknowledgments also require storage and crash recovery. The chosen scope permits loss. |
| 003 — Milestone sequence | Accepted by delegation | Documentation first, then Go tooling and an in-process queue, then a local transport. | Networking first offers an earlier demo but combines queue correctness with framing, resource management, and disconnect semantics. |
| 004 — Ordering and concurrency | Proposed | FIFO admission/removal order; concurrent callers; no processing-completion order. | A single consumer simplifies observation but limits parallel work. Per-key ordering adds routing or partitioning. |
| 005 — Bounds and ownership | Proposed | Count, payload-byte, and message-size limits; reject full; copy payloads. | Blocking requires cancellation and bounded waiters. Eviction loses accepted work. Ownership transfer saves copying but makes buffer lifetime part of the public API. Numeric limits are provisional. |
| 006 — Lifecycle | Proposed | Cancelable empty receive; idempotent close rejects admission and allows drain. | Immediate discard loses queued messages. Waiting for processing requires consumer completion tracking and a shutdown deadline policy. |
| 007 — State ownership | Proposed | One mutex protects queue state and accounting. | An owner goroutine centralizes transitions but adds command/reply coordination. A buffered channel alone does not settle byte accounting and concurrent close. Validate before choosing the data structure. |

Record the review outcome for ADRs 004–007 before queue implementation. A decision
is revisable, but changes to delivery, ownership, or lifecycle also require updates
to the contract and acceptance scenarios.

## References

- [Go module management](https://go.dev/doc/modules/managing-dependencies)
- [Go memory model](https://go.dev/ref/mem)
- [Go race detector and its limits](https://go.dev/doc/articles/race_detector)
- [Publisher confirms versus consumer acknowledgments](https://www.rabbitmq.com/docs/confirms)
