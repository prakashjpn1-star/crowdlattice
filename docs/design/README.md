# Low-level designs

Each service owns one state transition boundary. Read [transactions](transactions.md) and [state machines](state-machines.md) first.

- [Room lifecycle ordered publish and snapshots](publication.md)
- [Snapshot to stream handoff replay and reset](replay.md)
- [Hot-room placement and shared upstream feeds](fanout.md)
- [Capacity grants and connection generations](sessions.md)
- [Slow viewers bounded buffers and reconnect control](backpressure.md)
- [Worker drain loss and safe reconnection](recovery.md)
- [Presence and actual connection accounting](presence.md)
- [Storage](storage.md)
- [Browser and API modules](browser.md)
- [Observability](observability.md)

Contracts and examples are linked from the [documentation guide](../README.md).
