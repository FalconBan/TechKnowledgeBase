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

## Self-test
1. Pick the right NoSQL type for a real-time chat app and justify.
2. What do you give up vs a relational DB, and how do you compensate?
3. When is "eventual consistency" acceptable to your users?
