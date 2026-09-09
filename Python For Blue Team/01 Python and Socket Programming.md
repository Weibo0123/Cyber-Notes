# Python and Socket Programming

## The `socket` Library

Python's `socket` module creates and manages network sockets for both client and server roles:
```python
import socket
socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```
The two arguments — **address family** and **socket type** — determine the kind of communication the socket supports.

## Creating a TCP Server

```python
import socket

HOST, PORT = "127.0.0.1", 65432

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.bind((HOST, PORT))
    s.listen()
    conn, addr = s.accept()

    with conn:
        print("Connected:", addr)
        conn.sendall(b"Hello, socket!")
```
| Step | Purpose |
|---|---|
| `socket()` | Create the socket |
| `bind()` | Associate it with an IP + port |
| `listen()` | Start listening for incoming TCP connections |
| `accept()` | Block until a client connects, then return a connection object + address |
| `sendall()` | Send data reliably to the connected client |

## Address Families (1st Argument)

| Family | Meaning |
|---|---|
| `AF_INET` | IPv4 |
| `AF_INET6` | IPv6 |
| `AF_UNIX` | Local IPC via filesystem paths |
| `AF_NETLINK` | Linux kernel ↔ user-space communication |
| `AF_IPX` / `AF_X25` / `AF_APPLETALK` | Legacy protocols |

Support for some families depends on the OS and Python build.

## Socket Types (2nd Argument)

| Type | Protocol/Behavior | Common Uses |
|---|---|---|
| `SOCK_STREAM` | Connection-oriented byte stream (TCP) | Web servers, databases, FTP |
| `SOCK_DGRAM` | Connectionless datagrams (UDP) | Streaming, games, fast transfer |
| `SOCK_RAW` | Low-level packet access, bypassing normal transport-layer handling | Packet capture, custom protocols, IDS tools (file 04) |
| `SOCK_RDM` | Reliable datagrams | Specialized use cases |
| `SOCK_SEQPACKET` | Reliable, connection-oriented, message-boundary-preserving | Message-based services |

## UDP Sockets

```python
import socket

HOST, PORT = "127.0.0.1", 65432

with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as s:
    s.bind((HOST, PORT))
    data, addr = s.recvfrom(1024)
    s.sendto(b"Hello from UDP socket!", addr)
    print("Received from client:", data.decode())
```
Key differences from TCP:
- No connection is established before sending — there's no `listen()`/`accept()` handshake.
- `recvfrom()` returns both the data **and** the sender's address (since there's no persistent connection to track it).
- `sendto()` sends to a specific address per call.
- No delivery or ordering guarantee.

⚠️ **Telnet only speaks TCP** — it can't be used to poke a UDP socket. Use `nc` (netcat), which supports both.

## Key Socket Methods

```
socket()                create a socket
bind()                    assign IP + port
listen()                    listen for TCP connections
accept()                      accept a TCP connection
recv() / recvfrom()             receive data (recvfrom also gives sender address — UDP)
send() / sendall()                 send data (TCP)
sendto()                             send data to a specific address (UDP)
```

This is the foundation for the applied tools built in the rest of this notebook: a port scanner (file 02) and a raw-socket-based IDS (file 04) both build directly on these primitives.

## Cheat Sheet

```
socket.socket(family, type)

AF_INET (IPv4)  AF_INET6 (IPv6)  AF_UNIX (local IPC)

SOCK_STREAM (TCP, connection-oriented)   SOCK_DGRAM (UDP, connectionless)   SOCK_RAW (raw packets)

TCP server flow: socket() → bind() → listen() → accept() → sendall()/recv()
UDP flow:          socket() → bind() → recvfrom()/sendto()  (no accept/listen)

Telnet = TCP only.  nc (netcat) = TCP and UDP.
```
