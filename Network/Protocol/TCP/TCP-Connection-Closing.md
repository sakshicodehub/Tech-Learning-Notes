## TCP Connection Closing

### When closing happens

A TCP connection closes when either side decides it's done sending data — the application calls something like `close()` on the socket (e.g., a browser finished receiving a page, a server finished sending a response, or an idle connection is being cleaned up). Either side can initiate closing; TCP connections are symmetric, so there's no "only the client can close" rule.

There are two main ways a connection ends: a **graceful close** (FIN-based, normal case) and an **abrupt close** (RST-based, error/abort case). Let's go through both.

## Graceful close — the four-way handshake

Because TCP connections are **full-duplex** (data flows independently in both directions), closing isn't a single instant — each direction has to be shut down separately. That's why it takes four steps instead of the three used to open a connection.

**Step by step:**

```
Client → Server: FIN (seq=x)         "I have no more data to send"
Server → Client: ACK (ack=x+1)       "Got it, acknowledged"
   ... server can still send data here if it wants ...
Server → Client: FIN (seq=y)         "I'm also done sending"
Client → Server: ACK (ack=y+1)       "Confirmed, connection closed"
```

Notice the FIN and ACK from the server aren't necessarily sent together — the server might still have data left to send after receiving the client's FIN. This is called a **half-close**: the client has said "I'm done talking," but the server can keep sending data until it's also ready to close. Only once both sides have sent and acknowledged a FIN is the connection fully torn down.

**Why four steps and not fewer:** each side needs to independently declare "I'm done" and get that acknowledged, because either side might still have unsent data in flight when the other side wants to stop.

### The TIME_WAIT state

After the side that sends the *final* ACK (usually the one that initiated the close) finishes the handshake, it doesn't immediately discard the connection — it enters a state called **TIME_WAIT** and holds onto that connection's info for a period (traditionally **2×MSL**, Maximum Segment Lifetime, often ~60 seconds, though it varies by OS).

**Why this wait exists:**
1. **In case the final ACK gets lost.** If the other side doesn't receive that last ACK, it will retransmit its FIN. The side in TIME_WAIT needs to still be "listening" so it can resend the ACK instead of the peer being stuck waiting forever.
2. **To let old, delayed packets from this connection die out on the network**, so they can't accidentally be mistaken for part of a *new* connection that happens to reuse the same IP/port pair.

This is why you'll sometimes see a server hold many connections in `TIME_WAIT` state (visible via `netstat`) right after a period of heavy short-lived connections — it's expected, not a bug, though it can become a resource concern under very high connection churn (a common reason people tune OS-level TCP settings or use connection pooling/keep-alive).

### Full state sequence (for the closing side)

```
ESTABLISHED
   ↓ (send FIN)
FIN_WAIT_1
   ↓ (receive ACK)
FIN_WAIT_2
   ↓ (receive peer's FIN, send ACK)
TIME_WAIT
   ↓ (wait ~2×MSL)
CLOSED
```

And for the side receiving the close first:

```
ESTABLISHED
   ↓ (receive FIN, send ACK)
CLOSE_WAIT     ← app can still send remaining data here
   ↓ (app finishes, sends its own FIN)
LAST_ACK
   ↓ (receive final ACK)
CLOSED
```

## Abrupt close — RST (Reset)

Sometimes a connection doesn't close gracefully — instead it gets terminated immediately via a **RST** flag. This happens when:
- An application crashes or is killed abruptly, leaving no chance to run the normal close sequence
- Data arrives for a connection the receiver doesn't recognize (e.g., after a crash and restart, or a port that's no longer listening)
- One side wants to forcibly abort the connection (e.g., a firewall blocking traffic, or an app explicitly aborting rather than closing cleanly)
- A serious error occurs, like receiving data that violates the expected sequence badly enough that recovery isn't attempted

**How it works:** a single segment with the RST flag set is sent, and the connection is torn down **immediately** on both sides — no handshake, no acknowledgment expected, no TIME_WAIT. Any unacknowledged or in-flight data is simply discarded. It's the TCP equivalent of hanging up the phone mid-sentence rather than saying goodbye.

**Why it matters:** RST is faster but "unsafe" — the two sides can end up with different views of what data was actually delivered, since there's no confirmation step. That's fine for aborting a broken connection, but it's not used for normal, planned closes.

## Quick comparison

| | FIN (graceful) | RST (abrupt) |
|---|---|---|
| Purpose | Normal, planned close | Error / forced abort |
| Confirms both sides done | Yes (via ACKs) | No |
| Data in flight | Delivered/acknowledged before close completes | Discarded immediately |
| Resource cleanup | Enters TIME_WAIT first | Immediate |
| Typical trigger | App calls `close()` normally | Crash, invalid segment, forced abort |

## Practical relevance (e.g., in your Rails/Sidekiq work)

This matters more than it might seem day-to-day:
- **Connection pools** (like ActiveRecord's DB connection pool, or HTTP client pools) try to reuse TCP connections specifically to avoid paying the cost of a new handshake *and* a new close/TIME_WAIT cycle for every request.
- If you've ever seen a server error like `connection reset by peer`, that's literally an RST arriving — often because the other end (a load balancer, proxy, or the remote server) closed or timed out the connection abruptly rather than gracefully.
- Long-lived connections (like Sidekiq's Redis connection, or a persistent HTTP keep-alive connection) deliberately avoid this whole open/close cycle per request/job, which is a big part of why they're faster than opening a fresh TCP connection every time.