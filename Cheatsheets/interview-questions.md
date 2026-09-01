# Rapid-Fire Interview Cheat Sheet

Skim this the morning of an interview. One-liners + what to expand on.

## Algorithms
- **Big-O order:** O(1) < O(log n) < O(n) < O(n log n) < O(n^2) < O(2^n).
- **Hash table:** O(1) avg lookup; not ordered; watch collisions.
- **BFS** = unweighted shortest path (queue). **DFS** = cycles/topo/connected components (stack).
- **Dijkstra** = non-negative weighted shortest path.
- **Sliding window** for subarray problems; **two pointers** on sorted arrays.
- Always state time & space complexity and the trade-off.

## System Design
- Scale order: scale up -> cache -> load balance -> shard/replicate -> async queues -> CDN.
- **CAP:** can't have C, A, P all at once; pick CP vs AP per op.
- **Consistent hashing** to shard without reshuffling everything.
- Cache invalidation is the hard part - TTL or event-driven.

## Databases
- **ACID** = atomicity, consistency, isolation, durability.
- **B-tree index** for ranges + point lookups; hash index for exact only.
- Composite index: leftmost prefix rule; order matters.
- Isolation levels: READ COMMITTED < REPEATABLE READ < SERIALIZABLE.
- NoSQL: key-value (fastest), document, column-family, graph.

## OS / Networking
- **Process** = own memory (heavy); **thread** = shared memory (light).
- **TCP** reliable/ordered (3-way handshake); **UDP** fast/unreliable.
- **HTTPS** = HTTP + TLS. DNS: domain -> IP, cached by TTL.
- Caches fast->slow: CPU L1/L2/L3 -> RAM -> SSD/HDD.

## Questions interviewers love (be ready to expand)
1. How would you design X end-to-end? (pick a topic above)
2. What's the trade-off of your choice?
3. What happens at 10x / 100x load?
4. Where does it break first, and how do you monitor for it?

> Tip: in any answer, name the **trade-off** explicitly - that's usually what they're probing.
