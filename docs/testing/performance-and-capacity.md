# Measurements and capacity assumptions

Measured local throughput, simulated populations and analytical deployment targets are reported separately. Every stated target is provisional until measured.

## Local qualification and load methodology

Local target: 500 real protocol connections across 3 workers, one hot room at 10 events/second with 256-byte payload, plus a separate small room. Provisional healthy delivery p95 <=500 ms and p99 <=2 seconds; UI observation within 3 seconds; recovery after worker loss <=20 seconds plus documented client backoff. Measure end-to-end application timestamps only with known same-host clocks.

Start at 100 connections, then 250 and 500. Record attempted/established/active/disconnected sockets, generator CPU/RAM, server CPU/RAM, open file descriptors, byte rate and per-client event counts. Preserve the offered load schedule and actual start lag. Open-loop join storms and steady streaming are separate tests. Browser tests use a small number of real tabs; bulk protocol clients are not mislabeled as rendered browsers.

Bound server/store memory to a provisional 2 GiB, generator separately to 1 GiB, with a stop threshold below workspace exhaustion. No system-wide tuning, privileged changes, production targets or outside traffic is required. If the generator saturates first, the run is generator-limited, not proof of a server ceiling.

## Five-million-viewer capacity study

For an illustrative 256-byte update once per second per viewer, 5,000,000 × 256 = 1,280,000,000 application bytes/second, or 10.24 Gbit/second, before framing, TLS, transport overhead and retransmission. This is arithmetic under an assumption, not a network benchmark. Ten updates/second multiplies that base traffic by ten.

At an illustrative 16 KiB of retained state per connection, 5 million connections consume 81.92 GB (decimal) for that state alone, excluding runtime, buffers, operating system and spare capacity. Reconnecting 20 percent of the audience over 30 seconds means about 33,333 join attempts/second. These dimensions must be modeled separately.

The required capacity report uses measured sustainable sockets, egress and CPU per local worker, applies explicit safety headroom and a worker-failure reserve, and calculates the maximum of memory-, CPU-, socket- and bandwidth-driven node counts. Do not sum incompatible limits or linearly extrapolate a saturated single-node result. Document shared-store, publisher hot-room, routing, inter-worker fan-out, TLS and regional failure bottlenecks left untested.

A proposed production topology may use regional routing and edge fan-out, but no real CDN, cloud deployment or paid infrastructure is necessary to complete the local project. Multi-region fault tolerance and true 5-million capacity remain unverified.
