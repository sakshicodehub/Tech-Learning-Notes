
## Module 2: Dockerfile, Build Process & Layer Caching

### The Dockerfile — the recipe

A Dockerfile is a plain text file of instructions. Example:

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```

Each line becomes a **layer** in the image (roughly — some instructions like `ENV`, `CMD`, `EXPOSE` just add metadata, not filesystem layers; `RUN`, `COPY`, `ADD` create actual filesystem layers).

**Analogy:** Think of building the image like baking a layered cake, where each layer is poured and set (cached) before the next one goes on. If you only change the topmost layer, you don't need to re-bake the whole cake — just redo that last layer.

### Layer Caching — the interview-critical part

Docker caches each layer. When you rebuild, Docker checks: *"has this instruction, and everything before it, stayed exactly the same?"* If yes, it reuses the cached layer instead of re-executing it. This is why **instruction order in a Dockerfile matters enormously** for build speed.

Look at the example above again:

```dockerfile
COPY package*.json ./
RUN npm install
COPY . .
```

Why copy `package.json` *separately* before copying the rest of the code? Because `npm install` is slow. If you did `COPY . .` first, then `RUN npm install`, then **any code change** (even a one-line change in `server.js`) invalidates the cache from that point onward — forcing `npm install` to rerun every single build, even though your dependencies didn't change.

**Analogy:** Imagine packing a suitcase for a trip. If you fold your clothes fresh every single day (npm install) just because you added one new sock (a code change) to a *different* pocket, that's wasteful. Instead, pack the clothes once (install deps), and only repack the pocket that actually changed (your app code).

**Rule of thumb for Dockerfile ordering:**
1. Things that change *least often* go at the top (base image, system deps)
2. Things that change *most often* go at the bottom (your actual application code)

### Multi-stage builds — reduces final image size

A very common FAANG-adjacent interview topic: *"How do you keep production images small?"*

```dockerfile
# Stage 1: build
FROM golang:1.21 AS builder
WORKDIR /app
COPY . .
RUN go build -o myapp

# Stage 2: run
FROM alpine:latest
COPY --from=builder /app/myapp /myapp
CMD ["/myapp"]
```

**Analogy:** Stage 1 is like a construction site full of tools, scaffolding, cranes (compilers, build tools) needed to build a house. Stage 2 is the finished house — you don't ship the cranes and scaffolding to the customer, you only ship the final house. Multi-stage builds let you use a heavy image to *build*, then copy only the final artifact into a tiny image (like `alpine`, ~5MB) to *ship*. This can shrink image size from 1GB+ down to 15-20MB for compiled languages.

### Interview Question (this is a real one asked at scale-oriented companies)

**Q: You have a Dockerfile where `RUN apt-get update && apt-get install -y curl` is on its own line, separate from other apt installs elsewhere in the file. A teammate says this is bad practice and you should combine RUN commands. Why might they be right, on two separate grounds — one about caching, one about image size?**


Answer: Why combining RUN commands matters

**Ground 1 — Caching correctness (the dangerous one):**

```dockerfile
RUN apt-get update
RUN apt-get install -y curl
```

If these are **separate layers**, and later you add `RUN apt-get install -y wget` weeks later, Docker sees the `apt-get update` layer is unchanged and **reuses the cache** — meaning it skips re-running `apt-get update`. But your package index might now be stale (pointing to old package versions/URLs that may no longer exist), so `apt-get install -y wget` can fail or silently install an outdated/vulnerable version.

**Fix:** always chain them in the same `RUN` so they're atomic and re-run together:
```dockerfile
RUN apt-get update && apt-get install -y curl wget
```
This is such a common gotcha that Docker's own official best-practices docs call it out explicitly.

**Ground 2 — Image size (the wasteful one):**

Every `RUN` creates a new layer, and **layers only ever get added, never truly "shrunk"** in the final image — even if a later layer deletes files, the earlier layer's data still exists in the image history (this trips people up a lot). So:

```dockerfile
RUN apt-get update
RUN apt-get install -y curl
RUN rm -rf /var/lib/apt/lists/*
```

...doesn't actually save space in the final image, because the `apt-get install` layer already baked in the cache files, and the `rm` layer just *adds a new layer* recording that those files are hidden. The disk usage from the install layer is still there underneath.

**Fix:** clean up in the *same* `RUN` command, so the cleanup happens before the layer is sealed:
```dockerfile
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
```

**Analogy:** Think of each layer as a sealed box you ship in a moving truck. If you pack a box, seal it, then open a *new* box just to write "ignore the stuff in box 1" — box 1's weight is still in the truck. You have to throw away the trash *before* sealing the box, not after.

This "layers are append-only, deletions don't shrink history" fact is a favorite Docker interview trap — good to have it locked in.
