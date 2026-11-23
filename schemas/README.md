# Schema catalog

These draft-2020-12 JSON Schemas define proposed version 1 shapes. They complement the [API contracts](../docs/contracts/api.md); they are not a running service.

| Schema | Contract |
| --- | --- |
| [room.schema.json](room.schema.json) | Draft room configuration |
| [publish-event.schema.json](publish-event.schema.json) | Registered publisher application event |
| [application-frame.schema.json](application-frame.schema.json) | Ordered committed application frame |
| [join.schema.json](join.schema.json) | Viewer join or reconnect request |
| [room-control.schema.json](room-control.schema.json) | Room start or end request |
| [snapshot.schema.json](snapshot.schema.json) | Consistent room snapshot |
| [worker-map.schema.json](worker-map.schema.json) | Complete versioned routing map |
| [control-frame.schema.json](control-frame.schema.json) | Heartbeat reset end or error frame |
| [capacity-input.schema.json](capacity-input.schema.json) | Explicit analytical capacity assumptions |

[reference-schema.sql](reference-schema.sql) defines principal relational keys, bounds and indexes. Real migrations must add checksums, immutable-field triggers and complete domain constraints. SQL setup alone does not prove concurrency/recovery.

JSON Schema does not verify database time, current owner/revision, cross-field arithmetic, loopback process ownership, encoded byte length or complete map/route semantics. Strict parsing rejects duplicate keys/nonfinite values and the domain service checks current state inside the transaction. Keys/session authority stay in headers/cookies; examples contain no live tokens.

Configured counts and schema maxima are proposed design limits pending permitted-environment checks and measurement. Ordinary list/page and endpoint byte limits follow the source contracts. Examples illustrate shape, not observed acceptance results.
