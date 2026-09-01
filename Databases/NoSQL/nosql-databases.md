# NoSQL Databases

## The trade-off
- SQL gives ACID; NoSQL often trades some of that for scale & latency.
- **BASE:** "Basically Available, Soft state, Eventual consistency."

## Flavors
- **Key-value** (Redis, DynamoDB): fastest; get/put by key. Great for caching, sessions, leaderboards.
- **Document** (MongoDB): nested JSON-ish; good for object-like data with flexible schema.
- **Column-family** (Cassandra, HBase): wide columns, great write throughput & availability.
- **Graph** (Neo4j): relationships are first-class (social, recommendations, fraud).

## SQL vs NoSQL quick compare
| | SQL (relational) | NoSQL |
|--|------------------|-------|
| Model | Tables, fixed schema | Key-value / doc / column / graph |
| Query | Powerful joins, ACID | Varies; often single-key lookups |
| Scale | Mostly vertical | Horizontal by design |
| Use when | Complex relationships, transactions | Huge scale, flexible schema, high write throughput |

## Choosing one (interview angle)
- Need strong cross-record transactions? -> SQL.
- Massive horizontal scale / unpredictable schema / very high write rate? -> NoSQL (pick flavor by access pattern).
- Often a hybrid: SQL for core transactional data + Redis cache in front.

## Specific NoSQL databases by type

### Key-value
**Redis**
- **Capabilities:** In-memory, sub-ms latency; rich structures (strings, lists, hashes, sets, sorted sets); pub/sub; streams/queues; optional persistence (RDB snapshots / AOF); great for caching, sessions, rate limiting, leaderboards.
- **Limitations:** Memory-bound - cost grows with dataset; core command execution is single-threaded; not a fit for storing huge datasets cheaply.
- **Pick when:** Caching, session store, rate limiting, real-time features, job queues.

**Memcached**
- **Capabilities:** Very fast, simple distributed key-value; multi-threaded; easy horizontal scale for caching.
- **Limitations:** Basic (mostly strings), no rich structures, no persistence (data lost on restart).
- **Pick when:** Pure in-memory caching layer for simple keys across many nodes.

**DynamoDB (AWS)**
- **Capabilities:** Fully managed, auto-scaling, single-digit ms at any scale; optional strong consistency; per-item granularity.
- **Limitations:** Access is keyed (no joins); cost can climb with throughput; vendor lock-in; partition-key design is critical to performance.
- **Pick when:** AWS-native apps wanting a scalable KV store with zero ops.

**etcd / ZooKeeper**
- **Capabilities:** Ordered key-value + watch/consensus (Raft/ZAB) for coordination.
- **Limitations:** Not built for high-throughput data storage; low write throughput by design.
- **Pick when:** Service discovery, distributed locks, config, leader election - NOT primary data storage.

### Document
**MongoDB**
- **Capabilities:** Flexible schema; rich querying (indexes, aggregation pipeline); sharding; multi-document transactions (4.0+).
- **Limitations:** Cross-document joins are less natural (use aggregation); "schemaless" can lead to sprawl without discipline; multi-doc transactions historically weaker than RDBMS.
- **Pick when:** Object-like data, evolving schemas, horizontal scale, content/catalog/inventory data.

**Firestore (Firebase)**
- **Capabilities:** Real-time sync, offline support, built-in auth; great mobile experience.
- **Limitations:** Per-query cost model; vendor lock-in; some complex queries are limited.
- **Pick when:** Mobile-first apps wanting sync + backend in one platform.

**CouchDB**
- **Capabilities:** Replication-first, local-first sync across devices.
- **Limitations:** Smaller ecosystem than Mongo; query model (views/Mango) differs.
- **Pick when:** Offline-first apps that sync between devices/clients.

### Wide-column / column-family
**Cassandra**
- **Capabilities:** Distributed, tunable consistency, excellent write throughput & availability, no single point of failure, horizontal scale.
- **Limitations:** Eventual consistency by default (tunable); no built-in secondary indexes - design denormalized + materialized views/secondary indexes; complex to operate; overkill for small data.
- **Pick when:** Very high write volume, global distribution, need availability over strict consistency.

**HBase**
- **Capabilities:** Scales to very large datasets on HDFS/Hadoop; good for wide tables.
- **Limitations:** JVM/Hadoop ops complexity; not optimized for low-latency KV like Redis.
- **Pick when:** Huge datasets in a Hadoop ecosystem.

**ScyllaDB**
- **Capabilities:** C++ rewrite of the Cassandra API with better single-node performance & multi-core scaling.
- **Limitations:** Younger ecosystem; similar design trade-offs to Cassandra.
- **Pick when:** You want Cassandra-style scale with stronger per-node performance.

### Graph
**Neo4j**
- **Capabilities:** Property graph model; Cypher query language; fast relationship traversals (many hops); ACID transactions.
- **Limitations:** Can be memory-heavy/slower on very large dense graphs; emphasizes vertical scaling; not ideal for simple KV or wide-column workloads.
- **Pick when:** Relationships are the core of your queries - social, recommendations, fraud, knowledge graphs.

**Amazon Neptune**
- **Capabilities:** Managed graph (Property Graph / SPARQL & openCypher); scales without ops.
- **Limitations:** Vendor lock-in; less flexible than self-hosted Neo4j for advanced tuning.
- **Pick when:** AWS-native graph workloads.

## How to pick NoSQL (criteria)
| Access pattern | Best fit |
|----------------|----------|
| Fastest get/put by single key, in-memory | Redis / Memcached |
| Managed scalable KV, no ops | DynamoDB |
| Object-like docs, flexible schema, scale | MongoDB |
| Mobile sync + offline | Firestore / CouchDB |
| Very high write throughput, global, availability-first | Cassandra / ScyllaDB |
| Relationships are the query (many hops) | Neo4j / Neptune |
| Coordination/locks/service discovery | etcd / ZooKeeper |

**Deciding questions:**
1. What's your primary access pattern? (by key -> KV; by object -> doc; by relationship -> graph)
2. How much write throughput & how global? (high/global -> wide-column or DynamoDB)
3. Do you need strong cross-record transactions? (yes -> consider SQL or a transactional NoSQL)
4. Ops budget? (low -> managed: DynamoDB, Firestore, Neptune; high -> Cassandra/Mongo/Neo4j self-hosted)


## Self-test
1. Pick the right NoSQL type for a real-time chat app and justify.
2. What do you give up vs a relational DB, and how do you compensate?
3. When is "eventual consistency" acceptable to your users?
4. Redis vs DynamoDB - when would you pick each?
5. Why does Cassandra need denormalization + careful index design?
6. Graph DB vs relational joins - when is the graph model clearly better?
