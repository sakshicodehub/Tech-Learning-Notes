### Multi-tenancy is primarily an application architecture/design pattern.
Systems use shared databases separated by logical rules, unique tenant ID tags, or separate schemas to manage user data.

The easiest way to understand multi-tenancy deeply is to use **one example application** and see how the same tables are created and queried under each architecture.

Let's use a SaaS application with multiple organizations:

* `Acme Corp`
* `Globex`
* `Initech`

And suppose every organization has users, projects, and tasks.

---

# The really important difference

Let's compare what happens when you create a new tenant.

### Shared schema

New tenant:

```text
organization_id = 4
```

That's basically it.

No tables need to be created.

```text
users
projects
tasks
```

already exist.

---

### Separate schema

New tenant:

```text
CREATE SCHEMA tenant_004;
```

Then create:

```text
tenant_004.users
tenant_004.projects
tenant_004.tasks
```

---

### Separate database

New tenant:

```text
CREATE DATABASE tenant_004_db;
```

Then create:

```text
users
projects
tasks
```

inside that database.

---

# Think about it like apartments

A useful analogy is an apartment building.

### Shared DB + shared schema

One giant room:

```text
┌───────────────────────────────┐
│ users                         │
│                               │
│ Acme data                     │
│ Globex data                   │
│ Initech data                  │
└───────────────────────────────┘
```

Everyone shares the same tables.

`organization_id` tells you whose data is whose.

---

### Shared DB + separate schema

One building, separate apartments:

```text
Building
│
├── Acme apartment
│   ├── users
│   ├── projects
│   └── tasks
│
├── Globex apartment
│   ├── users
│   ├── projects
│   └── tasks
│
└── Initech apartment
    ├── users
    ├── projects
    └── tasks
```

Same building/database, but separate spaces/schemas.

---

### Separate DB

Separate houses:

```text
🏠 Acme
   └── Database

🏠 Globex
   └── Database

🏠 Initech
   └── Database
```

Much stronger physical/logical separation.

---

# Now let's look at one complete request

Suppose:

```http
GET /projects
```

is sent by an Acme employee.

## Approach 1 — Shared schema

Application knows:

```text
organization_id = 1
```

Query:

```sql
SELECT *
FROM projects
WHERE organization_id = 1;
```

---

## Approach 2 — Separate schema

Application knows:

```text
schema = tenant_001
```

Query:

```sql
SELECT *
FROM tenant_001.projects;
```

or:

```sql
SET search_path TO tenant_001;

SELECT *
FROM projects;
```

---

## Approach 3 — Separate database

Application knows:

```text
database = acme_db
```

Connection:

```text
PostgreSQL → acme_db
```

Query:

```sql
SELECT *
FROM projects;
```

---

# What about IDs?

This is another subtle difference.

> With shared schema, you might have:

```text
projects

id   organization_id   name
--------------------------------
1    1                  CRM
2    1                  Website
3    2                  Billing
4    2                  Analytics
```

> IDs are globally unique.

Alternatively, you might use a composite uniqueness rule:

```sql
UNIQUE (organization_id, name)
```

or:

```sql
UNIQUE (organization_id, id)
```

depending on your design.

> With separate schemas:

```text
tenant_001.projects
id = 1
id = 2
```

and:

```text
tenant_002.projects
id = 1
id = 2
```

That's perfectly fine because they're different tables.

> With separate databases, the same thing happens:

```text
acme_db.projects
id = 1

globex_db.projects
id = 1
```

Again, no conflict.

---

# What about backups?

This is another major architectural difference.

### Shared schema

**You usually back up the whole database**:

```text
myapp DB
 ├── Acme
 ├── Globex
 ├── Initech
 └── ...
```

Restoring **only Acme** can be complicated.

---

### Separate schema

Still one database:

```text
myapp DB
 ├── tenant_001
 ├── tenant_002
 └── tenant_003
```

You have better logical separation, but tenant-specific backup/restore is still more complicated than separate databases.

---

### Separate database

Very easy conceptually:

```text
acme_db → backup
globex_db → backup
initech_db → backup
```

You can restore Acme without touching Globex.

This is one reason companies with strict data requirements sometimes choose database-per-tenant.

---

# What about performance?

There isn't one universal winner.

### Shared schema

You have potentially huge tables:

```text
projects
---------
10 million rows
```

Indexes become extremely important.

You would typically index:

```sql
CREATE INDEX idx_projects_org
ON projects(organization_id);
```

For queries such as:

```sql
WHERE organization_id = ?
```

---

### Separate schemas

Each tenant's table is smaller:

```text
tenant_001.projects → 5,000 rows
tenant_002.projects → 20,000 rows
tenant_003.projects → 3,000 rows
```

You don't have one giant `projects` table containing every tenant.

---

### Separate databases

Same benefit, plus stronger infrastructure isolation.

But now you have potentially thousands of databases/connections to manage.

---

# The migration problem is very important

Imagine you have:

```text
50,000 tenants
```

and your application changes:

```sql
ALTER TABLE users
ADD COLUMN last_login_at TIMESTAMP;
```

### Shared schema

One migration:

```sql
ALTER TABLE users
ADD COLUMN last_login_at TIMESTAMP;
```

Very easy.

### Separate schema

Potentially:

```text
50,000 schemas
```

You need to update:

```text
tenant_001.users
tenant_002.users
...
tenant_50000.users
```

You need good migration/provisioning automation.

### Separate database

Potentially:

```text
50,000 databases
```

You need to migrate every database.

This is operationally much harder.

---

# Security isolation

This is where the three architectures differ significantly.

### Shared schema

Application must correctly enforce:

```sql
WHERE organization_id = ?
```

A bug like:

```sql
SELECT * FROM projects;
```

could expose **every tenant's projects**.

You can improve this significantly with PostgreSQL Row-Level Security (RLS), but the architecture still relies on careful tenant scoping.

---

### Separate schema

A query against:

```text
tenant_001.projects
```

doesn't naturally return:

```text
tenant_002.projects
```

You have a stronger boundary.

But your application must correctly select the schema, and database permissions/search-path handling must be designed carefully.

---

### Separate database

The application connects to:

```text
acme_db
```

Acme queries can't simply access Globex's tables because they're in another database.

You get the strongest isolation of the three.

---

# One more architecture you should know

In real SaaS systems, you don't necessarily have to choose **one architecture for every tenant**.

A very common advanced design is **hybrid multi-tenancy**.

For example:

```text
                    SaaS Platform
                         │
        ┌────────────────┼────────────────┐
        │                │                │
   Small tenants    Medium tenants    Enterprise
        │                │                │
 Shared schema     Separate schema    Separate DB
```

For example:

```text
1,000 small customers
        ↓
shared database/schema

100 medium customers
        ↓
separate schemas

10 enterprise customers
        ↓
dedicated databases
```

This gives you a nice balance.

A small customer doesn't need their own database.

But a huge enterprise customer might say:

> "Our data must be isolated in a dedicated database."

You can give them:

```text
enterprise_acme_db
```

while smaller customers remain in:

```text
shared_saas_db
```

---

# The key mental model

If you remember only this, remember:

```text
                    TENANT ISOLATION
                           │
          ┌────────────────┼─────────────────┐
          │                │                 │
          ▼                ▼                 ▼
   organization_id      schema          database
          │                │                 │
          ▼                ▼                 ▼
     same tables      separate tables   separate tables
     same DB          same DB           separate DB
```

### Shared DB + Shared Schema

```text
DB
└── projects
    ├── tenant A rows
    ├── tenant B rows
    └── tenant C rows
```

### Shared DB + Separate Schema

```text
DB
├── tenant_a
│   └── projects
├── tenant_b
│   └── projects
└── tenant_c
    └── projects
```

### Separate DB

```text
tenant_a_db
└── projects

tenant_b_db
└── projects

tenant_c_db
└── projects
```

And **table creation is the key difference**:

```text
Shared schema:
    CREATE TABLE projects          ← once

Separate schema:
    CREATE TABLE tenant_a.projects ← once per tenant

Separate DB:
    CREATE TABLE projects           ← once per database/tenant
```