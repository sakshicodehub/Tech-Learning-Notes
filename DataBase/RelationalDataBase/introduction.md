Here's a rundown of the major database types you'll see in the market, along with how each is actually used in production.

## Relational (SQL) Databases

Structured, table-based, with strict schemas and ACID guarantees.

- **MySQL / MariaDB** — Web apps, e-commerce, content management (WordPress, Rails apps). You already work with this.
- **PostgreSQL** — Similar use cases but stronger for complex queries, JSON support, geospatial data (PostGIS). Popular for fintech, analytics-heavy apps.
- **Oracle DB** — Large enterprises, banking, ERP systems (high cost, heavy compliance needs).
- **Microsoft SQL Server** — Enterprise apps in .NET/Windows shops.
- **SQLite** — Embedded use: mobile apps, small desktop tools, local caching.

**Real use:** Anything with clear relationships and needing transactional integrity — orders, payments, user accounts, inventory.

## NoSQL Databases

### Document stores
- **MongoDB, CouchDB** — Flexible schema, JSON-like documents. Used for content catalogs, user profiles, product catalogs where fields vary a lot.

### Key-Value stores
- **Redis, Memcached** — In-memory, extremely fast. Used for caching, session storage, rate limiting, real-time leaderboards. Redis is also often used as a Sidekiq backend, so this one's directly relevant to your stack.
- **DynamoDB (AWS)** — Managed key-value/document store for high-scale apps (Amazon itself, many serverless architectures).

### Column-family stores
- **Cassandra, HBase** — Handle massive write volumes across distributed clusters. Used by Netflix, Instagram-scale systems for time-series data, activity feeds, IoT data.

### Graph databases
- **Neo4j, Amazon Neptune** — Store relationships as first-class citizens. Used for social networks, recommendation engines, fraud detection, knowledge graphs.

### Search-oriented
- **Elasticsearch, Solr** — Full-text search, log analytics. Used for site search features, monitoring/logging dashboards (ELK stack).

## Time-Series Databases
- **InfluxDB, TimescaleDB, Prometheus** — Optimized for timestamped data. Used for monitoring metrics, IoT sensor data, financial tick data.

## NewSQL
- **CockroachDB, Google Spanner, TiDB** — Combine SQL's consistency with NoSQL-style horizontal scaling. Used where you need both strong consistency and global scale (banking, large distributed systems).

## Vector Databases
- **Pinecone, Weaviate, Milvus, pgvector (Postgres extension)** — Store embeddings for similarity search. Used heavily now for AI/LLM applications — semantic search, RAG pipelines, recommendation systems.

## How this maps to a typical real-world stack

Most production systems aren't "one database" — they combine several:
- **Primary transactional store:** PostgreSQL/MySQL for orders, users, core business data
- **Cache layer:** Redis for sessions, hot data, job queues
- **Search:** Elasticsearch for product/content search
- **Analytics/logging:** Time-series DB or a data warehouse (Snowflake, BigQuery, Redshift)
- **Object/file storage:** S3 or similar (not a "database" per se, but part of the picture)

For example, a typical Rails e-commerce app: MySQL/Postgres for the core data, Redis + Sidekiq for background jobs, maybe Elasticsearch for product search, and increasingly a vector DB if they've added AI-powered recommendations or search.

Want me to go deeper on any one category — like when to actually reach for NoSQL over relational, or how sharding/replication works in practice?