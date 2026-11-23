# Software requirements

A successful publish commits one ordered room event and matching snapshot. Delivery may repeat; viewers deduplicate by room generation and sequence. This product distributes application updates, not video or audio.

## Room and publication requirements

| ID | Requirement |
| --- | --- |
| ROOM-01 | Create a room with immutable ID/generation, bounded event schema and versioned configuration. |
| ROOM-02 | Room states are DRAFT, LIVE and ENDED. Start/end use expected revision and operation key; ENDED does not reopen. |
| ROOM-03 | Publishing accepts only registered event types: score-set, status-set and announcement. Payloads are plain validated data. |
| ROOM-04 | Canonical event, next sequence, room snapshot/reducer state and publisher receipt commit atomically. Rollback exposes no sequence gap. |
| ROOM-05 | Same publisher key and request returns the original event; changed content with that key conflicts. |
| ROOM-06 | Publication is bounded to 8 KiB/event, with a 256-byte synthetic payload profile for capacity analysis. |
| ROOM-07 | Ending a room rejects new events and emits an ordered terminal event. Retained reads and snapshot inspection remain possible. |

Use a documented canonical serialization and content digest; reject duplicate JSON keys, invalid numbers, unknown fields and unsupported versions. Snapshot reduction must be deterministic and validated against an independent reference reducer. Browser labels and announcements are escaped text, never arbitrary HTML.

## Snapshot and replay protocol

A new viewer receives a snapshot through sequence S and then events after S. Snapshot state and S come from one consistent durable view. The subsequent subscription must catch up from S before following live updates so an event between snapshot read and connection establishment is not lost.

Reconnect supplies room generation, session identity/generation and last applied sequence. Within the replay window, return events in order before live tailing. A cursor from another room/generation, a cursor beyond high-water, or malformed cursor is rejected.

Keep a bounded replay window, initially 10,000 events or 10 minutes, with an explicit low-water. If history is no longer retained, send RESET_REQUIRED and a new snapshot; never silently skip to the newest event. Snapshots and retained events must overlap consistently at the retention boundary. A reset is visible in the viewer UI and metrics.

## Fan-out and hot-room design

Each worker maintains at most one upstream room feed for its subscribed viewers, sharing immutable encoded event buffers where practical. It must not fetch or deserialize the same room event independently for every viewer. The number of authoritative room-feed reads scales primarily with subscribed workers/rooms, not viewer count.

The same hot room must be distributable across all 3 local workers. Placement by room alone cannot assign a 5-million-viewer room to one process. Use stable virtual buckets based on room/session IDs plus a versioned worker map; the exact routing algorithm is an implementation decision with tests.

Worker capacity, outbound bytes and per-room subscriptions are observable. Routing should avoid full workers and retain bounded spare capacity. Worker changes must preserve event identity and cursors. No worker-local memory is the sole durable publication history.

## Session ownership and admission

| ID | Requirement |
| --- | --- |
| SES-01 | Synthetic viewer sessions have opaque IDs and replaceable connection generations. A new connection generation supersedes the prior one. |
| SES-02 | Join/reconnect is idempotent by scoped request key and cannot create unbounded duplicate session records. |
| SES-03 | Allocate finite worker capacity grants atomically; total active grants never exceed the configured local global cap. |
| SES-04 | Unused granted capacity remains reserved until release/expiry, preventing over-admission through optimistic counters. |
| SES-05 | A worker losing its capacity/ownership lease stops admission and streaming before using expired authority. |
| SES-06 | Control-store uncertainty produces bounded degraded behaviour: existing streams run only under still-valid grants; no new grants are invented. |
| SES-07 | Session transport values are disposable local demo credentials, never real account tokens. |

Proposed worker-grant TTL is 15 seconds, renewal every 5 seconds. Document durable-time versus process-monotonic-clock conversion and a conservative expiry margin. A process resumed after a long scheduling pause checks expiry before sending again. Session migration excludes old generations from unique presence, although duplicate event delivery during reconnection remains possible and harmless after cursor deduplication.

Admission over the configured cap returns a retryable overload response with jitter guidance. It does not accept a socket and wait indefinitely in an unbounded queue. No production bot resistance or proof that one synthetic ID equals one real human is claimed.

## Backpressure and reconnection control

Per-client buffered outbound data is bounded, proposed 64 KiB and/or 256 frames. When a reliable event would overflow the limit, close that client's stream with a recoverable slow-consumer reason and retain its last known cursor semantics. Do not silently drop scoreboard events. Presence summaries may be coalesced because they are explicitly nonauthoritative observations.

Bound replay work, concurrent snapshot generation and joins per worker. Cache/share identical snapshots by room generation/sequence; collapse concurrent snapshot rebuilds. Reconnect uses bounded exponential backoff and jitter. A server-provided delay is a minimum, not a synchronized wake-up instruction for all clients.

Heartbeats distinguish a healthy idle room from a broken transport; they do not advance the application sequence. Disconnecting a viewer frees local resources once and updates observed counts. Work that belongs to a stalled client cannot delay unrelated clients or the operator health endpoint.

## Worker drain failure and recovery

Worker lifecycle: STARTING, READY, DRAINING, UNAVAILABLE, STOPPED. Draining stops new joins, signals reconnect hints with staggered delays and allows bounded active delivery before shutdown. Failed-worker grants expire; replacement workers use new process identities and subscription positions.

Routing-map revisions and worker generations are checked on join and migration. A stale worker cannot reclaim ownership by advertising its old ID. Several local reconcilers may observe the same expiry; conditional transitions and unique grants prevent double reassignment.

Publisher restart reuses durable keys or creates explicit new events; it must not repeat the same fixture sequence as new content accidentally. Store outage stops publishing and new admission, preserves current cursors, and returns clear readiness failure. Restart must not erase event history, reset room generation or fabricate live audience counts.

## Presence and observability semantics

Workers report aggregated active-socket and current-session estimates periodically, initially every 5 seconds. Presence is best effort with an age indicator, not a database write for every viewer on every event. Exclude expired worker generations from the estimate. Do not use approximate presence to decide strict admission caps.

Metrics include active connections, allocated capacity, admission rejects, events published/delivered, per-room fan-out factor, egress bytes, send lag, slow consumers, replay/reset counts, reconnect arrival rate and worker health. Label actual socket counts separately from simulated logical population. Histograms include disconnects/timeouts and generator failures, not just fast successful viewers.
