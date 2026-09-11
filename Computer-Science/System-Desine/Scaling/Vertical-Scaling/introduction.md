# cons of vertical scaling
**Hardware limit** — You can only keep making one machine bigger up to a maximum.

**Expensive** — A very powerful server can become much more expensive than several smaller servers.

**Single point of failure** — If that one server goes down, the whole service/database may go down.

**Downtime during upgrades** — Increasing CPU/RAM/storage may require maintenance or migration, depending on the infrastructure.

**Limited fault tolerance** — One machine means less redundancy.

**Doesn't distribute the workload** — You're still relying on one machine to handle everything.

**Diminishing returns** — Doubling hardware doesn't always double performance, because the bottleneck might be disk I/O, locks, network, query design, etc.


Eventually:

The machine has a physical hardware limit.
Bigger machines become disproportionately expensive.
Upgrading may require downtime or migration.
A single machine remains a single point of failure.
Some workloads benefit from distributing data/work across multiple machines.
