# System Design Fundamentals

## The scaling playbook (in order)
1. **Scale up** - bigger machine. Simplest; has a ceiling.
2. **Caching** - read-heavy? Put hot data in memory (Redis/Memcached). Biggest, cheapest win.
3. **Load balancing** - spread requests across N identical servers.
4. **Read replicas / sharding** - split data & traffic horizontally.
5. **Async / queues** - decouple work; absorb spikes (Kafka, SQS).
6. **CDN** - edge-cache static/immutable assets globally.

## Key concepts
- **Load balancer:** L4 (TCP, fast, less aware) vs L7 (HTTP, can route by path/header). Health checks + sticky sessions when needed.
- **Caching:** cache-aside pattern; handle invalidation (TTL or event-driven). Watch stampede -> use locking / stale-while-revalidate.
- **Database sharding:** partition by key (user_id, region). Trade-off: cross-shard joins are hard; pick a good shard key.
- **Replication:** synchronous (strong consistency, slower) vs asynchronous (better availability, can lose data on failover).
- **Message queue:** decouples producers/consumers, buffers spikes, enables retry. At-least-once delivery -> handle idempotency.

## CAP / PACELC (simplified)
- You can't have all of **C**onsistency, **A**vailability, **P**artition tolerance simultaneously.
- Partition tolerance is a given in distributed systems -> you choose CP vs AP per operation.
- PACELC: "if there's no partition, you trade latency for consistency; if there is, you trade availability for consistency."

## Classic questions to be ready for
- Design URL shortener (base62 encode of an ID; handle collisions).
- Design a news feed (fan-out on write vs read; hybrid for celebrities).
- Design rate limiter (token bucket / sliding window).
- Design key-value store (consistent hashing for sharding).

## Consistent hashing
- Hash nodes & keys onto a ring; each key maps to the next node clockwise.
- Adding/removing a node moves only ~1/n of keys. Use **virtual nodes** for balance.

## Self-test
1. What breaks if you scale up forever? When do you shard?
2. Sync vs async replication - give a real trade-off each.
3. Why does consistent hashing beat modulo-N sharding?
