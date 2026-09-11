## Load Balancing in System Design

Load balancing is the practice of distributing incoming network traffic or requests across multiple servers, so no single server gets overwhelmed. It sits between clients and your backend servers, deciding which server should handle each request.

### Why it matters

- **Availability**: if one server crashes, the load balancer routes traffic to the remaining healthy servers instead of the whole system going down.
- **Scalability**: as traffic grows, you add more servers behind the load balancer instead of trying to make one server infinitely powerful.
- **Performance**: requests get spread out, so response times stay low even under heavy load.

### Common algorithms

1. **Round Robin** — requests go to servers in rotating order (Server A, B, C, A, B, C...).
2. **Least Connections** — new requests go to whichever server currently has the fewest active connections.
3. **Weighted Round Robin / Least Connections** — same as above, but more powerful servers get proportionally more traffic.
4. **IP Hash** — a client's IP is hashed to consistently route them to the same server (useful for session persistence).
5. **Least Response Time** — routes to the server responding fastest, factoring in current load.

### Types of load balancers

- **Layer 4 (Transport layer)**: routes based on IP and port, without inspecting the actual content of packets. Fast but less flexible.
- **Layer 7 (Application layer)**: can inspect HTTP headers, cookies, URLs, etc., and route intelligently (e.g., send `/api/*` to one set of servers, `/images/*` to another).

### Where load balancers fit

```
Client → Load Balancer → [Server 1, Server 2, Server 3, ...]
```

They're often placed at multiple points in a system:
- Between clients and web servers
- Between web servers and application servers
- Between application servers and databases (read replicas)

### Health checks

Load balancers regularly ping backend servers to check they're alive. If a server fails a health check, it's temporarily removed from rotation until it recovers — this is key to the availability benefit.

### Popular tools

- **Software**: NGINX, HAProxy, Envoy
- **Cloud-managed**: AWS ELB/ALB/NLB, GCP Load Balancer, Azure Load Balancer

If you're studying this for interviews, a common follow-up question is "how do you handle load balancer failure itself" — the answer is usually running multiple load balancers behind a DNS round-robin or using a floating IP with a failover mechanism (like keepalived).

Want me to go deeper into any specific area — like how this applies to a Rails/Sidekiq setup, or the trade-offs between L4 vs L7?