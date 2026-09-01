# OS & Networking Basics

## Processes vs Threads
- **Process:** isolated memory space; heavier (own address space, file handles).
- **Thread:** shares process memory; lighter; needs synchronization.
- **Concurrency** (overlap in time) != **Parallelism** (true simultaneous on cores).
- Synchronization: mutexes, semaphores, atomic ops. Deadlock needs 4 conditions (mutual exclusion, hold & wait, no preemption, circular wait) - break one to prevent.

## Memory (quick)
- **Stack:** call frames, local vars; fast, LIFO, limited size.
- **Heap:** dynamic allocations; flexible but slower + fragmentation.
- **Virtual memory / paging:** lets processes see more RAM than physical; page faults on miss.

## Networking - the stack
`Application (HTTP) -> Transport (TCP/UDP) -> Internet (IP) -> Link`

### TCP vs UDP
| | TCP | UDP |
|--|-----|-----|
| Guarantee | Reliable, ordered | Best-effort |
| Setup | 3-way handshake | None |
| Use | Web, email, DB | DNS, streaming, gaming, voice |
- **TCP 3-way handshake:** SYN -> SYN-ACK -> ACK. Teardown: FIN (4 packets).
- TCP gives reliability via sequence numbers + ACKs + retransmission; flow control (window); congestion control.

### HTTP / HTTPS
- **HTTP** is stateless request/response. Methods: GET, POST, PUT, DELETE, PATCH.
- Status codes: 2xx ok, 3xx redirect, 4xx client error (401 auth, 403 forbidden, 404 not found, 429 too many), 5xx server (502/503/504 gateway).
- **HTTPS = HTTP + TLS:** encrypts in transit; TLS handshake negotiates keys (asymmetric to exchange, then symmetric for speed).
- **DNS:** domain -> IP. Resolver chain: app cache -> OS -> local DNS -> root -> TLD -> authoritative. Cached via TTL.

### Caching layers (fast -> slow)
CPU caches (L1/L2/L3) -> RAM -> SSD/HDD -> network. Bigger & slower as you go down.

## Self-test
1. Walk through the TCP handshake and why it's 3 (not 2) packets.
2. TCP vs UDP - pick one for a video call and justify.
3. What does DNS do, in order, when you type a URL?
