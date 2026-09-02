

### How this works
Every week you'll focus on a specific topic. We'll have a weekly 30–45 min followup where I'll:
Check your progress for the week
Ask a few questions to evaluate understanding
Review something you built or refactored
Give feedback and plan the next week
Come prepared. Build something every week — don't just read docs.

## Phase 1 — Core Foundations (Weeks 1–4)
### Week 1 — Rails internals & MVC deep dive Topics: 
    Request lifecycle, routing & constraints, ActiveRecord basics, callbacks & concerns, middleware stack Goal: Be able to explain what happens from the moment a URL is hit to the response being sent back.
### Week 2 — OOP principles in Ruby Topics:
     SOLID principles, inheritance vs composition, modules & mixins, Service Objects, Decorator pattern, Dependency Injection Goal: Refactor a fat model using service objects. Explain each SOLID principle with a real example.
### Week 3 — Database fundamentals Topics:
     Indexing strategy, query optimization, N+1 problems, migration best practices, transactions, ACID properties, joins & subqueries Goal: Diagnose and fix a slow query. Explain ACID with an example.
### Week 4 — Testing culture & TDD Topics: 
    RSpec fundamentals, unit vs integration vs e2e tests, FactoryBot, mocking & stubbing, test coverage mindset Goal: Write a proper test suite for an existing feature. Know the testing pyramid.

## Phase 2 — Mid-Level Engineering Skills (Weeks 5–8)
### Week 5 — API design & REST principles Topics: 
    RESTful conventions, versioning, serializers (JBuilder/AMS), authentication (JWT, OAuth2, Devise), error handling, rate limiting, idempotency Goal: Build a versioned, well-documented API endpoint. Explain the difference between authentication and authorization.
### Week 6 — Frontend essentials for Rails devs Topics:
     JavaScript fundamentals, Hotwire/Turbo/Stimulus, HTML & CSS layout basics, asset pipeline, how browsers render pages (critical rendering path) Goal: Add interactive UI to a Rails app without a full JS framework.
### Week 7 — Background jobs & performance Topics:
     Sidekiq & ActiveJob, caching with Redis, pagination, memoization, profiling tools, database connection pooling Goal: Move a slow controller action to a background job. Explain when to use a job vs a synchronous call.
### Week 8 — Software engineering lifecycle (SDLC) Topics: 
    Agile/Scrum basics, Git branching strategy, PR review etiquette, estimation, documentation, incident management basics Goal: Own a ticket end-to-end — proper branch, PR, description, and docs.

## Phase 3 — System Design & Architecture (Weeks 9–12)
This is what separates mid from senior in interviews. Every topic here is asked regularly.
### Week 9 — Caching (deep dive) Topics:
Cache levels — CPU cache, app-level, DB-level, CDN
Redis vs Memcached — when to use what
Cache strategies — Cache-aside, Write-through, Write-behind, Read-through
Cache invalidation — TTL, event-based, manual busting
Cache stampede & how to prevent it
Rails caching — fragment, action, HTTP caching, Russian doll caching
Interview questions you must be able to answer:
"How would you cache a user's feed that updates frequently?"
"What happens when your cache goes down?"
"How do you handle cache invalidation across microservices?"
Goal: Implement a multi-layer caching strategy in a Rails app.

### Week 10 — Databases at scale — Sharding, Partitioning, Replication Topics:
Sharding — horizontal partitioning across multiple DB instances, shard keys, consistent hashing
Partitioning — range, list, hash partitioning within a single DB
Replication — primary/replica setup, read replicas, replication lag
CAP theorem — Consistency, Availability, Partition tolerance — you can only pick 2
Vertical scaling — bigger machine, limits, cost
Horizontal scaling — more machines, stateless design requirement
Connection pooling, PgBouncer
When to denormalize
Interview questions you must be able to answer:
"Your users table has 500 million rows, how do you scale it?"
"Explain the tradeoffs between sharding and partitioning."
"How do you handle a replication lag of 30 seconds in a read-heavy app?"
Goal: Draw and explain a sharded database architecture for a social media app.

### Week 11 — Distributed Systems fundamentals Topics:
What makes a system distributed and why it's hard
Consistency models — strong, eventual, causal consistency
Consensus algorithms — Raft, Paxos (conceptual understanding)
Message queues — Kafka, RabbitMQ, SQS — async communication, decoupling
Idempotency — why it matters in distributed systems
Distributed transactions — 2-Phase Commit, Saga pattern
Fault tolerance — retries, circuit breakers, timeouts, bulkheads
Service discovery — how services find each other
Clock skew, network partitions, split-brain scenarios
Interview questions you must be able to answer:
"How do you ensure a payment is not processed twice?"
"What happens when two services need to write to different databases atomically?"
"How does a circuit breaker work and when would you use it?"
Goal: Design an order processing system that uses async messaging between services.

### Week 12 — Microservices architecture Topics:
Monolith vs microservices — honest tradeoffs, not just hype
When to break a monolith — strangler fig pattern
Service communication — sync (REST, gRPC) vs async (events/queues)
API Gateway pattern — single entry point, auth, rate limiting, routing
Service mesh — what it is, when you need it (Istio basics)
Data ownership — each service owns its data, no shared DB
Event-driven architecture — event sourcing, CQRS basics
Deployment strategies — blue/green, canary releases
12-factor app principles
Interview questions you must be able to answer:
"How would you split a Rails monolith into microservices?"
"How do you handle a failure in one microservice affecting others?"
"Explain the difference between event-driven and request-driven architecture."
Goal: Draw a microservices diagram for an e-commerce platform (users, orders, inventory, payments, notifications).

## Phase 4 — HLD, LLD & Interview Readiness (Weeks 13–16)
Pure interview prep. This is what gets you the job.
### Week 13 — Low Level Design (LLD) Topics:
Class diagrams — relationships, inheritance, composition
Design patterns — Factory, Singleton, Observer, Strategy, Command, Repository
SOLID in practice — real code examples, not theory
Writing clean, extensible code under time pressure
Common LLD problems: design a parking lot, design an elevator, design a URL shortener, design a rate limiter
Interview prep:
Practice drawing class diagrams on paper/whiteboard
Code each design pattern in Ruby
Focus: "How would you design the classes for X system?"
Goal: Solve 3 LLD problems end-to-end — classes, relationships, and working code.

### Week 14 — High Level Design (HLD) Topics:
HLD framework — requirements clarification → capacity estimation → high-level diagram → deep dives
Capacity estimation — back-of-envelope math (QPS, storage, bandwidth)
Load balancers — L4 vs L7, round robin, consistent hashing
CDN — how it works, when to use it, cache-control headers
Blob storage — S3, presigned URLs, chunked uploads
Search — Elasticsearch basics, full-text search vs DB search
Notification systems — push, email, SMS at scale
Common HLD interview problems:
Design Twitter / Instagram feed
Design a URL shortener
Design WhatsApp / chat system
Design YouTube / video streaming
Design an e-commerce platform
Design a rate limiter
Goal: Practice 2 full HLD problems — whiteboard from scratch in 45 minutes.

### Week 15 — DevOps, Security & Production readiness Topics:
Docker & containers, Docker Compose
CI/CD pipelines (GitHub Actions, CircleCI)
Kubernetes basics — pods, services, deployments, scaling (conceptual)
Observability — logs, metrics, traces (the three pillars)
Monitoring tools — Datadog, New Relic, Sentry
Security — OWASP top 10, SQL injection, XSS/CSRF, secrets management
Authorization patterns — Pundit/CanCan, RBAC vs ABAC
SSL/TLS, HTTPS, certificate basics
Interview questions you must be able to answer:
"How do you debug a production issue where you have no idea what's wrong?"
"Your app is throwing 500 errors but logs show nothing. Walk me through how you'd investigate."
Goal: Set up a CI/CD pipeline with monitoring and alerting for a Rails app.

### Week 16 — Mock interviews & consolidation Topics:
1 full mock LLD interview (45 min)
1 full mock HLD interview (45 min)
Behavioral questions — STAR method, ownership stories, conflict resolution
Reviewing weak areas from weeks 9–15
Resume and communication — how to explain your work clearly
Goal: Walk out of this week able to confidently handle a senior-level system design round.

Quick Reference — Interview Topic Cheat Sheet
Topic
Must Know
Caching
Redis, cache-aside, TTL, invalidation, stampede
Databases
ACID, indexing, sharding, partitioning, replication, CAP
Scaling
Horizontal vs vertical, stateless design, load balancing
Distributed systems
CAP theorem, eventual consistency, idempotency, Saga pattern
Microservices
API gateway, service mesh, event-driven, data ownership
HLD
Capacity estimation, load balancer, CDN, blob storage, queues
LLD
SOLID, design patterns, class diagrams, clean code
DevOps
Docker, CI/CD, Kubernetes basics, observability
Security
OWASP top 10, XSS, CSRF, RBAC, secrets


Weekly Followup Structure
Every week after your self-study, we meet and go through:
Progress recap — what you covered, what was unclear
Mini quiz — 3–5 questions on the week's topic
Code review or design review — something you built or designed that week
Feedback + next week plan — what to focus on, what to strengthen

Consistency beats intensity. One focused week of learning compounds fast. Show up every week, build something, and the progress will be obvious.





