# Relational / SQL Databases

## ACID (transactional guarantees)
- **A**tomicity - all or nothing.
- **C**onsistency - valid state to valid state.
- **I**solation - transactions don't interfere.
- **D**urability - committed data survives a crash.

## Indexing
- **B-tree / B+ tree:** balanced; range scans + point lookups in O(log n). Default for RDBMS.
- **Hash index:** O(1) exact match only - no ranges.
- **Composite index:** order matters; leftmost prefix rule (e.g. `(a,b)` helps `WHERE a=?` and `a=? AND b=?`, not `b=?` alone).
- **Covering index:** query answered from the index alone -> no table lookup.

## Transactions & isolation levels
`READ UNCOMMITTED < READ COMMITTED < REPEATABLE READ < SERIALIZABLE`
- Higher = more consistent, more contention/locking. Most OLTP defaults: READ COMMITTED or REPEATABLE READ.
- **MVCC** lets readers not block writers (and vice versa) in many engines.

## Designing a schema (interview angle)
1. Identify entities & relationships -> tables + foreign keys.
2. Choose shard key if distributed (usually the primary lookup).
3. Add indexes for your actual query patterns - not speculative ones.
4. Denormalize only for hot read paths, and keep it in sync deliberately.

## Self-test
1. Why does index order matter in a composite index?
2. What can go wrong with async replication during failover?
3. When would you denormalize, and how do you keep it consistent?
