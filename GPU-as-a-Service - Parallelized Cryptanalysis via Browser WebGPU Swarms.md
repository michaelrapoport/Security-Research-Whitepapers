# Security Research Whitepaper: PL-2027-G5

## GPU-as-a-Service: Parallelized Cryptanalysis via Browser WebGPU Swarms

| **Author** | **Laboratory** | **Date** | **Classification** |
| :--- | :--- | :--- | :--- |
| Michael Rapoport | Polaritrionics Labs | Jan 19, 2026 | TLP:CLEAR |

---

### Abstract

> The introduction of the WebGPU API into modern browsers represents a paradigm shift in client-side computational capability. This paper details the architecture for a distributed cryptanalysis platform that transforms transient web visitors into a massively parallel, ad-hoc GPU supercomputer. We demonstrate how to orchestrate a swarm to perform high-throughput hashing and brute-forcing tasks by dispatching compute shaders written in WGSL (WebGPU Shading Language). Our findings indicate that a moderately sized swarm can achieve hash rates comparable to dedicated hardware, creating a new class of "free" computational threats.

---

### 1. Introduction: Beyond CPU-Based Browser Mining

For years, browser-based threats have been limited by the inefficiencies of JavaScript and the single-threaded nature of early WebAssembly. This restricted parasitic computation to algorithms that were CPU-friendly but slow. WebGPU changes this calculus entirely by providing low-level access to the thousands of parallel cores on a client's graphics card.

This research explores the viability of using this newfound power not for mining, but for targeted, high-speed cryptanalytic attacks, such as password hash cracking.

---

### 2. System Architecture

The system is built on a Chimeric Network foundation, with a specific focus on the compute layer.

1.  **Orchestration Layer (MQTT):** A public MQTT broker is used to distribute "work units." A typical job payload includes the target hash, the salt, and a specific nonce range (e.g., `{"target": "...", "range_start": 1000000, "range_end": 2000000}`). This partitions the search space across the swarm.

2.  **Compute Layer (WebGPU):**
    *   The core cryptographic logic (e.g., a SHA-256 or Scrypt loop) is implemented as a **compute shader** in WGSL.
    *   Upon receiving a work unit, the browser's JavaScript transfers the nonce range and target hash into a GPU buffer.
    *   It then dispatches the compute shader, instructing the GPU to execute the hashing function across thousands of workgroups in parallel. This allows a single browser to test millions of nonces per second.

3.  **Exfiltration Layer (DHT):** To prevent flooding the C2, only successful results are reported. If a node finds a valid nonce that produces the target hash, it announces the solution to a public DHT, from which the operator can retrieve it.

---

### 3. Performance Benchmarks & Evasion

The power of this model is in its massive parallelism. While a single node is weaker than dedicated hardware, the aggregate power is significant.

| System | SHA-256 Hash Rate (Est.) | Notes |
| :--- | :--- | :--- |
| Single RTX 4090 | ~115 GH/s | Dedicated Hardware |
| Single Browser (Apple M1 GPU) | ~150 MH/s | Single Node |
| **Swarm of 10,000 Nodes (Avg.)** | **~1.5 TH/s** | Aggregate Power |

**Evasion is critical for persistence:**

*   **Resource Throttling:** The compute shader is dispatched in bursts and is programmed to utilize no more than 30-40% of the GPU's estimated execution units to avoid causing noticeable system slowdown or fan activation.
*   **Contextual Disguise:** The WebGPU context is created as part of a seemingly legitimate element, such as a WebGL-based game, a 3D advertisement, or a data visualization widget. The cryptanalysis computations run in the background, disguised as rendering or physics calculations.

---

### 4. Mitigation Strategies

Countering WebGPU abuse requires a new layer of browser security.

1.  **Resource Metering:** Browsers must implement robust heuristics to detect non-visible tabs or frames that are dispatching a high volume of compute shaders without a corresponding render pipeline output.
2.  **Permissions Model:** Long-running or high-intensity WebGPU compute operations could require an explicit user permission prompt, similar to notifications or location access.
3.  **Signature Detection:** Security software can be trained to recognize the memory access patterns and WGSL code structures common to popular hashing algorithms.

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