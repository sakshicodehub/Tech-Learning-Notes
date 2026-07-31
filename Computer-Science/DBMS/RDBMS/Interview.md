
## Now — Interview Sub-Questions (Senior Dev Level)

Answer these in your own words like you've been doing with Docker. I'll grade + correct.

**Round 1 — Fundamentals:**
1. What's the difference between a **primary key** and a **unique key**? Can a table have multiple of each?
2. Explain **normalization** — what problem does 1NF → 2NF → 3NF each solve? Give an example of data that violates 2NF.
3. What is **denormalization** and when would a senior dev deliberately choose it?

**Round 2 — Transactions & Concurrency (this is where senior devs get filtered):**
4. Explain **ACID** — give a real example of what breaks if each property is missing.
5. What's the difference between **pessimistic locking** and **optimistic locking**? When would you use each?
6. Explain **MVCC (Multi-Version Concurrency Control)**. Why do Postgres/MySQL use it instead of just locking everything?
7. What are the 4 **isolation levels**? What's a **dirty read**, **non-repeatable read**, and **phantom read** — give one line each.

**Round 3 — Performance (practical, gets asked a lot):**
8. How does a **B-Tree index** actually speed up a query? Why not just index every column?
9. What's the difference between a **clustered** and **non-clustered index**?
10. You have a query that's slow on a 10M row table — walk me through how you'd debug it (hint: `EXPLAIN ANALYZE`).






## Q1 — Primary Key vs Unique Key 

> **Primary Key and Unique Key are both used to keep data unique, but they have different purposes.**
>
> A **Primary Key** is the main column used to identify each row in a table. It **cannot contain NULL values** and **must be unique**. Every table can have **only one Primary Key**, although it can be made up of multiple columns (called a composite primary key).
>
> A **Unique Key** also makes sure that duplicate values are not allowed, but it is **not the main identifier** of the table. A table can have **multiple Unique Keys**. In most databases, a Unique Key can contain **NULL values** (often multiple NULLs are allowed, depending on the database system).
>
> **One important difference:** A Primary Key does **not** become a Foreign Key. The Primary Key stays in its own table. When another table references that Primary Key, the column in the second table is called a **Foreign Key**.
>
> **Example:**
>
> * In the `users` table, `user_id` is the **Primary Key**.
> * In the `orders` table, `user_id` is the **Foreign Key** because it refers to the `users` table.
>
> **Real-life example:** Think of **Aadhaar** and **PAN**. Both are unique, so both can be candidate keys. If the system chooses **Aadhaar** as the main identifier, it becomes the **Primary Key**, while **PAN** can remain a **Unique Key**.

### Short interview version (30 seconds)

> "A Primary Key uniquely identifies each row in a table. It is always unique, cannot be NULL, and a table can have only one Primary Key. A Unique Key also enforces uniqueness but is not the main identifier. A table can have multiple Unique Keys, and in most databases they allow NULL values. Also, a Primary Key remains a Primary Key in its own table. When another table references it, that column in the other table is called a Foreign Key."




## Q2 — Normalization — Correction
---

### **Normalization (NF) Interview Answer**

> **Normalization is the process of organizing data in a database to reduce redundancy and improve data integrity. It divides data into multiple tables so that duplicate data is minimized and updates become easier.**

### **1NF (First Normal Form) – Atomic Values**

> **1NF says that every column should contain only one value.** We should not store multiple values in a single column.

**Example:**

❌ Before:

```
user_id | phone_numbers
1       | 9876543210,9123456780
```

✅ After:

```
user_id | phone_number
1       | 9876543210
1       | 9123456780
```

**Interview line:**

> "1NF removes multi-valued columns and makes every field atomic."

---

### **2NF (Second Normal Form) – Remove Partial Dependency**

> **2NF applies only when there is a composite primary key.** Every non-key column should depend on the entire primary key, not just part of it.

**Example:**

```
OrderDetails
(order_id, product_id, product_name, quantity)

Primary Key = (order_id, product_id)
```

Here, `product_name` depends only on `product_id`, not on the full key.

**Fix:**

```
Products
product_id | product_name

OrderDetails
order_id | product_id | quantity
```

**Interview line:**

> "2NF removes partial dependency by moving attributes that depend on only part of a composite key into a separate table."

---

### **3NF (Third Normal Form) – Remove Transitive Dependency**

> **3NF says that non-key columns should depend only on the primary key, not on another non-key column.**

**Example:**

```
Employee
emp_id | dept_id | dept_name
```

Here, `dept_name` depends on `dept_id`, not directly on `emp_id`.

**Fix:**

```
Employee
emp_id | dept_id

Department
dept_id | dept_name
```

**Interview line:**

> "3NF removes transitive dependency by separating data that depends on another non-key column."

---

## Easy way to remember

* **1NF → One value per cell (Atomic values)**
* **2NF → No Partial Dependency (Composite key issue)**
* **3NF → No Transitive Dependency (Non-key depends on non-key)**

---

## 1-Minute Interview Answer

> "Normalization is the process of organizing data to reduce redundancy and improve consistency. In **1NF**, every column should have only one atomic value. In **2NF**, if there is a composite primary key, every non-key column must depend on the entire key, removing partial dependencies. In **3NF**, non-key columns should depend only on the primary key and not on other non-key columns, removing transitive dependencies. This improves data integrity and avoids duplicate data."

---

### Give an example of data that violates 2NF.

A simple interview example is:

### **Table: OrderDetails**

| order_id | product_id | product_name | quantity |
| -------- | ---------- | ------------ | -------- |
| 101      | P1         | Laptop       | 2        |
| 101      | P2         | Mouse        | 1        |
| 102      | P1         | Laptop       | 1        |

**Primary Key:** `(order_id, product_id)` (composite key)

### Why does it violate 2NF?

* `quantity` depends on the **entire** primary key `(order_id, product_id)` because you need both the order and the product to know the quantity.
* But `product_name` depends **only on `product_id`**, not on `order_id`.

This is called a **partial dependency**, which violates **2NF**.

### How to fix it?

Split the table into two tables.

**Products**

| product_id | product_name |
| ---------- | ------------ |
| P1         | Laptop       |
| P2         | Mouse        |

**OrderDetails**

| order_id | product_id | quantity |
| -------- | ---------- | -------- |
| 101      | P1         | 2        |
| 101      | P2         | 1        |
| 102      | P1         | 1        |

Now:

* `product_name` is stored only once in the `Products` table.
* `OrderDetails` contains only columns that depend on the full composite key.
* The table is in **2NF**.

### Interview answer (30 seconds)

> "A common example is an `OrderDetails` table with a composite primary key `(order_id, product_id)`. If the table also stores `product_name`, it violates 2NF because `product_name` depends only on `product_id`, not on the entire composite key. This is called a partial dependency. We fix it by moving `product_name` to a separate `Products` table."





## Q3 — Denormalization (your turn was empty, here's the explanation)

**Denormalization** = deliberately introducing redundancy (duplicate data, or pre-joined data) into a normalized schema to **improve read performance**, at the cost of extra storage and update complexity.

**Why a senior dev chooses it:**
- Normalized schema = great for data integrity, bad for read-heavy systems with heavy joins
- If a query joins 5 tables on every page load (e.g., an e-commerce order summary combining `orders`, `users`, `products`, `payments`), and this runs thousands of times/sec — the join cost adds up
- Solution: store a **denormalized "read" table/view** — e.g., an `order_summary` table with `user_name`, `product_name` copied directly into it, avoiding joins at read time

**Real-world analogy** (like your Docker layer cache): normalization = single source of truth (like a base image), denormalization = a pre-baked image with everything included so you don't rebuild every layer on every request. Faster to read, but now you have to keep the duplicate in sync (like invalidating cache).

**Tradeoff to say out loud in interview:** "Normalize for write-heavy, transactional correctness. Denormalize for read-heavy, latency-sensitive paths — and accept the sync/consistency burden that comes with it." That sentence alone signals seniority.

---

Ready for **Round 2 (Transactions & Concurrency)** —

**Q4: Explain ACID — give a real example of what breaks if each property is missing.**



## Correction: The "I" is **Isolation**, not "Idempotency"

Idempotency is a *different* concept entirely (means: performing an operation multiple times has the same effect as doing it once — used in API design, retry logic, payment gateways). Don't mix these up in an interview — they'll immediately flag it.


**ACID = Atomicity, Consistency, Isolation, Durability**

Your *explanation* of "I" (transactions should be separated, not interfere with each other) — that's actually a correct description of **Isolation**, you just mislabeled it. Good instinct, wrong word.

---

## Let me sharpen each with "what breaks if missing":

**Atomicity** — "all or nothing." 
- **What breaks without it**: A debits ₹1000 from A, but before crediting B, the system crashes. Without atomicity, A's debit **stays committed** but B never gets credited. Money vanishes into thin air. Your example was right, just make sure you can say the mechanism: **the DB uses transaction logs / rollback segments to undo partial work** if a crash happens mid-transaction.

**Consistency** — the DB moves from one **valid state to another valid state**, respecting all constraints (FKs, checks, triggers).
- **What breaks without it**: Say there's a constraint "balance cannot go negative." Without consistency enforcement, a bug could let A's balance go to -₹500 through a rogue transaction that skipped validation, leaving the DB in an *invalid* state, even though the transaction "completed."
- Note: consistency is about **rule/constraint enforcement**, not just "before/after total is same" — that total-preservation is actually a *side-effect of atomicity+consistency together* in the bank example, not consistency alone. Worth knowing the distinction.

**Isolation** — concurrent transactions shouldn't see each other's uncommitted/intermediate state.
- **What breaks without it**: A sends ₹500 to B. Simultaneously, someone reads B's balance mid-transaction and sees a **partially updated** value (a "dirty read") — makes a decision based on data that never actually got finalized. Your locking example is right — that's literally how isolation is *implemented* (locks or MVCC).

**Durability** — once committed, data survives crashes/power loss — it's on disk, not just in memory.
- **What breaks without it**: Transaction commits, bank shows "success" to user, but server crashes 1 second later before the write is flushed to disk. Without durability, that commit is **lost** — user sees "success" but money never actually moved. This is why DBs use **write-ahead logs (WAL)** flushed to disk **before** acknowledging commit.

---

> **ACID is a set of four properties that make database transactions reliable and safe. It stands for Atomicity, Consistency, Isolation, and Durability.**

* **Atomicity (All or Nothing):**
  A transaction should either complete fully or not happen at all.
  **Example:** If ₹1000 is deducted from Account A but the system crashes before adding it to Account B, the whole transaction is rolled back. Otherwise, money would disappear.

* **Consistency (Rules are Always Followed):**
  A transaction should take the database from one valid state to another, following all constraints and rules.
  **Example:** If a bank has a rule that an account balance cannot go below zero, the database should reject any transaction that breaks this rule. Without consistency, invalid data can be stored.

* **Isolation (Transactions Don't Interfere):**
  Multiple transactions running at the same time should not affect each other or see incomplete changes.
  **Example:** While one transaction is transferring money, another user should not see the balance in the middle of the transfer. Otherwise, they might read incorrect or temporary data.

* **Durability (Data Stays Saved):**
  Once a transaction is committed, the data is permanently saved, even if the system crashes immediately afterward.
  **Example:** If the bank shows "Transfer Successful" and then the server crashes, the transaction should still be there after restart.

**Simple one-line conclusion:**

> **"ACID ensures that database transactions are safe, accurate, and reliable by making sure they complete correctly, follow rules, don't interfere with each other, and are permanently saved after commit."**



**Q5**: What's the difference between **pessimistic locking** and **optimistic locking**? When would you use each? Take your shot.

