
# 3. Separate Database Per Tenant

This is the strongest isolation model.

Instead of:

```text
One database
    ↓
many schemas
```

you have:

```text
Database Server
│
├── acme_db
│   ├── users
│   ├── projects
│   └── tasks
│
├── globex_db
│   ├── users
│   ├── projects
│   └── tasks
│
└── initech_db
    ├── users
    ├── projects
    └── tasks
```

Every tenant has its own database.

---

# Table creation

For Acme:

```sql
CREATE DATABASE acme_db;
```

Connect to `acme_db`:

```sql
CREATE TABLE users (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255),
    email VARCHAR(255)
);

CREATE TABLE projects (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE tasks (
    id BIGSERIAL PRIMARY KEY,
    project_id BIGINT,
    title VARCHAR(255)
);
```

For Globex, you create another database:

```sql
CREATE DATABASE globex_db;
```

And the same tables:

```sql
CREATE TABLE users (...);
CREATE TABLE projects (...);
CREATE TABLE tasks (...);
```

So the structure is duplicated between databases.

---

# How does the application know which DB to connect to?

You need a **tenant registry** somewhere.

For example, you might have a central database:

```text
tenant_registry

tenant_id | name     | database
---------------------------------
1         | Acme     | acme_db
2         | Globex   | globex_db
3         | Initech  | initech_db
```

The request arrives:

```text
User → API
```

The application identifies:

```text
user → organization 1 → Acme
```

Then looks up:

```text
Acme → acme_db
```

and obtains a connection to:

```text
acme_db
```

Then:

```sql
SELECT *
FROM projects;
```

There is no need for:

```sql
WHERE organization_id = 1
```

because that database belongs exclusively to Acme.

---
