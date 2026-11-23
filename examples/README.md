# Worked examples

These fictional examples make the contracts concrete. Replace illustrative IDs with returned runtime identities; use a stable Idempotency-Key for the same mutation intent. No file contains a live authority token or invented fixture digest.

| Example | Schema | Meaning |
| --- | --- | --- |
| [room.json](room.json) | [room](../schemas/room.schema.json) | Start separately with expected revision. A generation is assigned once and never resets on worker restart. |
| [score-publish.json](score-publish.json) | [publish-event](../schemas/publish-event.schema.json) | Publisher supplies stable Idempotency-Key; the receipt proves durable acceptance, not audience delivery. |
| [announcement.json](announcement.json) | [publish-event](../schemas/publish-event.schema.json) | Plain escaped text; never arbitrary HTML. |
| [join.json](join.json) | [join](../schemas/join.schema.json) | Authority uses a protected header/cookie. Supply actual returned identities in the runtime. |
| [reconnect.json](reconnect.json) | [join](../schemas/join.schema.json) | The cursor means applied sequence 41, not merely the last bytes received. |
| [score-frame.json](score-frame.json) | [application-frame](../schemas/application-frame.schema.json) | After applying, retain cursor 42. A duplicate 42 is harmless; a gap is not silently skipped. |
| [reset-required.json](reset-required.json) | [control-frame](../schemas/control-frame.schema.json) | Fetch the consistent snapshot at the indicated/returned current sequence, then catch up from it. |
| [heartbeat.json](heartbeat.json) | [control-frame](../schemas/control-frame.schema.json) | Illustrative timestamp only. A heartbeat does not advance application sequence. |
| [capacity-assumptions.json](capacity-assumptions.json) | [capacity-input](../schemas/capacity-input.schema.json) | Analytical assumptions only; measured per-node fields are deliberately absent until an actual run exists. |

Run the analytical calculator with `node examples/capacity-model.mjs examples/capacity-assumptions.json`. It reports assumption-based arithmetic, not measured or validated deployment capacity. Node is a documentation-companion tool, not a required product backend.

Large fixture generators, independent expected digests and actual load results remain runtime implementation deliverables. The [SSE frame](score-update.sse) illustrates the wire format; its id denotes received framing while the application explicitly tracks the last applied cursor.
