
## Module 3: Networking & Volumes (Data Persistence)

### The core problem containers create

Containers are ephemeral and isolated by default — but real apps need to (a) **talk to each other** and (b) **persist data** beyond the container's life. Let's tackle networking first.

### Docker Networking — the main types

**1. Bridge network (default)**

When you run a container without specifying a network, Docker creates a private virtual network (`docker0` bridge) on the host. Containers on this bridge get internal IPs and can talk to each other, but the outside world can't reach them unless you explicitly publish ports (`-p 8080:80`).

**Analogy:** A bridge network is like an apartment building with an internal intercom system. Residents (containers) can call each other directly using the intercom (internal IP), but to let someone from the street (outside world) reach a specific resident, you need a doorman who forwards street-door buzzes to a specific apartment (`-p hostPort:containerPort` is that doorman/forwarding rule).

**2. Host network**

The container skips its own network namespace entirely and directly uses the host's network stack. No port mapping needed — if your app listens on port 3000, it's on port 3000 on the host directly.

**Analogy:** Instead of living in a separate apartment, the container moves into your house directly and just uses your home's front door and phone line. Faster (no translation overhead) but zero isolation — port conflicts become your problem, and it's less secure.

**3. None network**

No networking at all — fully isolated. Used for security-sensitive batch jobs that shouldn't talk to anything.

**4. Overlay network**

Used in multi-host setups (Docker Swarm or Kubernetes-adjacent scenarios) — lets containers on **different physical machines** talk to each other as if they were on the same local network, using an encapsulated virtual network layer (VXLAN under the hood).

**Analogy:** If bridge is an intercom *within* one building, overlay is a private company phone line that connects employees across *different office buildings in different cities* — dial an extension, and it doesn't matter which building the person physically sits in.

### DNS-based service discovery (important nuance)

On a **custom** bridge network (not the default one), Docker gives each container automatic DNS resolution by container name. So container `api` can reach container `db` just by hostname `db:5432` — no hardcoded IPs needed. This is *why* Docker Compose "just works" for multi-container apps — Compose creates a custom network automatically.

**Gotcha interview point:** This automatic DNS resolution does **NOT** work on the *default* bridge network — only on user-defined bridge networks. This trips a lot of people up.

### Volumes — solving data persistence

Container filesystems die with the container (remember: writable layer is deleted). For a database, that's catastrophic. Solutions:

- **Named volumes**: `docker volume create mydata`, then `-v mydata:/var/lib/mysql`. Docker manages the storage location on the host, fully decoupled from any single container.
- **Bind mounts**: `-v /host/path:/container/path`. Directly maps a host folder into the container — common for local dev (live-reloading code).
- **tmpfs mounts**: stored in host RAM only, never touches disk — used for sensitive, temporary data (secrets, session tokens).

**Analogy:** A container's own filesystem is like a hotel room's minibar — restocked fresh (wiped) every time a new guest checks in. A **volume** is like a hotel safety deposit box in the basement — it exists independently of any specific room, and you can plug it into whichever room (container) you check into next. Your valuables (data) survive even if the room gets renovated (container destroyed).

## Interview Question

**Q: You have a MySQL container. You stop and remove the container (`docker rm`), then start a brand-new MySQL container. Under what condition does your data survive, and under what condition is it gone forever? Answer in terms of volumes vs. no volumes.**

yes the data is gone forever but id we use volums so the data will remain in volums. the data volums means the same direcory data we are saving and using in local machin directory so the container delete the data gone but as we have same data on local directory it remains on there

Correcting your answer

What you described — "the same directory data we are saving and using in local machine directory" — is a bind mount. You explicitly point to a folder you already know the path of on your host machine (e.g., /home/sakshi/data:/var/lib/mysql), and you can cd into it yourself and see the files directly.

A named volume is different: you don't pick the host path. Docker creates and manages it for you in its own internal storage area (something like /var/lib/docker/volumes/mydata/_data on Linux), and you're not meant to touch that path directly — you only interact with it through Docker (docker volume ls, docker volume inspect, mounting it into containers by name).

Correct full answer:

**If the MySQL container was run without any volume or bind mount, all its data lives only in the container's writable layer — so docker rm destroys the data permanently, with no recovery possible. If it was run with a volume (-v mydata:/var/lib/mysql) or a bind mount (-v /host/path:/var/lib/mysql), the actual data files live outside the container's lifecycle. So even after docker rm, the volume still exists independently. Starting a new container and mounting that same volume/path restores the exact data — MySQL just picks up where it left off, seeing its data directory already populated."**

Why the volume-vs-bind-mount distinction matters in interviews: Named volumes are the recommended approach for production because they're portable across OSes, Docker manages permissions/drivers for you, and they can use volume plugins (for cloud storage, NFS, etc.). Bind mounts are mainly for local dev (live code reload) because they tie you to a specific host filesystem path/structure.

Good grasp overall though — the core mental model (container ephemeral, volume persistent) is right, which is the important part.