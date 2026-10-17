# Kaisel

Kaisel is a Go-based message broker in early development, focused on predictable
behavior, bounded memory usage, and measurable performance.

The initial design is a shared, in-memory FIFO work queue with competing
consumers and at-most-once delivery per admitted message.
