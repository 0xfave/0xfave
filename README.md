# 👋 Gm! I'm Fave

I build backend systems for AI inference and high-throughput data infrastructure.

Rust-first. I care about systems that stay fast and predictable under load.

---

## Currently building: inference-engine-ops

A Rust-native LLM inference server, built from the ground up.

**Problem:** most inference servers hide the tradeoffs that actually
determine cost and latency — batching strategy, cache eviction, prefill
vs decode. I wanted to understand and control those directly.

**Shipped so far:**
- Axum HTTP server with an OpenAI-compatible API
- Continuous batching scheduler
- KV cache with paging
- Semantic caching layer
- Prometheus observability

**Results (benchmarked, M0 → M3):**
- KV cache: 1.17 → 9.30 tok/s, a 7.9x speedup. Stopped reprocessing
  full history on every generation step.
- Batching (B=4): 10.80 tok/s total throughput across 4 concurrent
  sequences, sharing one forward pass across requests.
- Epoch queue: 5.66–10.39 tok/s sustained across up to max_batch
  concurrent users, with automatic epoch scheduling.
- Time to first token stayed under 0.5s through every stage, even as
  throughput scaled up.

[→ Repository](https://github.com/0xfave/inference-engine-ops).

---

## Selected work

### onchain-intelligence-engine
Real-time Rust/ClickHouse pipeline tracking Pump.fun, PumpSwap, Moonshot,
and Launchlabs DEX activity at sub-second latency.

**Problem:** on-chain data is high-volume and noisy. Querying it fast
enough for trading and analytics use cases needed a purpose-built pipeline,
not a generic indexer.

**Approach:** async ingestion via Geyser/gRPC, normalized storage in
ClickHouse, dockerized for deployment.

[→ Repository](https://github.com/0xfave/onchain-intelligence-engine) · [→ Write-up on Geyser/gRPC](https://0xfave.hashnode.dev/how-geyser-streams-data)

### job-queue
Rust job queue purpose-built for LLM inference workloads. PostgreSQL-backed,
LISTEN/NOTIFY for dispatch, no Redis, no managed queue service.

**Problem:** inference workloads need reliable job dispatch with retry
and dead-letter handling, and I wanted this without adding infra dependencies.

**Architecture:** three concurrent tokio tasks (HTTP API, scheduler,
heartbeat monitor). Workers bound by `tokio::sync::Semaphore` for
concurrency control. Full state machine: Pending → Running → Completed,
with Failed jobs retrying until exhausted, then routed to a DLQ.

**Decision:** Postgres LISTEN/NOTIFY over Redis or a managed queue.
I built this to learn queue design from first principles — state
machines, retry logic, worker dispatch — rather than patch services
together with an off-the-shelf tool. Understanding the mechanics
directly, not just wiring managed infra.

[→ Repository](https://github.com/0xfave/job-queue)

### JupLend data migration
Rust-based migration tool moving operations data from PostgreSQL to
ClickHouse, with parallel batch processing and automatic row-count
verification.

**Problem:** Postgres wasn't well-suited to the client's growing record
volume and the query patterns they needed for fast retrieval at scale.

**Decision:** recommended ClickHouse — MergeTree engine, monthly
partitioning, ordering optimized for their actual query patterns
(action type, time, user).

**Approach:** built a range-based parallel migration tool rather than
a one-off script, with dry-run mode and automatic verification to
guarantee no data loss during the move.

**Result:** significantly faster query performance at scale.

[→ Repository](https://github.com/0xfave/jup-migrate/)

### Token-2022 stablecoin (Superteam SSS bounty)
Built a stablecoin with transfer hooks, blacklist, and permanent delegate
on Solana's Token-2022 standard.

[→ Repository](link)

---

## Background

Security research: 20+ validated vulnerabilities found across Code4rena,
Sherlock, Cantina, and Immunefi audits. This gives me an edge in backend
work — I think about failure modes and abuse cases by default, not as
an afterthought.

---

## Writing

I write about what I build. [Engineering notes →](https://0xfave.hashnode.dev)

---

## Reach me

[X/Twitter](https://twitter.com/0xfav3) · [Email](mailto:fave8194@gmail.com)
