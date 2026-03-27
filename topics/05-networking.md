# 5. Networking

---

## TCP vs UDP

Both are transport layer protocols for sending data over the internet.

### TCP (Transmission Control Protocol)
- **Reliable**, ordered delivery with error checking
- Uses a **3-way handshake** to establish connection (SYN → SYN-ACK → ACK)
- Retransmits lost packets
- Slower but guaranteed delivery
- Use cases: HTTP, email, file transfer, database connections

### UDP (User Datagram Protocol)
- **Fast**, no connection setup, no guarantee of delivery
- No handshake, no retransmission
- Packets can arrive out of order or get lost
- Use cases: Video streaming, gaming, DNS, VoIP

### Comparison

| Aspect | TCP | UDP |
|--------|-----|-----|
| Connection | Connection-oriented (handshake) | Connectionless |
| Reliability | Guaranteed delivery | No guarantee |
| Order | Ordered | No ordering |
| Speed | Slower | Faster |
| Overhead | Higher (headers, retransmission) | Lower |
| Example | Loading a webpage | Live video call |

**Analogy:** TCP = registered mail (tracked, confirmed delivery). UDP = throwing flyers from a rooftop (fast, no confirmation).

---

## HTTP vs HTTPS

### HTTP (HyperText Transfer Protocol)
- Communication between client and server in **plain text**
- Anyone intercepting can read the data
- Port: 80

### HTTPS (HTTP Secure)
- HTTP + **TLS/SSL encryption**
- Data is encrypted in transit — interceptors see gibberish
- Port: 443
- Uses certificates to verify server identity

```
HTTP:  Client ---[plain text "password123"]--> Server
HTTPS: Client ---[encrypted "a8f3k2..."]----> Server (decrypts to "password123")
```

### TLS Handshake (simplified)
```
1. Client: "Hello, I support these encryption methods"
2. Server: "Let's use this method. Here's my certificate."
3. Client: Verifies certificate, generates session key, sends it encrypted
4. Both: Use session key for encrypted communication
```

---

## WebSockets

A **persistent, full-duplex** connection between client and server.

### How it differs from HTTP

| Aspect | HTTP | WebSocket |
|--------|------|-----------|
| Connection | New connection per request | Single persistent connection |
| Direction | Client → Server (request-response) | Both ways simultaneously |
| Overhead | Headers sent every time | Lightweight after handshake |
| Real-time | No (need polling) | Yes |

### How it works
```
1. Client sends HTTP request with "Upgrade: websocket" header
2. Server agrees → connection upgraded to WebSocket
3. Both sides can send messages anytime
4. Connection stays open until explicitly closed
```

### Use cases
- Chat apps (WhatsApp Web)
- Live notifications
- Real-time dashboards (stock prices)
- Collaborative editing (Google Docs)

---

## WebRTC (Web Real-Time Communication)

**Peer-to-peer** protocol for real-time audio, video, and data sharing directly between browsers.

### How it works
```
User A ←→ Signaling Server ←→ User B    (exchange connection info)
User A ←————— direct P2P ——————→ User B  (media flows directly, no server)
```

- **Signaling Server:** Helps peers discover each other and exchange connection details (SDP, ICE candidates)
- **STUN Server:** Helps find your public IP behind NAT
- **TURN Server:** Relays media when direct P2P fails (firewalls)

### Key features
- No plugins needed — works in browsers
- Low latency — direct peer connection
- Encrypted by default

### Use cases
- Google Meet, Discord, Zoom (partially)
- Screen sharing
- File sharing (peer-to-peer)

---

## Video Streaming

How platforms like Netflix/YouTube deliver video efficiently.

### Adaptive Bitrate Streaming (ABR)
Video is **split into small chunks** (2-10 seconds each) encoded at **multiple quality levels**.

```
Original video → Encoder →  1080p chunks (5 Mbps)
                          →  720p chunks  (2.5 Mbps)
                          →  480p chunks  (1 Mbps)
                          →  360p chunks  (0.5 Mbps)
```

Client **automatically switches** quality based on current bandwidth.

### Protocols
- **HLS (HTTP Live Streaming):** Apple's protocol. Most widely used. Uses `.m3u8` playlist files.
- **DASH (Dynamic Adaptive Streaming over HTTP):** Open standard. Uses `.mpd` manifest files.

### How Netflix streams
```
1. Video uploaded → transcoded into multiple resolutions + bitrates
2. Chunks stored on CDN edge servers worldwide
3. User presses play → client fetches manifest file
4. Client downloads chunks one by one, picking quality based on bandwidth
5. Bandwidth drops? → switches to lower quality seamlessly
```
