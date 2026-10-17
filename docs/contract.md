# First messaging contract

Status: the shared work queue, volatile destructive delivery, and in-process-first
scope are chosen. The detailed rules below are a draft to review before queue
implementation.

## Model and delivery

- One shared queue in one process; multiple producers and competing consumers.
- Payloads are opaque bytes. Empty payloads are allowed and consume a count slot.
- A successful publish means the message was admitted to broker-owned memory.
  It does not mean a consumer received or processed it, or that it is durable.
- Each admitted message may be returned by receive at most once. Receive removes
  the message; there is no acknowledgment or broker-managed redelivery.
- Consumer failure after removal loses the message. Process failure loses every
  queued message. A new instance starts empty.
- Repeating a publish creates another message. There is no producer deduplication
  or end-to-end exactly-once guarantee.

## Ordering and ownership

- Successful admissions define one FIFO removal order. A publish that finishes
  before another starts precedes it. Overlapping publishes may be admitted in
  either order; caller start times do not define an order.
- Concurrent receives remove in that FIFO order, but return scheduling and
  application processing may complete in a different order.
- Publish copies accepted payload bytes. The caller may reuse its buffer after
  the call returns, but must not mutate it concurrently with the call.
- A returned payload belongs to the receiver; the broker does not reuse or mutate
  its backing storage.

## Limits and overload

The queue has three positive limits. Proposed starting values, to validate rather
than treat as measured production settings:

| Limit | Proposed value |
| --- | --- |
| Queued message count, N | 1,024 |
| Total queued payload bytes, B | 8 MiB |
| Individual payload bytes, S | 64 KiB |

- Require S <= B. Reject invalid configuration before accepting messages.
- Never admit a message if it would exceed N, B, or S. Exact boundaries are valid.
- Reject a full queue immediately. Do not block publishers, evict accepted work,
  or create a background backlog.
- Failed admission leaves queue contents and accounting unchanged. Removal
  releases the message's count and payload-byte capacity.
- The broker must not retain an arbitrarily large caller backing array for a
  short payload, or create unbounded pending copies under concurrent publication.
- B bounds retained queued payload, not total process memory. Metadata, temporary
  allocations, runtime overhead, caller buffers, and already-received payloads
  need separate accounting when measuring memory.

## Empty receive, cancellation, and close

- Receive waits on an empty open queue and supports cancellation.
- Cancellation observed before removal returns a cancellation error and removes
  nothing. If removal wins a concurrent cancellation race, return the message
  successfully. Cancellation must not report failure after silently removing it.
- Close is idempotent and prevents new admissions. A publish overlapping close
  succeeds only if its admission happens before the close transition.
- Existing messages remain drainable. Once closed and empty, receive returns a
  closed result, including receivers already waiting.
- Close does not wait for consumers or application processing. It does not
  promise that draining will finish before process exit.
- Admission, removal, close, and their capacity changes must each have a single
  atomic state transition.
- Distinguish invalid configuration, oversized payload, full queue, closed queue,
  and canceled receive. Internal invariant failures must not be hidden as success.

## Acceptance scenarios

Write executable tests alongside the first implementation. These scenarios are
specifications, not tests that currently exist or pass.

| Scenario | Required observation |
| --- | --- |
| Payload isolation | Mutating a publisher buffer after success does not change received bytes. |
| FIFO | Sequentially admitted A, B, C are removed as A, B, C. |
| Competing consumers | After draining, each successfully admitted test item appears exactly once across consumers. |
| Concurrent producers | All successful admissions are accounted for; each producer's nonoverlapping publishes preserve order. |
| Boundaries | Test count, aggregate bytes, and per-message limits independently, at the exact boundary and one beyond. |
| Failed admission | Rejection leaves accepted contents and available capacity unchanged. |
| Empty payloads | Count limits prevent unlimited zero-byte messages. |
| Capacity reuse | Removal makes its count and byte capacity available for another admission. |
| Cancellation | Cancel an empty receive promptly; a later publication remains receivable. |
| Close and drain | Reject new publications, drain accepted items, then report closed; repeated close is safe. |
| Transition races | Coordinate publish/receive/close and cancellation races; preserve accounting and delivery rules under the race detector. |
| Invalid limits | Reject nonpositive limits and S greater than B. |

Use small limits, explicit synchronization rather than sleeps, unique test item
identities, and bounded test deadlines. Verify concurrent operations complete;
do not infer correctness only from the absence of a data race.

## Later network contract

Before socket code, decide framing, versioning, connection and buffer limits,
maximum frame size, deadlines, and exposure/authentication scope. Loopback-only
is the recommended first service scope.

A network publish response would confirm admission only. Disconnect after
admission but before the response leaves the publisher uncertain; retry can
duplicate work. Test partial/coalesced input, malformed or oversized frames,
slow clients, disconnects, and shutdown with active requests. Transport choice
and these limits remain undecided until that milestone.
