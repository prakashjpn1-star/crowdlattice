# Design review and open questions

The first release is a real local product with a separate large-scale capacity study. Its success depends on shared-state and recovery evidence rather than a headline traffic count.

| Risk | Design response |
| --- | --- |
| Snapshot and subscription can lose an event unless catch-up begins after S. | Addressed in the component algorithms and acceptance plan; runtime proof remains pending. |
| A single hot room must spread across all three workers. | Addressed in the component algorithms and acceptance plan; runtime proof remains pending. |
| Reliable per-client buffering must remain finite under slow readers. | Addressed in the component algorithms and acceptance plan; runtime proof remains pending. |
| Actual sockets, reserved capacity and approximate unique presence are different measures. | Addressed in the component algorithms and acceptance plan; runtime proof remains pending. |

The reference architecture resolves the language direction to Python, local transactional SQLite and a basic HTML/CSS/JavaScript frontend. Exact framework/driver/canonicalizer versions require permitted-environment verification. New algorithm defaults are explicitly proposed in their design pages.

No multi-million deployment, production bot resistance, arbitrary-origin rollback or cross-region consistency is established by the local design. Original inputs are archived for traceability. AI assistance and programme eligibility remain separate from participant-authored submission answers.
