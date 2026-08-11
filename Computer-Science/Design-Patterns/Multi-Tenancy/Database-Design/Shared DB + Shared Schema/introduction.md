
# 1. Shared DB + Shared Schema

This is the **simplest and most common** approach.

### Architecture

```text
                PostgreSQL
                    │
        ┌───────────┼───────────┐
        │           │           │
      users       projects     tasks
        │           │           │
        └──────── organization_id
```

There is **one database** and **one set of tables**.

### Database

```text
myapp
```

### Tables

```text
users
projects
tasks
organizations
```

> The important part is that tenant-specific tables contain an `organization_id`.

For example:

```sql
CREATE TABLE organizations (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);
```

Then:

```sql
CREATE TABLE users (
    id BIGSERIAL PRIMARY KEY,
    organization_id BIGINT NOT NULL,
    name VARCHAR(255),
    email VARCHAR(255),

    FOREIGN KEY (organization_id)
        REFERENCES organizations(id)
);
```

And:

```sql
CREATE TABLE projects (
    id BIGSERIAL PRIMARY KEY,
    organization_id BIGINT NOT NULL,
    name VARCHAR(255),

    FOREIGN KEY (organization_id)
        REFERENCES organizations(id)
);
```

Tasks:

```sql
CREATE TABLE tasks (
    id BIGSERIAL PRIMARY KEY,
    organization_id BIGINT NOT NULL,
    project_id BIGINT NOT NULL,
    title VARCHAR(255),

    FOREIGN KEY (organization_id)
        REFERENCES organizations(id),

    FOREIGN KEY (project_id)
        REFERENCES projects(id)
);
```

---

## What does the data look like?

Suppose:

```text
organizations

id    name
----------------
1     Acme
2     Globex
3     Initech
```

The `projects` table contains everyone's projects:

```text
id    organization_id    name
-----------------------------------
101   1                  CRM
102   1                  Website
103   2                  Billing
104   2                  Analytics
105   3                  HR System
```

So Acme owns:

```text
101 CRM
102 Website
```

Globex owns:

```text
103 Billing
104 Analytics
```

Initech owns:

```text
105 HR System
```

There is **not** a separate `projects` table for each organization.

There is only:

```text
projects
```

---

## How does querying work?

This is the most important part.

If Acme's `organization_id` is `1`:

```sql
SELECT *
FROM projects
WHERE organization_id = 1;
```

You get:

```text
CRM
Website
```

For Globex:

```sql
SELECT *
FROM projects
WHERE organization_id = 2;
```

You get:

```text
Billing
Analytics
```

### The application must always enforce this

For example, a dangerous query is:

```sql
SELECT *
FROM projects
WHERE id = 101;
```

It doesn't explicitly verify that project `101` belongs to the current organization.

Instead:

```sql
SELECT *
FROM projects
WHERE id = 101
  AND organization_id = 1;
```

This is the fundamental rule of shared-schema multi-tenancy:

> **Every tenant-owned query must be scoped by `organization_id`.**

---

# What happens when you create a table?

This is where people sometimes get confused.

Suppose you add a new feature:

```text
invoices
```

You create **one table**:

```sql
CREATE TABLE invoices (
    id BIGSERIAL PRIMARY KEY,
    organization_id BIGINT NOT NULL,
    amount DECIMAL(10,2),
    created_at TIMESTAMP,

    FOREIGN KEY (organization_id)
        REFERENCES organizations(id)
);
```

You don't create:

```text
acme_invoices
globex_invoices
initech_invoices
```

You create only:

```text
invoices
```

And put:

```text
organization_id
```

on it.

---

## How migrations work

Suppose tomorrow you add:

```text
phone_number
```

to users.

You run:

```sql
ALTER TABLE users
ADD COLUMN phone_number VARCHAR(30);
```

**Once.**

It automatically applies to every tenant because every tenant uses the same table.

That's a huge advantage.

---
