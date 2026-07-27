## Module 6: Docker Security

### Root by default — the biggest real-world risk

By default, processes inside a container run as **root** (UID 0) unless you specify otherwise. This sounds contained (pun intended) because of namespaces — but it's still a serious risk.

**Why it matters:** If an attacker exploits a vulnerability in your app and breaks out of the container (a **container escape**, via a kernel bug or misconfiguration), root-inside-container often maps to meaningful privilege on the host too, especially if the container was run with excessive privileges.

**Fix — run as non-root:**
```dockerfile
FROM node:18-alpine
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser
```

**Analogy:** Running a container as root is like giving every hotel guest a master key that opens every room in the building, "just in case they need it." Most guests never misuse it — but if one room's lock gets picked (container escape), the intruder now holds a master key to the whole hotel (host), instead of just that one room's key.

### `--privileged` flag — avoid unless absolutely necessary

Running `docker run --privileged` disables almost all isolation — the container gets access to *all* host devices and capabilities, effectively as powerful as a process running directly on the host. Only legitimate use cases: things like Docker-in-Docker for CI systems, or hardware-level tools. Never use it for a normal web app.

### Linux Capabilities — fine-grained root

Instead of all-or-nothing root, Linux breaks "root power" into ~40 discrete **capabilities** (e.g., `CAP_NET_BIND_SERVICE` to bind ports below 1024, `CAP_SYS_ADMIN` for broad admin actions). Docker drops most dangerous capabilities by default, but you can go further:

```bash
docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE myapp
```

**Analogy:** Instead of handing someone the master key (full root), you hand them a keyring with *only* the specific keys they need — the mailroom key, not the server room key too.

### Seccomp — filtering syscalls

**Seccomp** (secure computing mode) restricts which system calls a container's process can make to the kernel at all. Docker applies a default seccomp profile blocking ~44 dangerous syscalls (like `reboot`, `mount`) out of the box. You can tighten this further with custom profiles for extra-sensitive workloads.

**Analogy:** If capabilities are "which keys you hold," seccomp is "which doors even exist for you to try" — it removes the door entirely, not just the key.

### Image vulnerability scanning

Base images (`node:18`, `python:3.11`, etc.) often bundle OS packages with known CVEs. Tools like **Docker Scout**, **Trivy**, or **Snyk** scan image layers against CVE databases.

**Best practices that come up in interviews:**
- Use minimal base images (`alpine`, `distroless`) — smaller attack surface, fewer packages to have CVEs in
- Pin exact versions (`node:18.19.0-alpine`, not `node:latest`) — reproducibility + avoids surprise vulnerable updates
- Regularly rebuild images even if your code hasn't changed — base image patches security fixes over time, and a stale image misses them

## Interview Question

**Q: A security team flags that your production containers all run as root and use the default `bridge` network with several unnecessary capabilities enabled. Walk through, in priority order, the three changes you'd make and *why* each one specifically reduces blast radius in the event of a container compromise.**

Answer, or say "next" for **Module 7: Orchestration — Docker Swarm vs Kubernetes, and how scheduling/scaling actually works at the cluster level** (this is genuinely FAANG system-design territory).