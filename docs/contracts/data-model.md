# Data model and integrity

Current state, immutable accepted outcomes and audit records agree at each committed transition.

## Logical records and transaction boundaries

Required entities: Room; immutable RoomEvent; consistent RoomSnapshot; PublishReceipt; WorkerInstance; CapacityGrant; versioned RoutingMap; ViewerSessionOwnership; bounded ReplayCursor metadata; OperatorAudit; SchemaMigration. Enforce nonnull identities, unique room/generation/sequence, unique scoped keys, positive grants and immutable terminal state.

Publish/snapshot/receipt and admission/grant mutations each have documented atomic boundaries. Network sends are not part of the database commit. Event delivery uses the committed log; cached snapshots can be discarded and rebuilt. Route configuration uses expected revision and complete activation, never a partially installed worker map.

Record session/grant history at joins/ownership changes rather than every frame. Initial retained-data cap and cleanup must preserve publisher receipts needed for uncertain requests. Consistent backup includes room generation, events, snapshot baseline and receipts together.
