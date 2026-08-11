
# 2. Shared DB + Separate Schema

Now things become more interesting.

Here we still have **one PostgreSQL database**, but each tenant gets its own schema.

Think of a schema as a namespace/container for tables.

```text
PostgreSQL Database
│
├── public
│
├── acme
│   ├── users
│   ├── projects
│   └── tasks
│
├── globex
│   ├── users
│   ├── projects
│   └── tasks
│
└── initech
    ├── users
    ├── projects
    └── tasks
```

Now notice something important.

There are actually **three different `users` tables**:

```text
acme.users
globex.users
initech.users
```

And three `projects` tables:

```text
acme.projects
globex.projects
initech.projects
```

---

# Creating the schemas

You might create:

```sql
CREATE SCHEMA acme;
CREATE SCHEMA globex;
CREATE SCHEMA initech;
```

Then:

```sql
CREATE TABLE acme.users (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255),
    email VARCHAR(255)
);
```

And:

```sql
CREATE TABLE acme.projects (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255)
);
```

For Globex:

```sql
CREATE TABLE globex.users (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255),
    email VARCHAR(255)
);
```

```sql
CREATE TABLE globex.projects (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255)
);
```

So now:

```text
acme.projects
```

and:

```text
globex.projects
```

are completely different tables.

---

# Do we still need organization_id?

Usually **not for tenant isolation**.

That's because the schema itself identifies the tenant.

For example:

```sql
SELECT *
FROM acme.projects;
```

means:

> Give me projects belonging to Acme.

While:

```sql
SELECT *
FROM globex.projects;
```

means:

> Give me projects belonging to Globex.

You don't necessarily need:

```text
organization_id
```

inside those tables.

---

# How does the application know which schema to use?

This is the key concept.

Suppose the request is:

```http
GET /projects
```

and the logged-in user belongs to:

```text
organization = Acme
```

Your application determines:

```text
tenant = acme
```

Then it queries:

```sql
SELECT *
FROM acme.projects;
```

For a Globex user:

```sql
SELECT *
FROM globex.projects;
```

Same application code conceptually, but different schema.

---

# PostgreSQL `search_path`

PostgreSQL gives you another interesting mechanism.

You can set:

```sql
SET search_path TO acme;
```

Then:

```sql
SELECT *
FROM projects;
```

actually means:

```sql
SELECT *
FROM acme.projects;
```

For Globex:

```sql
SET search_path TO globex;
```

Then:

```sql
SELECT *
FROM projects;
```

means:

```sql
SELECT *
FROM globex.projects;
```

This can make tenant-aware applications cleaner.

---

# But table creation becomes harder

Here's the major difference.

Suppose you add:

```text
phone_number
```

to users.

With shared schema:

```sql
ALTER TABLE users
ADD COLUMN phone_number VARCHAR(30);
```

Done.

With separate schemas, you need:

```sql
ALTER TABLE acme.users
ADD COLUMN phone_number VARCHAR(30);

ALTER TABLE globex.users
ADD COLUMN phone_number VARCHAR(30);

ALTER TABLE initech.users
ADD COLUMN phone_number VARCHAR(30);
```

And if you have:

```text
10,000 tenants
```

you potentially have:

```text
10,000 users tables
10,000 projects tables
10,000 tasks tables
...
```

A migration has to be applied across all tenant schemas.

---

# How do you handle this in real applications?

You typically maintain a list of tenants:

```text
tenants
----------------
id
name
schema_name
```

For example:

```text
1 | Acme    | tenant_001
2 | Globex  | tenant_002
3 | Initech | tenant_003
```

When a new organization signs up:

```text
Create organization
        ↓
Create schema tenant_004
        ↓
Create all required tables
        ↓
Create initial data
        ↓
Organization is ready
```

So provisioning becomes part of your application.

---
