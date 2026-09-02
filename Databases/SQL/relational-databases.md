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

## Specific relational databases

### PostgreSQL
- **Capabilities:** Full ACID + strong MVCC; rich types (JSONB, arrays, ranges, hstore); advanced indexes (GIST/GIN for spatial, full-text, JSON); window functions, CTEs, generated columns; first-class extensions (e.g., PostGIS); logical & physical replication.
- **Limitations:** MVCC leaves dead tuples on update/delete (autovacuum needed under heavy churn); handles many concurrent writers fine; horizontal scale is via add-ons (Citus, etc.), not built-in; heavier to operate than SQLite.
- **Pick when:** The default "batteries-included" relational choice; complex queries; mixed OLTP + light analytics; need flexible types and extensions.

### MySQL / MariaDB
- **Capabilities:** Extremely widely deployed & hosted; InnoDB engine gives ACID + MVCC; strong master/replica read scaling; huge tooling, community, and hiring pool; great for LAMP-style stacks.
- **Limitations:** Fewer advanced index/type features than Postgres (JSON support arrived later); optimizer can be less predictable on very complex queries; MariaDB has diverged with its own extras to know about.
- **Pick when:** You want the most battle-tested, universally-available RDBMS with strong read scaling and a big ecosystem.

### SQLite
- **Capabilities:** Embedded (no server process), single portable file, zero-config, ACID, very fast in-process reads; ideal for local data.
- **Limitations:** Database-level write lock - one writer at a time; no network access by default; not built for high-concurrency multi-user servers.
- **Pick when:** Mobile/desktop apps, edge/offline devices, local caching, tests, small tools - NOT a primary server DB under heavy concurrent writes.

### Microsoft SQL Server
- **Capabilities:** Strong enterprise features (row-level security, in-memory OLTP), excellent tooling (SSMS, T-SQL), tight Azure/.NET integration, solid analytics stack.
- **Limitations:** Licensing cost; historically Windows-centric (now cross-platform); T-SQL is proprietary.
- **Pick when:** Microsoft/Azure-centric enterprise shops needing deep .NET/Azure integration and enterprise-grade features.

### Oracle Database
- **Capabilities:** Enterprise scale (RAC), mature OLTP + data warehousing, rich partitioning; strong mission-critical track record.
- **Limitations:** Expensive licensing; complex to operate; overkill for most startups.
- **Pick when:** Large enterprises with existing Oracle investment and high-scale critical OLTP.

### Distributed / "NewSQL" (brief)
- **CockroachDB, Cloud Spanner, YugabyteDB, TiDB:** SQL interface + horizontal scale + strong consistency.
- **Limitation:** More operational complexity; some feature gaps vs a single-node Postgres.
- **Pick when:** You need SQL semantics at multi-node scale without giving up ACID.

## How to pick a relational DB (criteria)
| Criterion | Lean toward |
|-----------|-------------|
| Most features / flexibility, default choice | PostgreSQL |
| Max ecosystem/hosting availability, read scaling | MySQL / MariaDB |
| Embedded/local/no-ops small scale | SQLite |
| Microsoft/Azure enterprise stack | SQL Server |
| Legacy/enterprise large-scale OLTP | Oracle |
| SQL + horizontal scale + strong consistency | CockroachDB / Spanner / TiDB |

**Deciding questions:**
1. How much concurrent write throughput? (high -> avoid single-writer limits; consider distributed)
2. Do you need complex queries/rich types? (yes -> Postgres)
3. Must it scale horizontally without rewriting? (yes -> NewSQL or add-on sharding)
4. Ops budget & team skills? (small -> managed Postgres/MySQL on a cloud provider)


## Self-test
1. Why does index order matter in a composite index?
2. What can go wrong with async replication during failover?
3. When would you denormalize, and how do you keep it consistent?
4. Postgres vs MySQL - name one concrete reason to pick each.
5. Why is SQLite wrong for a high-concurrency multi-user server?
6. When does "NewSQL" (e.g., CockroachDB) beat plain Postgres + sharding middleware?
