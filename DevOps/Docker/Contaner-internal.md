
## Module 5: Container Internals — What Actually Happens Under `docker run`

This is where interviews shift from "can you use Docker" to "do you understand what Docker *is*." Most engineers stop at Module 1-4. Knowing this layer is what impresses senior/staff interviewers.

### The layered architecture (top to bottom)

```
docker CLI  →  Docker Daemon (dockerd)  →  containerd  →  containerd-shim  →  runc  →  Linux kernel (namespaces + cgroups)
```

Let's unpack why there are so many layers.

**1. Docker CLI** — just sends a REST API request to `dockerd`. The CLI itself does almost nothing; it's a thin client.

**2. `dockerd` (Docker Daemon)** — handles higher-level stuff: image management, networking, volumes, build. But — and this is the key historical fact — **Docker doesn't actually create containers itself anymore.** It delegates that job.

**3. `containerd`** — a separate, independent project (donated to CNCF) that manages the container lifecycle: pulling images, managing storage, starting/stopping containers. It's the "manager" that coordinates but doesn't do the actual low-level kernel work either.

**4. `containerd-shim`** — a small process inserted between `containerd` and the actual container process. Its job: if `containerd` (or even `dockerd`) crashes or restarts, **your running containers don't die** — the shim keeps holding the container's process open, detached from the parent. This is why you can restart the Docker daemon without killing your running containers.

**Analogy:** the shim is like a middle-manager who keeps a project running even if the CEO (dockerd) or VP (containerd) temporarily steps out of the building. The actual workers (container processes) don't stop just because leadership is rebooting.

**5. `runc`** — the actual low-level tool that does the real work: creates namespaces, sets up cgroups, and finally calls `execve()` to run your container's process. `runc` is an implementation of the **OCI Runtime Spec** (Open Container Initiative) — an industry-standard spec that *any* container runtime can implement (that's why `runc`, `crun`, `gVisor`, `kata-containers` are all swappable — Docker isn't tied to one specific runtime implementation).

**6. Linux kernel** — actually enforces the namespaces/cgroups that `runc` requested. This is the ground floor — everything above is orchestration around this reality.

### Why does this layered design exist? (real interview question)

Historically, Docker was one big monolithic binary — CLI, daemon, and container execution all coupled together. This caused two problems:
1. **No standardization** — other tools (Kubernetes) couldn't reuse Docker's container-running logic without pulling in all of Docker.
2. **Fragility** — if the daemon crashed/updated, every container died with it.

So Docker split itself apart: donated `runc` and the OCI spec to standardize "how do you actually run a container," and donated `containerd` to CNCF as a reusable, daemon-agnostic container lifecycle manager. **This is exactly why Kubernetes doesn't need Docker installed anymore** — it talks to `containerd` (or `CRI-O`) directly via the **CRI (Container Runtime Interface)**, skipping `dockerd` entirely. This was the whole "Kubernetes deprecating Docker" news a few years back — Kubernetes never depended on `dockerd`, it depended on `containerd`, which Docker itself also uses.

## Interview Question (senior/staff level — this is a real FAANG-tier question)

**Q: Kubernetes announced it was "deprecating Docker support." Does this mean you can no longer run Docker-built images on Kubernetes? Explain what actually changed, using the layers we just discussed.**
## Answer: Kubernetes "deprecating Docker" — what actually changed

**No** — Docker-built images run on Kubernetes exactly as before, with zero issues. This is the single most misunderstood headline in container tech, and it's a great interview trap because it tests whether you actually understand the layers from Module 5.

**What changed:** Kubernetes had a component called **`dockershim`** — a compatibility adapter that let Kubernetes talk to `dockerd` using the CRI (Container Runtime Interface) protocol, even though `dockerd` didn't natively speak CRI. Kubernetes removed `dockershim` (as of v1.24) because maintaining a special-case adapter just for `dockerd` was extra maintenance burden, when Kubernetes could instead talk **directly to `containerd`** (or `CRI-O`) — which, remember from Module 5, is the *same* underlying runtime that `dockerd` itself uses internally.

**What did NOT change:** Docker images are built to the **OCI Image Spec** — a standardized image format, not a Docker-proprietary one. `containerd` and `CRI-O` both understand OCI images natively. So any image you build with `docker build` is fully runnable on Kubernetes — Kubernetes just no longer routes through `dockerd`'s API to launch it; it goes straight to `containerd`, cutting out a redundant middle layer.

**Analogy:** Imagine a courier company (Kubernetes) that used to call a shipping coordinator (`dockerd`) who then forwarded instructions to the actual warehouse (`containerd`) to move boxes (containers). The courier company realized they could just call the warehouse *directly* and skip the coordinator — the boxes (OCI images) themselves are exactly the same standard shape either way, so nothing about the boxes changed, only who's issuing the pickup instructions.

**Why this is a great interview signal:** Anyone who says "yes, you can't run Docker images on K8s anymore" reveals they only know Docker at the CLI-usage level. Anyone who explains the `dockershim`/`containerd`/OCI-image distinction shows they understand the actual architecture — this single question filters a lot of candidates at senior interviews.
