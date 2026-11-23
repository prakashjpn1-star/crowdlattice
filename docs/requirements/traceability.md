# Requirements traceability

Every source section and numbered requirement is retained. Coverage is planned verification, not a test pass.

| Source section | Active contract | Component |
| --- | --- | --- |
| 1 Purpose and product | [Read](../requirements/product.md) | Cross-component requirement |
| 2 Why the project is substantial | [Read](../requirements/product.md) | Cross-component requirement |
| 3 Scale and evidence contract | [Read](../requirements/product.md) | Cross-component requirement |
| 4 Fit with the restricted desktop | [Read](../requirements/product.md) | Cross-component requirement |
| 5 Release scope and exclusions | [Read](../requirements/product.md) | Cross-component requirement |
| 6 Reference scenario | [Read](../requirements/product.md) | Cross-component requirement |
| 7 Definitions and guarantees | [Read](../requirements/product.md) | Cross-component requirement |
| 8 Room and publication requirements | [Read](../requirements/software.md) | [Room lifecycle ordered publish and snapshots](../design/publication.md) |
| 9 Snapshot and replay protocol | [Read](../requirements/software.md) | [Snapshot to stream handoff replay and reset](../design/replay.md) |
| 10 Fan-out and hot-room design | [Read](../requirements/software.md) | [Hot-room placement and shared upstream feeds](../design/fanout.md) |
| 11 Session ownership and admission | [Read](../requirements/software.md) | [Capacity grants and connection generations](../design/sessions.md) |
| 12 Backpressure and reconnection control | [Read](../requirements/software.md) | [Slow viewers bounded buffers and reconnect control](../design/backpressure.md) |
| 13 Worker drain failure and recovery | [Read](../requirements/software.md) | [Worker drain loss and safe reconnection](../design/recovery.md) |
| 14 Presence and observability semantics | [Read](../requirements/software.md) | [Presence and actual connection accounting](../design/presence.md) |
| 15 Logical records and transaction boundaries | [Read](../contracts/data-model.md) | Cross-component requirement |
| 16 API and streaming contract | [Read](../contracts/api.md) | Cross-component requirement |
| 17 Browser experience | [Read](../requirements/browser-journeys.md) | Cross-component requirement |
| 18 Local qualification and load methodology | [Read](../testing/performance-and-capacity.md) | Cross-component requirement |
| 19 Five-million-viewer capacity study | [Read](../testing/performance-and-capacity.md) | Cross-component requirement |
| 20 Acceptance catalog | [Read](../testing/acceptance-plan.md) | Cross-component requirement |
| 21 Milestones and deliverables | [Read](../delivery/milestones.md) | Cross-component requirement |
| 22 Open decisions and references | [Read](../architecture/references.md) | Cross-component requirement |

## Numbered requirements including UI

| ID | Behavior | Design |
| --- | --- | --- |
| ROOM-01 | Create a room with immutable ID/generation, bounded event schema and versioned configuration. | [Component](../design/publication.md) |
| ROOM-02 | Room states are DRAFT, LIVE and ENDED. Start/end use expected revision and operation key; ENDED does not reopen. | [Component](../design/publication.md) |
| ROOM-03 | Publishing accepts only registered event types: score-set, status-set and announcement. Payloads are plain validated data. | [Component](../design/publication.md) |
| ROOM-04 | Canonical event, next sequence, room snapshot/reducer state and publisher receipt commit atomically. Rollback exposes no sequence gap. | [Component](../design/publication.md) |
| ROOM-05 | Same publisher key and request returns the original event; changed content with that key conflicts. | [Component](../design/publication.md) |
| ROOM-06 | Publication is bounded to 8 KiB/event, with a 256-byte synthetic payload profile for capacity analysis. | [Component](../design/publication.md) |
| ROOM-07 | Ending a room rejects new events and emits an ordered terminal event. Retained reads and snapshot inspection remain possible. | [Component](../design/publication.md) |
| SES-01 | Synthetic viewer sessions have opaque IDs and replaceable connection generations. A new connection generation supersedes the prior one. | [Component](../design/sessions.md) |
| SES-02 | Join/reconnect is idempotent by scoped request key and cannot create unbounded duplicate session records. | [Component](../design/sessions.md) |
| SES-03 | Allocate finite worker capacity grants atomically; total active grants never exceed the configured local global cap. | [Component](../design/sessions.md) |
| SES-04 | Unused granted capacity remains reserved until release/expiry, preventing over-admission through optimistic counters. | [Component](../design/sessions.md) |
| SES-05 | A worker losing its capacity/ownership lease stops admission and streaming before using expired authority. | [Component](../design/sessions.md) |
| SES-06 | Control-store uncertainty produces bounded degraded behaviour: existing streams run only under still-valid grants; no new grants are invented. | [Component](../design/sessions.md) |
| SES-07 | Session transport values are disposable local demo credentials, never real account tokens. | [Component](../design/sessions.md) |
| LA-UI-01 | Serve HTML, CSS, scripts and optional assets from the local application origin; all required flows work without external connectivity. | [Browser](../design/browser.md) |
| LA-UI-02 | JavaScript is the dependency-minimal path. If TypeScript is selected, use a compiler available through the permitted mirror, document the local compile command and serve the generated JavaScript. Browsers must not require a TypeScript compiler or internet downloads at runtime. | [Browser](../design/browser.md) |
| LA-UI-03 | Keep the frontend small: an HTML entry page, CSS file and JavaScript modules, with corresponding TypeScript source only if chosen. Any selected library must be bundled locally and pass the dependency gate; no framework is required. | [Browser](../design/browser.md) |
| LA-UI-04 | Wire controls to real local APIs and the real streaming connection. Show loading, accepted, reconnecting, stale, empty and error states from actual server outcomes. | [Browser](../design/browser.md) |
| LA-UI-05 | Bound the rendered event list, reuse the active stream and release browser resources when switching rooms. A UI refresh must not create uncontrolled duplicate connections. | [Browser](../design/browser.md) |
| LA-UI-06 | Labels, keyboard operation, text status and inline validation cover every action. Operator actions remain authorized and validated by the backend. | [Browser](../design/browser.md) |

## Acceptance cases

| ID | Oracle | Outcome |
| --- | --- | --- |
| LA-AC-01 | Publish lifecycle rejects invalid transitions/schema and preserves immutable room generation. | NOT RUN |
| LA-AC-02 | Same-key publish and lost-response replay create one ordered event; changed content conflicts. | NOT RUN |
| LA-AC-03 | Concurrent publishers produce unique contiguous committed sequences and correct snapshot digest. | NOT RUN |
| LA-AC-04 | Snapshot/subscription race loses no event between S and live tail. | NOT RUN |
| LA-AC-05 | Reconnect within retained range replays in order; duplicate frames never regress client state. | NOT RUN |
| LA-AC-06 | Cursor below low-water produces an explicit reset with consistent snapshot, not silent loss. | NOT RUN |
| LA-AC-07 | One hot room spreads over 3 workers and uses bounded upstream feeds independent of viewer count. | NOT RUN |
| LA-AC-08 | Concurrent joins respect aggregate grant capacity and return explicit overload at the cap. | NOT RUN |
| LA-AC-09 | Superseded session generations are excluded from unique presence and cannot reclaim ownership. | NOT RUN |
| LA-AC-10 | Slow client reaches its buffer bound and resets/disconnects without harming healthy clients. | NOT RUN |
| LA-AC-11 | Worker hard kill causes grant expiry and resumable reconnect; no sequence reset or fabricated receipt. | NOT RUN |
| LA-AC-12 | Drain stops new joins and staggers reconnects within recorded bounds. | NOT RUN |
| LA-AC-13 | Store outage stops new publish/admission; expired worker grants do not continue unauthorized streams. | NOT RUN |
| LA-AC-14 | Reconnect storm is bounded by admission/backoff rather than unbounded pending sockets. | NOT RUN |
| LA-AC-15 | Room end is ordered, rejects later publication and remains inspectable. | NOT RUN |
| LA-AC-16 | Presence has age/generation semantics and is not used as exact admission authority. | NOT RUN |
| LA-AC-17 | 500-real-connection target records payload, actual sockets, lag, byte rate and generator limitations. | NOT RUN |
| LA-AC-18 | Two-room test proves a slow/hot room does not permanently starve the other room. | NOT RUN |
| LA-AC-19 | Viewer/operator browser journeys, reload, keyboard access and stale tabs behave correctly. | NOT RUN |
| LA-AC-20 | Origin/role/size/input checks and disabled-by-default fault controls pass. | NOT RUN |
| LA-AC-21 | Consistent restore preserves generation, sequence, snapshot and publisher receipts. | NOT RUN |
| LA-AC-22 | Thirty-minute bounded soak has stable resources and no unexplained event gap in healthy clients. | NOT RUN |
| LA-AC-23 | Entire core journey runs offline with only loopback services. | NOT RUN |
| LA-AC-24 | Capacity report distinguishes measurement, simulation and 5-million arithmetic; no unsupported production claim. | NOT RUN |
| LA-AC-25 | Basic HTML/CSS/JS or compiled-TS pages load entirely locally and complete join, publish, reconnect, drain and diagnostics flows with keyboard access and actual backend state; any selected TypeScript build works through the permitted local toolchain. | NOT RUN |
