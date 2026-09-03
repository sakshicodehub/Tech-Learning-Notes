## TCP (Transmission Control Protocol)

### Why it exists — the problem it solves

The internet's base layer, IP, only promises to try to get a packet from A to B. It doesn't guarantee:
- the packet arrives at all
- it arrives only once (not duplicated)
- it arrives in the order it was sent
- its contents aren't corrupted
- the sender doesn't overwhelm the receiver or the network

For a lot of things — streaming, gaming — that's fine. But for a file download, an API call, a database connection, or literally any web page load, losing a chunk of data or getting it out of order would silently break things. TCP was built to sit on top of IP and turn that "best effort, no guarantees" delivery into a **reliable, ordered, error-checked stream** of bytes between two endpoints.

### How it works, step by step

**1. Establishing a connection — the three-way handshake**

Before any actual data flows, both sides sync up:

```
Client → Server:  SYN (seq=x)          "I want to talk, here's my starting sequence number"
Server → Client:  SYN-ACK (seq=y, ack=x+1)   "Got it, here's mine, and I acknowledge yours"
Client → Server:  ACK (ack=y+1)        "Confirmed, let's go"
```

This confirms both sides are alive, reachable, and agree on where the "byte counting" starts. This is also why TCP is called **connection-oriented** — unlike UDP, there's a defined session with a beginning and end.

**2. Breaking data into segments**

The application's data stream (say, an HTTP response) is split into **segments**, each with:
- a **sequence number** (which byte range this segment covers)
- source/destination **ports** (which identify which application on each machine)
- a **checksum** (to detect corruption)

Sequence numbers are the key trick — they let the receiver reconstruct the original byte order even if segments arrive out of order or get duplicated over the network.

**3. Acknowledgments and retransmission**

For each segment (or group of segments) received, the receiver sends back an **ACK** referencing the next expected sequence number. If the sender doesn't get an ACK within a timeout, it assumes the segment was lost and **retransmits** it. This is the core reliability mechanism — nothing is considered "delivered" until it's acknowledged.

Modern TCP also uses **selective acknowledgment (SACK)**, so the receiver can say "I got segments 1, 2, and 4, but not 3" — letting the sender resend just the missing piece instead of everything after it.

**4. Flow control — don't overwhelm the receiver**

The receiver advertises a **window size**: how many bytes it can currently buffer. The sender won't send more than that before getting an ACK freeing up space. This stops a fast sender from flooding a slow receiver (e.g., a phone on a weak connection).

**5. Congestion control — don't overwhelm the network**

Separately, TCP also protects the network itself. It starts sending slowly (**slow start**), increasing its send rate as ACKs confirm segments are getting through, and backs off sharply if it detects packet loss (a sign of congestion). This is why file transfers "ramp up" speed at the start rather than blasting at full speed immediately.

**6. Closing the connection**

A **four-way handshake** tears things down cleanly:
```
Client → Server: FIN   "I'm done sending"
Server → Client: ACK
Server → Client: FIN   "I'm done too"
Client → Server: ACK
```
Each side confirms it's finished before the connection is fully released, so no data in flight gets silently dropped.

### The tradeoff

All of this — handshakes, sequence tracking, ACKs, retransmission, congestion control — adds **latency and overhead** compared to just firing packets (UDP). That's the price for reliability: TCP guarantees your data arrives complete and in order, at the cost of speed and some extra round trips, especially on high-latency or lossy networks (this is part of why HTTP/3 moved to QUIC/UDP — to avoid some of TCP's handshake overhead for web traffic).

### Where you'd run into it day to day

Any time you open a database connection, hit an HTTP endpoint, or SSH into a server, that's TCP underneath, silently handling the handshake, retransmissions, and ordering so your application code never has to think about lost or scrambled packets.


Questions
what is segment what exact it have and how devide file in segment?
what is TCP-Acknowledgments and how work?
how and when closing work?