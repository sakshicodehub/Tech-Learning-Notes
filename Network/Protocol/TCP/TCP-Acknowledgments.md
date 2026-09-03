
## TCP Acknowledgments (ACKs)

### What problem they solve

TCP promises reliable delivery — but the sender has no way of knowing whether data actually arrived unless the receiver tells it. **Acknowledgments are that feedback signal.** They let the sender know "this data got through safely," and by extension, tell it what to do if something *didn't* get through (retransmit it).

### The core mechanism: cumulative acknowledgment

TCP doesn't acknowledge segment-by-segment with something like "got segment 3." Instead, it uses **byte sequence numbers** and says: *"I have successfully received everything up to byte X, send me from X onward."*

This is called a **cumulative ACK**. The acknowledgment number in the TCP header means: *"the next byte I expect is this one"* — i.e., everything before it has arrived intact and in order.

**Example:**

```
Sender sends:
  Segment 1: seq=0,    1000 bytes (covers bytes 0–999)
  Segment 2: seq=1000, 1000 bytes (covers bytes 1000–1999)
  Segment 3: seq=2000, 1000 bytes (covers bytes 2000–2999)

Receiver gets all 3 in order, sends back:
  ACK: ack=3000   → "I've got everything through byte 2999, send me starting at 3000"
```

One ACK can confirm multiple segments at once — the receiver doesn't need to send three separate ACKs for three segments if they arrive together; a single ACK for the highest contiguous byte received covers all of them.

### What happens when something is missing

This is where cumulative ACKs get interesting. Say segment 2 gets lost in transit:

```
Sender sends:
  Segment 1: seq=0,    (bytes 0–999)      → arrives fine
  Segment 2: seq=1000, (bytes 1000–1999)  → LOST
  Segment 3: seq=2000, (bytes 2000–2999)  → arrives fine
```

The receiver got segment 1 and segment 3, but **not** segment 2. Since ACKs are cumulative (only acknowledge contiguous data), the receiver can't say "got 3000 bytes" — because there's a hole at byte 1000. So it keeps ACKing what it has fully and contiguously received:

```
Receiver → Sender: ACK ack=1000   (after segment 1)
Receiver → Sender: ACK ack=1000   (again, after segment 3 arrives — still stuck at 1000, since 2 is missing)
```

That repeated ACK for the same number is called a **duplicate ACK**. If the sender sees **three duplicate ACKs** in a row, it treats that as a strong signal that segment 2 was lost (not just delayed) and triggers **fast retransmit** — resending segment 2 immediately, without waiting for a timeout.

### Retransmission timeout (RTO) — the backup mechanism

If duplicate ACKs don't catch the loss (e.g., it was the very last segment, so there's nothing after it to trigger dup ACKs), TCP falls back to a timer. Every segment sent starts a **retransmission timer**, calculated from the connection's measured round-trip time (RTT). If no ACK arrives before the timer expires, TCP assumes the segment was lost and resends it — then doubles the timeout for the next attempt (exponential backoff), in case the network is congested rather than just glitchy.

### SACK (Selective Acknowledgment) — the smarter version

Plain cumulative ACK has a weakness: in the example above, once segment 2 is lost, the sender doesn't know segment 3 *did* arrive — it just knows "stuck at 1000." Without SACK, it might resend segment 3 unnecessarily.

**SACK** (negotiated during the handshake, now widely supported) fixes this by letting the receiver report non-contiguous received ranges explicitly:

```
Receiver → Sender: ACK ack=1000, SACK=[2000–2999]
```

This says: *"I'm still missing byte 1000 onward as my base, but I separately have 2000–2999 already — don't resend that part."* The sender then knows to retransmit **only** the actual gap (segment 2), which is much more efficient on lossy connections.

### Delayed ACKs — an efficiency trick

Sending an ACK for every single segment is wasteful, especially since ACKs themselves take up bandwidth. Most TCP implementations use **delayed ACK**: wait a short window (commonly up to ~200ms, or until 2 segments have arrived) to see if more data shows up, so one ACK can cover multiple segments — or even better, "piggyback" the ACK onto an outgoing data segment if the receiver is also sending data back (common in a two-way conversation like a database query/response).

### Flags vs. the acknowledgment number — a common confusion

These are two different things in the TCP header:
- The **ACK flag** (a single bit) — just says "the acknowledgment number field in this header is valid, pay attention to it." It's set on almost every segment after the initial SYN.
- The **acknowledgment number field** — the actual 32-bit value saying which byte is expected next.

So nearly every packet after the handshake has `ACK=1` and carries a meaningful ack number — acknowledgment isn't a separate standalone packet type most of the time, it rides along with regular data packets.

### Putting it together — a simple back-and-forth

```
Client → Server: SYN seq=0
Server → Client: SYN-ACK seq=0, ack=1        (ack=1: "I got your SYN, next byte I want is 1")
Client → Server: ACK ack=1                    (handshake complete)

Client → Server: seq=1,    "GET /page HTTP..." (100 bytes)
Server → Client: ACK ack=101                  ("got your 100 bytes, next expected is byte 101")

Server → Client: seq=1,    "HTML content..."  (1000 bytes)
Client → Server: ACK ack=1001                 ("got it all, expecting byte 1001 next")
```

### Why this design matters

- **Reliability**: nothing is considered delivered until acknowledged — lost data gets detected and resent automatically.
- **Efficiency**: cumulative ACKs mean one ACK can confirm many segments; delayed ACK avoids ACK-spam; SACK avoids unnecessary retransmission.
- **Self-correcting**: duplicate ACKs and RTO timers work together as two independent safety nets — fast detection when possible (dup ACKs), guaranteed detection eventually (timeout) even in the worst case.

This ACK feedback loop is also what feeds TCP's **congestion control** — every ACK (or lack of one) is a signal the sender uses to decide whether to speed up or back off, which is really the mechanism that lets TCP "sense" the health of the network in real time.

Cumulative TCP ACKs:

ACK number = the next byte the receiver expects.


## What does "cumulative" mean with example?

Now imagine the sender sends three segments:

Sender                         Receiver

SEQ=1000, 500 bytes  --------->

SEQ=1500, 500 bytes  --------->

SEQ=2000, 500 bytes  --------->

The receiver successfully receives all three.

It now has:

1000–2499

The next byte it needs is: 2500

So the receiver can send:

ACK = 2500

That one ACK effectively confirms everything up through byte 2499.

That's what cumulative acknowledgment means.

ACK=2500
      ↓
"I have received everything before byte 2500."


## What if one segment is lost?

This is where things become interesting.

Suppose:

Sender                         Receiver

SEQ=1000, 500 bytes  --------->

SEQ=1500, 500 bytes  --------->   ❌ LOST

SEQ=2000, 500 bytes  --------->

The receiver gets:

1000–1499

Then it doesn't get:

1500–1999   ❌

Then it receives:

2000–2499

But there's a problem.

The receiver is still missing:

1500–1999

So the next byte it wants is:

1500

Therefore, it sends:

ACK = 1500

Even though it received the later segment.


## TCP Retransmission Timeout (RTO)
It is essentially the amount of time TCP is willing to wait for an acknowledgment before deciding that something may have gone wrong.
0.2s → no ACK
0.4s → no ACK
0.6s → no ACK
0.8s → no ACK
1.0s → TIMEOUT


## Selective Acknowledgment: 
I am missing this part, but I already received these other parts.

With SACK enabled, the receiver can say something like:

    ACK = 1500

    SACK:
    2000–3000 received

Meaning:

"I still need 1500,
but I already received 2000 through 2999."

Now the sender knows exactly what is missing.

    1000–1499   ✅
    1500–1999   ❌ ← missing
    2000–2499   ✅
    2500–2999   ✅

So it can retransmit only the missing portion.

    SEQ=1500 --------------------> ✅