# Fault harness and fixture contracts

A seeded championship room and an independent small room use deterministic score/status events. Clients record generation, last-applied sequence, duplicates/gaps and lag. After recovery compare state through a shared final sequence using an independent reducer.

```mermaid
flowchart LR
    Schedule["Versioned fixture and arrival schedule"] --> Harness["Task-owned process supervisor"]
    Harness --> Targets["Real loopback services"]
    Harness --> Barrier["Named crash and response-loss barriers"]
    Targets --> Oracle["Committed invariant and client-observation checks"]
    Oracle --> Evidence["Retained receipts, timings and failed rounds"]
```

Each case owns isolated stores, ports, generated roles, seed and process IDs. Barriers acknowledge the exact commit/grant/send position before the harness kills or releases a tracked process. A stop before commit and a lost response after commit are separate scenarios. Fault controls exist only in explicit test mode.

Run critical crash windows at least 20 times and shared authority/contention races at least 100 rounds. Keep every failed seed. Assert quantity/capacity/order bounds during transitions as well as after final settlement. A thirty-minute bounded soak records process/generator memory, backlog and missing progress.

Local workload: Three fan-out workers; staged 100, 250 and 500 real protocol connections, one hot room at ten 256-byte events/s and a second independent room. Basic browser tests use a few real tabs.

Use open-loop schedules and retain scheduled versus actual start time. Preserve not-sent requests and generator saturation, transport timeouts/disconnects and unfinished work. Stop before resource exhaustion; no system-wide tuning or external targets are required. Canonical fixture digests come from an independent oracle during implementation; none are invented here.

Evidence contains commit, tool/store versions, OS/CPU/RAM, policy/config, fixture seed, actual clients/sockets, request keys, barriers, receipts, server/client observations and invariant verdict. The [acceptance catalog](acceptance-plan.md) includes every required scenario.
