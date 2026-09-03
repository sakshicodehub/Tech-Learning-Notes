## What is a TCP Segment?

A **segment** is the unit of data that TCP sends at a time — think of it as an envelope: a chunk of your actual data (the "payload") wrapped in a **TCP header** that carries all the control information TCP needs to do its job (sequencing, acknowledgment, error checking, etc.).

So when people say "TCP breaks data into segments," they mean: your file, HTTP response, or whatever byte stream you're sending gets sliced into pieces, and each piece gets its own header stuck on the front before being handed down to IP for delivery.

## What exactly is in a segment (the TCP header)

A TCP header is typically 20 bytes (can be more with options). Here's what's in it and why:

| Field | Size | Purpose |
|---|---|---|
| **Source port** | 16 bits | Which application/process sent it (e.g., 51000) |
| **Destination port** | 16 bits | Which application/process it's going to (e.g., 443 for HTTPS) |
| **Sequence number** | 32 bits | The position of the *first byte* of this segment's payload within the overall stream |
| **Acknowledgment number** | 32 bits | "I've received everything up to this byte, send me the next one" |
| **Data offset** | 4 bits | Where the header ends and the actual data begins |
| **Flags** (SYN, ACK, FIN, RST, PSH, URG) | few bits each | Control signals — SYN to start a connection, FIN to close it, ACK to acknowledge, RST to abruptly reset, PSH to push data to the app immediately |
| **Window size** | 16 bits | How many bytes the sender is willing to receive right now (flow control) |
| **Checksum** | 16 bits | Error-detection — lets the receiver verify the segment wasn't corrupted in transit |
| **Urgent pointer** | 16 bits | Marks urgent data, rarely used today |
| **Options** (optional) | variable | Extra features like SACK (selective ack), timestamps, window scaling |

Then after the header comes the **payload** — the actual slice of your data.

So a segment literally looks like:

```
[ TCP HEADER (20+ bytes) ][ DATA PAYLOAD (up to ~1460 bytes typically) ]
```

## How a file actually gets divided into segments

Here's the key concept: **MSS (Maximum Segment Size)**.

1. **The network has a size limit per packet.** Ethernet, the most common physical network, has a default **MTU (Maximum Transmission Unit)** of 1500 bytes — that's the biggest chunk that can travel as one frame at the link layer.

2. **Subtract the overhead.** Out of that 1500 bytes:
   - IP header takes 20 bytes
   - TCP header takes 20 bytes
   - That leaves **1460 bytes** for actual data — this is the **MSS**.

3. **During the handshake, both sides announce their MSS** (the max segment size they can accept), and TCP picks the smaller of the two. So if your file is, say, 10,000 bytes, TCP will slice it into segments of up to 1460 bytes of payload each:

```
File: 10,000 bytes total

Segment 1: seq=0     → bytes 0–1459      (1460 bytes)
Segment 2: seq=1460  → bytes 1460–2919   (1460 bytes)
Segment 3: seq=2920  → bytes 2920–4379   (1460 bytes)
...
Segment 7: seq=8760  → bytes 8760–9999   (1240 bytes, the remainder)
```

Each segment's **sequence number** marks where its payload starts in the overall byte stream — that's literally how the receiver reassembles the file in the right order, even if segments arrive late or out of sequence.

4. **This whole segment then goes down the stack:**
```
Application data
   ↓
TCP adds TCP header  → Segment
   ↓
IP adds IP header     → Packet (a.k.a. datagram)
   ↓
Link layer adds frame header/trailer → Frame
   ↓
Sent over the physical network
```

So a "segment" becomes a "packet" once IP wraps it, and a "frame" once the link layer (like Ethernet) wraps that. People use these terms loosely, but technically each layer has its own name for "data + that layer's header."

## Why 1460 and not something bigger?

If TCP tried to send bigger chunks than the network's MTU allows, the packet would have to be **fragmented** at the IP layer (broken into smaller pieces by a router), which is inefficient and can cause problems (fragments can get lost independently, and reassembly adds overhead). By keeping segments at or under the MSS, TCP avoids fragmentation entirely — each segment fits into exactly one link-layer frame.

## Quick concrete example

Say you're downloading a 5 KB JSON API response over TCP:

- MSS negotiated as 1460 bytes
- 5000 bytes ÷ 1460 ≈ **4 segments** (3 full + 1 partial)
- Each segment gets sequence numbers, so segment 2 might say "seq=1460" meaning "my payload starts at byte 1460 of this stream"
- Receiver's OS buffers incoming segments, reorders by sequence number if needed, strips the TCP headers, and hands the app a clean, ordered byte stream — the app never even sees individual segments, just the reassembled data.