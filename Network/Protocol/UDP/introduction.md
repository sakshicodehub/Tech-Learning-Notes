## UDP (User Datagram Protocol)

### Why it exists — the problem it solves

TCP's reliability comes at a cost: handshakes, acknowledgments, retransmissions, ordering, congestion control — all of that adds **latency and overhead**. For some applications, that tradeoff is wrong. If you're on a live video call and a frame gets lost, you don't want TCP pausing everything to retransmit that one frame — by the time it arrives, it's already useless; you'd rather just skip it and keep going with the next one.

UDP exists for exactly this case: applications that want to send data **fast**, with minimal overhead, and are willing to handle (or simply tolerate) loss, duplication, or reordering themselves — or not handle it at all.

### Core design: "fire and forget"

UDP is **connectionless** — there's no handshake, no persistent session state, no negotiation. The sender just packages data and sends it. That's it.

```
No SYN, no ACK, no connection setup.
Sender just sends → receiver either gets it or doesn't.
```

Because of this, UDP is often called **unreliable**, but that's a slightly misleading term — it doesn't mean "bad," it means "makes no promises." No guarantee of:
- delivery (packets can be silently dropped)
- order (packets can arrive out of sequence)
- no duplication (a packet could theoretically arrive twice)
- flow/congestion control (no automatic slowing down under load)

### The UDP header — much simpler than TCP

UDP header is only **8 bytes**, versus TCP's 20+:

| Field | Size | Purpose |
|---|---|---|
| **Source port** | 16 bits | Which app/process sent it |
| **Destination port** | 16 bits | Which app/process it's going to |
| **Length** | 16 bits | Total size of header + data |
| **Checksum** | 16 bits | Optional integrity check (can even be all-zeros/disabled in IPv4) |

That's it. No sequence number, no acknowledgment number, no window size, no flags. Each UDP packet (called a **datagram**) is a completely independent unit — the protocol has no concept of "this is part of a stream," unlike TCP where everything is one continuous byte sequence.

### How sending actually works

```
Application data (e.g., a DNS query)
   ↓
UDP wraps it with an 8-byte header  → Datagram
   ↓
IP wraps that with an IP header      → Packet
   ↓
Link layer wraps that                → Frame
   ↓
Sent over the network
```

The receiver's OS delivers each datagram to the application based on destination port — no reassembly needed because each datagram is already a complete, self-contained message the application can act on immediately.

**Example — a DNS query:**
```
Client → Server: UDP packet, dst port 53, payload = "resolve example.com"
Server → Client: UDP packet, src port 53, payload = "93.184.216.34"
```
One request, one response, done. No handshake before it, no teardown after. If the response doesn't come back in time, the application (or the OS resolver) just resends the query — the *application* decides to retry, not the protocol itself.

### What happens when a packet is lost?

Nothing — automatically, that is. UDP itself doesn't notice or care. It's entirely up to the application layer to decide:
- **Ignore it** (fine for a lost video/audio frame — the next one is coming in 33ms anyway)
- **Detect and request it manually** (some custom protocols built on UDP implement their own lightweight retry logic)
- **Rebuild reliability entirely on top of UDP** (this is what **QUIC**, used by HTTP/3, does — it adds its own sequencing, acknowledgment, and encryption on top of UDP, getting TCP-like reliability but with fewer round trips and no head-of-line blocking)

### Why use it — the real tradeoffs

| | UDP | TCP |
|---|---|---|
| Setup cost | None — send immediately | Handshake required first |
| Header size | 8 bytes | 20+ bytes |
| Ordering | Not guaranteed | Guaranteed |
| Delivery guarantee | None | Guaranteed (via retransmission) |
| Speed | Faster, lower latency | Slower due to reliability overhead |
| Congestion control | None built-in | Built-in |
| Best for | Speed-sensitive, loss-tolerant data | Data integrity-critical transfers |

### Common real-world uses

- **DNS** — quick request/response; if it fails, just retry the whole query, cheaper than TCP's handshake overhead for such a tiny exchange
- **VoIP / video calls** (Zoom, WebRTC) — a late packet is worse than a lost one; better to skip and keep the stream live
- **Online gaming** — player position updates arrive dozens of times per second; an old stale one arriving late is actively wrong to use, so no point retransmitting it
- **Live streaming / broadcast** — similar reasoning, real-time matters more than completeness
- **DHCP** — assigning IP addresses on a local network, before a device even has full connectivity set up
- **QUIC / HTTP/3** — modern web protocol that rebuilds TCP-like reliability *on top of* UDP, to avoid some of TCP's inherent handshake/head-of-line-blocking latency

### A useful mental model

TCP is like a **phone call**: you dial, wait for pickup, confirm you can hear each other, talk with the guarantee everything you say arrives in order, then formally hang up.

UDP is like sending a **postcard**: you write it, drop it in the mailbox, and hope it arrives. No confirmation, no guaranteed order if you send several, and if it gets lost, you won't even know unless you follow up yourself.

### Practical relevance to your stack

- If you use **Redis** for Sidekiq — that's TCP, since job data integrity matters.
- If your Rails app does any **DNS lookups** (external API calls, etc.) — those resolve over UDP under the hood, which is part of why DNS lookups are usually fast, but occasionally you'll see a timeout/retry if a UDP query gets dropped.
- If you're ever building or integrating **real-time features** (websockets for live updates, video/audio, live dashboards) — that's where UDP-based approaches (WebRTC, QUIC) tend to show up, as opposed to your typical HTTP/TCP request-response API work.