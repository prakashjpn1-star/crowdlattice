# Local access boundaries

The following release contracts apply to the local product and its operating environment.

## Fit with the restricted desktop

Run an API/control process, a durable local store and at least 3 fan-out worker processes over loopback. Serve the browser UI and all assets locally. The load generator uses actual local network sockets and generated data. No internet, Docker, GPU, sound, native desktop toolkit, cloud account or Windows component is needed.

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

## API and streaming contract

| Endpoint | Behaviour |
| --- | --- |
| GET /api/health, /api/ready | Separate liveness and durable-store/schema readiness. |
| POST /api/rooms | Create bounded room definition. |
| POST /api/rooms/{id}/start, /end | Revision-checked lifecycle transitions. |
| POST /api/rooms/{id}/events | Keyed validated publisher event. |
| GET /api/rooms/{id}/snapshot | State plus generation, sequence and digest in one snapshot. |
| POST /api/rooms/{id}/join | Allocate/reconcile session ownership and return local worker target. |
| GET /stream/rooms/{id} | Streaming frames with sequence and resume cursor. |
| GET /api/rooms/{id}/events | Bounded replay inspection. |
| POST /api/workers/{id}/drain | Operator-controlled bounded worker drain. |
| GET /api/audience, /api/workers, /api/metrics | Actual/estimated counts, ages and pressure. |

Frame example: `{"roomGeneration":"demo-1","sequence":42,"type":"score-set","payload":{"home":2,"away":1}}`. SSE is a suitable transport; an equivalent documented browser protocol is allowed. Do not expose reusable tokens in URLs or logs. Define heartbeat/reset/end/error frames separately from application events.

Malformed requests return 400, invalid content 422, stale generation/revision/key 409, oversized bodies 413, admission pressure 429 or 503 with documented meaning, and unavailable storage 503. Limit read pages to 200 and ordinary JSON responses to 1 MiB. Streaming limits are per frame/buffer rather than an unbounded whole-response allocation.
