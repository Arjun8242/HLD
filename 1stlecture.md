# High Level Design (HLD) — Lecture 1

# Network Protocols

Network protocols are rules that define how devices communicate over a network.

They determine:
- How data is transmitted
- How connections are established
- How systems exchange information
- How packets are handled

---

# Important Layers

Main focus:
1. Application Layer
2. Transport Layer

---

# Application Layer

The Application Layer contains protocols directly used by applications.

It has 2 major categories:

1. Client-Server Protocols
2. Peer-to-Peer Protocols

---

# 1. Client-Server Model

In this model:

- Client sends request
- Server processes request
- Server sends response

Example:
- Browser = Client
- Web Server = Server

## Diagram

```text
Client -----------------> Server
        Request

Client <----------------- Server
        Response
```

---

# Client-Server Protocols

## HTTP (HyperText Transfer Protocol)

### Features
- Used for websites and APIs
- Request-response based
- Connection oriented
- Runs on TCP

### Example
- Opening websites
- REST APIs

---

## FTP (File Transfer Protocol)

### Features
- Used for file transfer
- Uses 2 connections:
  1. Control connection
  2. Data connection
- Not encrypted by default

### Example
- Uploading files to a server

---

## SMTP (Simple Mail Transfer Protocol)

### Features
- Used to send emails

### Example
- Gmail sending emails

---

## IMAP (Internet Message Access Protocol)

### Features
- Used to receive emails
- Emails remain stored on server
- Multiple device sync supported

### Example
- Accessing same email from mobile and laptop

---

## POP (Post Office Protocol)

### Features
- Used to receive emails
- Downloads emails locally
- May remove emails from server

---

# WebSockets

WebSockets provide:
- Persistent connection
- Full duplex communication
- Real-time communication

### Examples
- Chat applications
- Live notifications
- Multiplayer games

## Important Point
WebSockets are NOT peer-to-peer.

Communication still happens through a server.

## Diagram

```text
Client 1  <------>  Server  <------>  Client 2
```

---

# 2. Peer-to-Peer (P2P) Model

In Peer-to-Peer communication:
- Clients communicate directly
- No central server needed for actual communication

---

# WebRTC

WebRTC enables:
- Real-time communication
- Direct client-to-client communication

### Used In
- Video calls
- Audio calls
- Screen sharing

---

# Features of WebRTC

- Peer-to-peer communication
- Low latency
- Fast communication
- Supports:
  - Audio
  - Video
  - Data transfer

---

# WebRTC Diagram

```text
Client 1  <----------------->  Client 2
```

---

# Why WebRTC is Fast

- Direct communication
- Less server dependency
- Lower latency

---

# Transport Layer

Transport layer handles:
- Data delivery
- Reliability
- Packet ordering
- Error handling

Main protocols:
1. TCP/IP
2. UDP/IP

---

# TCP (Transmission Control Protocol)

## Features

- Connection oriented
- Reliable
- Packet ordering maintained
- Error checking available

---

# TCP Communication

```text
Sender  -------- Packet 1 --------> Receiver
Sender  <--------- ACK ------------ Receiver

Sender  -------- Packet 2 --------> Receiver
Sender  <--------- ACK ------------ Receiver
```

---

# TCP Characteristics

- Reliable communication
- Slower than UDP
- Lost packets are retransmitted

### Used In
- HTTP
- FTP
- SMTP

---

# UDP (User Datagram Protocol)

## Features

- Faster communication
- No connection setup
- No packet ordering
- No retransmission

---

# UDP Communication

```text
Sender  -----------------> Receiver
```

---

# UDP Characteristics

- Faster than TCP
- Packet loss possible
- Used where speed matters more than reliability

### Used In
- Video streaming
- Online gaming
- WebRTC
- Live calls

---

# TCP vs UDP

| Feature | TCP | UDP |
|---|---|---|
| Connection Type | Connection-oriented | Connectionless |
| Reliability | Reliable | Unreliable |
| Packet Ordering | Maintained | Not maintained |
| Speed | Slower | Faster |
| Error Recovery | Yes | No |
| Use Cases | HTTP, FTP, SMTP | Gaming, Streaming, WebRTC |

---

# Client-Server vs Peer-to-Peer

| Feature | Client-Server | Peer-to-Peer |
|---|---|---|
| Communication | Through server | Direct |
| Centralized | Yes | No |
| Example | HTTP, WebSocket | WebRTC |

---

# Quick Revision

## Application Layer

### Client-Server Protocols
- HTTP
- FTP
- SMTP
- WebSockets

### Peer-to-Peer Protocols
- WebRTC

---

## Transport Layer
- TCP/IP
- UDP/IP

---

# Important Interview Points

- HTTP works over TCP
- WebSockets are not peer-to-peer
- WebRTC supports peer-to-peer communication
- TCP guarantees reliability and ordering
- UDP is faster but unreliable
- FTP uses 2 connections
- SMTP sends mail
- IMAP and POP receive mail