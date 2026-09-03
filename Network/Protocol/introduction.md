# Network Protocol

A network protocol is a set of rules that lets different devices talk to each other across a network.

Think of it like a common language or a set of polite conversation manners for computers.
If two people try to speak completely different languages without agreeing on how to take turns, they will not understand each other. Protocols make sure computers format data the same way, use the same addresses, and check for mistakes.

Think of them like a shared language and etiquette — they define how data is formatted, sent, received, and acknowledged, so that a phone, a laptop, and a server (even from different manufacturers, running different software) can all understand each other.


Common examples, grouped by what they do:

## Communication/transport
**TCP (Transmission Control Protocol)** — reliable, ordered delivery; used when you need every byte to arrive correctly (web pages, file transfers)
**UDP (User Datagram Protocol)** — fast but no guarantee of delivery; used for things like video calls or gaming where speed matters more than perfection

## Internet/addressing
**IP (Internet Protocol)** — handles addressing and routing so data finds its way to the right device

## Application-level
**HTTP/HTTPS** — how web browsers and servers exchange pages (HTTPS adds encryption)
**FTP** — file transfers

## SMTP/IMAP/POP3 — sending and receiving email
**DNS(Domain Name System)** — translates domain names (like google.com) into IP addresses

## Security
**SSL/TLS** — encrypts data in transit
**SSH** — secure remote login and command execution



Let's go through the major protocols in depth — how they work, why they exist, and what problem each one solves.

## TCP (Transmission Control Protocol)

**Problem it solves:** Raw IP just fires packets across the network with no guarantee they arrive, arrive in order, or arrive intact. Applications like web browsing or file transfer can't tolerate missing or scrambled data — you can't have half a webpage or a corrupted zip file.

**How it works:**
1. **Three-way handshake** — before any data moves, the two sides sync up: client sends `SYN`, server replies `SYN-ACK`, client replies `ACK`. This confirms both sides are ready and agrees on starting sequence numbers.
2. **Segmentation** — data is broken into segments, each tagged with a sequence number so the receiver can reorder them if they arrive out of sequence.
3. **Acknowledgments** — the receiver sends back `ACK`s confirming what it got. If the sender doesn't get an ACK within a timeout, it retransmits.
4. **Flow control** — the receiver advertises a "window size" (how much it can buffer), so a fast sender doesn't overwhelm a slow receiver.
5. **Congestion control** — TCP also backs off if it detects network congestion (e.g., via algorithms like slow start, congestion avoidance), to avoid flooding the network itself.
6. **Connection teardown** — a four-way handshake (`FIN`/`ACK` from each side) closes the connection cleanly.

**Why use it:** Guarantees reliable, ordered, error-checked delivery. The tradeoff is overhead — handshakes, acknowledgments, and retransmissions add latency.

## UDP (User Datagram Protocol)

**Problem it solves:** TCP's reliability machinery adds delay. Some applications (live video, voice calls, gaming) would rather drop a bit of data than wait for a retransmission — a dropped video frame is less noticeable than a frozen stream waiting for a resend.

**How it works:** UDP just sends packets ("datagrams") with a source port, destination port, length, and checksum — no handshake, no acknowledgment, no guaranteed order. It's "fire and forget." If a packet is lost, UDP itself does nothing about it; it's up to the application to handle that (or not bother).

**Why use it:** Minimal overhead, low latency. Used for DNS lookups (a quick question/answer that can just be retried if it fails), streaming media, VoIP, and online gaming.

## IP (Internet Protocol)

An IP address (Internet Protocol address) is a unique number assigned to any device that connects to a network so it can send and receive data

**Problem it solves:** How does data actually find its way from one specific machine, potentially anywhere in the world, to another? You need a universal addressing and routing scheme.

**How it works:**
- Every device gets an **IP address** (IPv4, like `192.168.1.1`, or IPv6 for a vastly larger address space).
- Data is broken into **packets**, each stamped with a source and destination IP address.
- Routers along the path look at the destination address and forward the packet toward it, hop by hop — no single router knows the whole path, just "which direction gets this closer."
- IP itself is **connectionless and unreliable** — it doesn't guarantee delivery, order, or integrity. That's why it's normally paired with TCP (for reliability) or left raw with UDP (for speed).

**Why use it:** It's the addressing/routing backbone of the entire internet — literally the "IP" in TCP/IP. Without it, there's no way to identify or reach a specific device among billions.

## HTTP / HTTPS

**Problem it solves:** Once you can reach a machine (via IP) and reliably transfer data (via TCP), you still need a shared "language" for requesting and serving web content — asking for a page, submitting a form, etc.

**How it works:**
1. Client opens a TCP connection to the server (usually port 80 for HTTP, 443 for HTTPS).
2. Client sends a **request**: a method (`GET`, `POST`, `PUT`, `DELETE`...), a path (`/users/5`), headers (metadata like content type, cookies, auth tokens), and optionally a body (e.g., form data or JSON).
3. Server processes it and sends a **response**: a status code (`200 OK`, `404 Not Found`, `500 Server Error`...), headers, and a body (HTML, JSON, etc.).
4. **HTTPS** adds a TLS layer underneath: before any HTTP data is exchanged, the client and server perform a TLS handshake to agree on encryption keys and verify the server's identity via a certificate. After that, all HTTP traffic is encrypted.
5. Modern HTTP (HTTP/2, HTTP/3) adds things like multiplexing multiple requests over one connection, header compression, and (in HTTP/3) running over UDP with its own reliability layer (QUIC) instead of TCP, to reduce latency further.

**Why use it:** It's the standard protocol for essentially all web traffic — every API call, every page load. HTTPS specifically solves the problem of anyone on the network path (say, on public wifi) being able to read or tamper with your data.

## DNS (Domain Name System)

**Problem it solves:** Humans want to type `google.com`, but machines route traffic using IP addresses. Something has to translate between the two.

**How it works:**
1. Your device asks a **resolver** (often run by your ISP or a public one like `8.8.8.8`) to look up a domain.
2. If the resolver doesn't already have it cached, it queries a **root server**, which points it to a **TLD server** (e.g., for `.com`), which points it to the **authoritative name server** for that specific domain.
3. That authoritative server returns the actual IP address.
4. The resolver caches the result (for a duration set by TTL) and returns it to your device.

Uses UDP by default (fast, since it's usually a single small query/response) but falls back to TCP for larger responses.

**Why use it:** Without DNS, you'd have to memorize IP addresses for every site you visit, and services couldn't change their underlying IP addresses (e.g., during infrastructure changes) without breaking everyone's bookmarks.

## SMTP / IMAP / POP3 (Email protocols)

**Problem it solves:** Sending and retrieving email needs its own protocols because the "send" and "read" flows are different from web browsing.

- **SMTP (Simple Mail Transfer Protocol)** — used to *send* mail from a client to a server, or between mail servers. It works like a relay: your mail client sends a message to your outgoing server, which looks up the recipient's domain via DNS (an `MX` record), then relays the message to the recipient's mail server.
- **IMAP (Internet Message Access Protocol)** — used to *read* mail while keeping it synced on the server. Your client sees the same mailbox state across multiple devices, since messages stay on the server.
- **POP3 (Post Office Protocol)** — an older way to retrieve mail that typically downloads it to a single device and removes it from the server (or optionally leaves a copy).

**Why use them:** Separation of concerns — sending, storing, and retrieving mail have different requirements, so each got its own protocol rather than overloading one.

## TLS/SSL

**Problem it solves:** Plain protocols like HTTP send data in cleartext — anyone intercepting the traffic (a coffee shop wifi snooper, an ISP, a malicious router) can read or modify it.

**How it works:**
1. **Handshake** — client and server agree on a TLS version and cipher suite, and the server presents a **certificate** (issued by a trusted Certificate Authority) proving its identity.
2. **Key exchange** — both sides derive a shared secret key (using asymmetric cryptography, e.g., Diffie-Hellman), without ever sending the key itself over the network.
3. **Symmetric encryption** — once the handshake is done, actual data is encrypted using that shared key (symmetric encryption is much faster than asymmetric for bulk data).
4. Every subsequent byte exchanged is encrypted and integrity-checked, so eavesdroppers see gibberish and tampering is detectable.

**Why use it:** It's what turns HTTP into HTTPS, and it underlies most secure protocols (secure email, secure database connections, etc.). It solves confidentiality (no one can read it), integrity (no one can silently alter it), and authentication (you know you're really talking to the server you think you are).

## SSH (Secure Shell)

**Problem it solves:** You need to remotely log into and run commands on another machine (e.g., a server) without exposing credentials or commands in plaintext.

**How it works:** Similar handshake/encryption concept to TLS — client and server negotiate encryption, the server proves its identity (often via a known host key), and the client authenticates (via password or, more commonly, a public/private key pair). Once established, all traffic — commands, output, even file transfers via SCP/SFTP — flows through this encrypted tunnel.

**Why use it:** Before SSH, protocols like Telnet sent everything, including passwords, in plaintext. SSH replaced that with a secure equivalent, and it's now also used to tunnel other traffic securely (e.g., port forwarding, Git operations over SSH).

---

If you want, I can also walk through how these actually stack together in a real request — e.g., what happens end-to-end when you type a URL into a browser (DNS → TCP handshake → TLS handshake → HTTP request/response) — since that ties most of these together in one flow.