# API and protocol contracts

The API uses bounded requests, explicit outcomes and operation-scoped request identity. Protected authority stays out of query strings.

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

## Contract companions

[JSON Schemas](../../schemas/README.md), [worked examples](../../examples/README.md) and [reference SQL](../../schemas/reference-schema.sql) make the proposed shapes explicit. Header/session identity and state-dependent checks remain the server’s responsibility.
