**Horizontal scaling has its own disadvantages**, mainly because the system becomes more distributed and therefore more complex.

 ### Cons of horizontal scaling

- **More complexity** — You now have multiple servers that need to work together.
- **Load balancer required** — Requests usually need to be distributed across instances.
- **Data consistency becomes harder** — If multiple servers maintain state/data, keeping them synchronized can be difficult.
- **Session management** — You can't simply store user sessions in one server's memory. You may need Redis/database-backed sessions or another shared mechanism.
- **Deployment becomes more complicated** — You need to deploy and monitor multiple instances.
- **Debugging is harder** — A request might go to Server 1, while the next request goes to Server 3, making failures harder to reproduce.
- **Network communication increases** — Servers may need to communicate with each other, adding latency and failure points.
- **Database can still be the bottleneck** — Adding 20 application servers doesn't automatically make the database 20× faster.
- **Higher infrastructure/operational cost** — More machines mean more monitoring, networking, logging, security, etc.
- **Distributed-system failures** — One server can fail, network connections can fail, or servers can temporarily have different states.