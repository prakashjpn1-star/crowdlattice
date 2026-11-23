# State machines and race outcomes

```mermaid
stateDiagram-v2
    [*] --> DRAFT
    DRAFT --> LIVE: start accepted
    LIVE --> ENDED: atomic terminal event
    ENDED --> [*]
```

```mermaid
stateDiagram-v2
    [*] --> STARTING
    STARTING --> READY: grants and feed ready
    READY --> DRAINING: stop new joins
    READY --> UNAVAILABLE: lost lease or process
    DRAINING --> STOPPED: bounded delivery then close
    UNAVAILABLE --> STOPPED
    STOPPED --> [*]
```

Additional states and transition guards are in the [software requirements](../requirements/software.md). Terminal records do not reopen; receipt replay proves a former commit without making a new transition.

| Race | Outcome |
| --- | --- |
| Completion versus deadline | Only a serialized transition with valid current authority can newly commit. |
| New control versus new work | The first valid committed transition determines admission under the pinned/current-state rules. |
| Two workers settle one due record | One conditional transition wins; the other records no duplicate terminal event. |
| Commit versus lost response | Replay original identity/key; transport timeout does not imply rollback. |
| Lowered limits versus occupancy | Existing valid occupancy drains; no new grant exceeds the effective admission rule. |
