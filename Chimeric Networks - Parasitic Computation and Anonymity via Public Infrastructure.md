# Security Research Whitepaper: PL-2026-X9

## Chimeric Networks: Parasitic Computation & Anonymity via Public Infrastructure

| **Author** | **Laboratory** | **Date** | **Classification** |
| :--- | :--- | :--- | :--- |
| Michael Rapoport | Polaritrionics Labs | Jan 19, 2026 | TLP:CLEAR |

---

### Abstract

> This paper characterizes a novel class of "Infrastructure-less" distributed systems termed **Chimeric Networks**. By weaponizing the functional overlap between unauthenticated IoT signaling (MQTT), peer-to-peer discovery (MLDHT), and browser-native transport (WebRTC), we demonstrate a methodology for parasitic computation and residential proxying that resides entirely within legitimate public protocol traffic. The Chimeric model represents the apex of "Living off the Cloud," where an operator avoids all forensic attribution by leveraging the internet's public commons.

---

### 1. Introduction: The Fragility of Centralized Control

The operational security of traditional distributed systems, including botnets, is predicated on the resilience of their Command & Control (C2) infrastructure. Whether centralized or employing Domain Generation Algorithms (DGA), this infrastructure remains a discoverable and ultimately fragile point of failure.

Chimeric Networks subvert this paradigm by offloading all operational layers—signaling, storage, and transport—to robust, high-availability public services that are not owned by the operator. The network becomes a "ghost," a logical entity that exists only as ephemeral traffic patterns on services maintained by neutral third parties.

---

### 2. The Trilateral Architecture

A Chimeric Network is composed of three interoperable layers, each mapped to a specific public protocol.

1.  **The Signal Layer (MQTT):** The system's nervous system. We utilize public, unauthenticated MQTT brokers (e.g., `test.mosquitto.org`, `broker.hivemq.com`) as a low-latency, publish-subscribe message bus. An operator broadcasts job instructions to a cryptographically derived topic, and any node in the swarm subscribed to that topic receives the command instantly.

2.  **The Storage Layer (DHT):** The system's asynchronous memory. To avoid direct communication from a worker node back to the C2, results are exfiltrated via the BitTorrent Mainline DHT. Using BEP 44, a node can `put` a small, mutable data item into the global DHT. The operator's aggregator node simply `get`s the data associated with a known InfoHash, breaking the chain of attribution.

3.  **The Compute & Transport Layer (Browser Technologies):** The system's muscle. The execution payload is delivered to transient web browsers.
    *   **WebAssembly (WASM):** For high-performance cryptographic tasks (computation).
    *   **WebRTC:** For establishing direct, encrypted peer-to-peer data channels for proxying and mesh communication (transport).

---

### 3. Use Case I: Voltaic Swarm (Parasitic Computation)

The Voltaic Swarm is a serverless crypto-mining or brute-forcing grid.

*   **Orchestration:** The operator broadcasts a block header and a nonce range via MQTT.
*   **Execution:** Thousands of browser-based nodes execute a WASM-based hashing module in a background Service Worker. To evade browser throttling, computation is performed in short bursts (e.g., 200ms on, 50ms off).
*   **Exfiltration:** Upon finding a valid nonce, the worker node announces the solution to the DHT under a job-specific InfoHash. The operator's listener is the only party aware of which InfoHash to monitor.

### 4. Use Case II: Ghost Tunnel (Anonymous Proxying)

The Ghost Tunnel is a decentralized residential proxy network that launders traffic through the IP addresses of swarm participants.

*   **The Blind Handshake:** To establish a connection without a central signaling server, the DHT is used as a rendezvous point.
    1.  An "exit node" (a browser in the swarm) generates a WebRTC SDP offer.
    2.  It stores this offer in the DHT at a key derived from a time-based seed.
    3.  The operator's client polls the DHT, retrieves the offer, generates an SDP answer, and posts it back.
    4.  Using public STUN servers for NAT traversal, a direct, encrypted UDP tunnel is established.
*   **Reputation Laundering:** All traffic originating from the tunnel inherits the high reputation score of the residential IP, bypassing geo-blocks and sophisticated WAFs that filter datacenter traffic.

---

### 5. Mitigation Strategies

Defending against Chimeric Networks requires a shift from infrastructure blocking to protocol-aware anomaly detection.

1.  **Content Security Policy (CSP):** System administrators should implement strict `connect-src` policies to block WebSocket connections to known public MQTT brokers.
2.  **DHT Traffic Analysis:** Monitor for high-frequency, small-payload `put` requests originating from a single residential IP, which is anomalous for standard BitTorrent usage.
3.  **Browser Heuristics:** Modern browsers should implement resource metering for background tabs, flagging or throttling WASM threads that exhibit consistent, high-entropy computational patterns characteristic of PoW.

---

### Support Our Independent Research

The work at Polaritrionics Labs is self-funded. If you find value in our research and wish to support our mission of identifying and mitigating next-generation threats, please consider a donation.

*   **Bitcoin (BTC):** `bc1q...`
*   **Ethereum (ETH):** `0x...`
*   **Monero (XMR):** `4...`

<p align="center">
  <a href="https://nowpayments.io/payment/?iid=4957005986&source=button" target="_blank" rel="noreferrer noopener">
     <img src="https://nowpayments.io/images/embeds/payment-button-black.svg" alt="Crypto payment button by NOWPayments">
  </a>
</p>