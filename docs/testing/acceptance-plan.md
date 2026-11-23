# Acceptance and failure scenarios

These are planned runtime oracles. Every case begins NOT RUN.

## Acceptance catalog

| ID | Required scenario and pass oracle |
| --- | --- |
| LA-AC-01 | Publish lifecycle rejects invalid transitions/schema and preserves immutable room generation. |
| LA-AC-02 | Same-key publish and lost-response replay create one ordered event; changed content conflicts. |
| LA-AC-03 | Concurrent publishers produce unique contiguous committed sequences and correct snapshot digest. |
| LA-AC-04 | Snapshot/subscription race loses no event between S and live tail. |
| LA-AC-05 | Reconnect within retained range replays in order; duplicate frames never regress client state. |
| LA-AC-06 | Cursor below low-water produces an explicit reset with consistent snapshot, not silent loss. |
| LA-AC-07 | One hot room spreads over 3 workers and uses bounded upstream feeds independent of viewer count. |
| LA-AC-08 | Concurrent joins respect aggregate grant capacity and return explicit overload at the cap. |
| LA-AC-09 | Superseded session generations are excluded from unique presence and cannot reclaim ownership. |
| LA-AC-10 | Slow client reaches its buffer bound and resets/disconnects without harming healthy clients. |
| LA-AC-11 | Worker hard kill causes grant expiry and resumable reconnect; no sequence reset or fabricated receipt. |
| LA-AC-12 | Drain stops new joins and staggers reconnects within recorded bounds. |
| LA-AC-13 | Store outage stops new publish/admission; expired worker grants do not continue unauthorized streams. |
| LA-AC-14 | Reconnect storm is bounded by admission/backoff rather than unbounded pending sockets. |
| LA-AC-15 | Room end is ordered, rejects later publication and remains inspectable. |
| LA-AC-16 | Presence has age/generation semantics and is not used as exact admission authority. |
| LA-AC-17 | 500-real-connection target records payload, actual sockets, lag, byte rate and generator limitations. |
| LA-AC-18 | Two-room test proves a slow/hot room does not permanently starve the other room. |
| LA-AC-19 | Viewer/operator browser journeys, reload, keyboard access and stale tabs behave correctly. |
| LA-AC-20 | Origin/role/size/input checks and disabled-by-default fault controls pass. |
| LA-AC-21 | Consistent restore preserves generation, sequence, snapshot and publisher receipts. |
| LA-AC-22 | Thirty-minute bounded soak has stable resources and no unexplained event gap in healthy clients. |
| LA-AC-23 | Entire core journey runs offline with only loopback services. |
| LA-AC-24 | Capacity report distinguishes measurement, simulation and 5-million arithmetic; no unsupported production claim. |
| LA-AC-25 | Basic HTML/CSS/JS or compiled-TS pages load entirely locally and complete join, publish, reconnect, drain and diagnostics flows with keyboard access and actual backend state; any selected TypeScript build works through the permitted local toolchain. |

All cases start NOT RUN. Keep commit/environment, fixtures, expected hashes, receipts, socket counts, barrier logs and failed rounds. Repeat critical crash windows 20 times and ownership/admission races 100 rounds. Required invariants include immutable publication, no cursor regression, bounded buffers, valid grants and explicit gap/reset handling.
