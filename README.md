# TCP Timing Lab 01

## Observing Transmission Delay (Serialization Delay)  
> Transmission Delay (Serialization Delay)  refers to the time required for a network interface to place an entire frame onto a physical transmission medium

Observing Ethernet Transmission Delay (Serialization Delay) through inter-frame timing (Δt) across 10/100/1000 Mbps links  
> **The network transmits bits, not packets.**

---

## 📌 Overview

In the classic textbook Computer Networking: A Top-Down Approach, Transmission Delay (also known as Serialization Delay) is defined as:

L / R

- L: packet length (bits)  
- R: link rate (bps)

It is one of the simplest formulas in networking.

So simple that almost every network engineer *knows* it —  
yet almost no one has ever *seen* it on the wire.


This lab demonstrates that **Ethernet Transmission Delay (serialization delay) is a fixed physical time**, not a theoretical abstraction.
By observing **inter-frame spacing (Δt)** in real packet captures, we directly measure Transmission Delay (serialization delay) across different link speeds.

---

## 🎯 Objective

* To **observe Transmission Delay (serialization delay) directly from packet captures**
* To validate that:

  * Transmission Delay (serialization delay) = **frame size / link rate**
  * It appears as **Δt between consecutive frames**
* To compare behavior across:

  * **10 Mbps / 100 Mbps / 1 Gbps (FDX)**

---

## 🧠 Key Insight

> **Transmission Delay (serialization delay) is not inferred — it is observable.**

Even though packet analyzers display frames,
what we are actually observing is:

> **bit transmission time projected into frame spacing**

---

## 🧪 Experiment Setup

### Topology

```
Client (10/100/1000 Mbps)
        │
        │
   NetOptics TAP (FDX)
        │
        │
Server (HTTP)
```
* Direct connection (no switch / router)
* TAP used for **accurate packet capture**
* Capture device: **NPM Device / Protocol Analyzer (in-memory capture)**
![Lab Topology](./experimental-setup_v2.svg)
---
### Traffic Generation
* HTTP download (curl)
* Large file transfer (multi-MB)
* Continuous packet train generation

---

## 📊 What We Observe

### Packet Train  

```
Frame1      Frame2      Frame3      Frame4
|-----|     |-----|     |-----|     |-----|
    Δt          Δt          Δt
```

## 📊 Key Observation

| Link Speed | Observed Δt |
|------------|------------|
| 10 Mbps    | ≈ 1.23 ms  |
| 100 Mbps   | ≈ 0.123 ms |
| 1 Gbps     | ≈ 0.012–0.013 ms* |

\* At 1 Gbps, Δt is limited by analyzer timestamp resolution.  

## 📊 Expected Results

| Link Speed | Frame Size | On-Wire Size | L/R (Frame) | Δt (Wire-Time) | Observed Δt |
|------------|------------|--------------|-------------|----------------|-------------|
| 10 Mbps    | 1518 B     | 1538 B       | 1.214 ms    | 1.230 ms       | Consistent  |
| 100 Mbps   | 1518 B     | 1538 B       | 0.121 ms    | 0.123 ms       | Consistent  |
| 1 Gbps     | 1518 B     | 1538 B       | 12.144 µs   | 12.304 µs      | ≈ 12–13 µs* |

\* Limited by analyzer timestamp resolution at microsecond granularity.

## Experimental Results (10Mbps)  
![Packet Analysis](./observing-ethernet-transmission-delay-on-a-10mbps-link-server-to-client_v2.svg)  

## Experimental Results (100Mbps)
![Packet Analysis2](./observing-ethernet-transmission-delay-on-a-100mbps-link-server-to-client-v2.svg)  

## Experimental Results (1Gbps)
![Packet Analysis3](./observing-ethernet-transmission-delay-on-a-1gbps-link-server-to-client.svg)  

---

## 📐 Theoretical Derivation

Ethernet on-wire size includes:

* Frame: 1518 bytes
* Preamble + SFD: 8 bytes
* IFG: 12 bytes

### Total:

```
1538 bytes (on wire)
```

### Serialization Delay:

```
(1538 bytes × 8 bits) / Link Speed
```

---

### Example (10 Mbps)

```
(1538 × 8) / 10,000,000 ≈ 1.23 ms
```

✔ Matches observed Δt

---

## 🔍 Measurement Method

## 🔧 Key Technique

This experiment relies on isolating a single physical timing component from a complex system.

1) **Controlled Environment**  
Design an "ideal" observation environment that isolates Transmission (Serialization) Delay by minimizing Processing, Queuing, and Propagation Delays.

2) **Wire-Level Visibility**  
Use a physical-layer TAP (NetOptics Full-Duplex In-Line TAP) to gain non-intrusive, wire-speed access to traffic.

3) **High-Precision Measurement**  
Capture packets using a dedicated NPM device / protocol analyzer with microsecond-level timestamp accuracy.

4) **Time-Domain Analysis**  
Analyze inter-frame spacing (Δt) in Wireshark to directly observe the physical timing structure of packet transmission.

---

### Important Notes

* Measure **only data frames (e.g., 1518B)**
* Ignore:

  * ACK packets (they may break spacing)
* For accuracy:

  * Analyze **single direction traffic**
  * Or split capture into TX/RX

---

## ⚠️ Common Misconception

❌ “Packets are sent instantaneously”
❌ “Throughput determines timing”

---

✅ Reality:

> **Packets take time to be serialized onto the wire**

and:

> **Δt = serialization delay**

---

## 🧩 Why This Matters

This experiment reveals a fundamental truth:

> **The network is a time-structured system**

Implications:

* Packet trains are **physically paced**
* TCP behavior is **time-driven (ACK clock)**
* Throughput is **not equal to bandwidth**

---

## 🔬 Measurement Validity

### Does TAP Aggregation Affect Results?

No.

Reason:

* IFG is a **fixed time gap (96 bit-times)**
* Serialization delay is **determined by link speed**
* Monitor port rate ≈ network rate

Therefore:

> **Observed Δt remains valid representation of wire-time spacing**

---

## 📎 Reproducibility

To reproduce:

1. Set NIC speed (10 / 100 / 1000 Mbps)
2. Use direct connection (no switch)
3. Generate continuous traffic (HTTP / iperf)
4. Capture with:

   * InfiniStream (preferred)
   * Wireshark (acceptable)
5. Measure:

   * Δt between full-size frames

---

## 📊 Summary Table

| Link Speed | Frame Size | On-Wire Size | Δt (Expected) | Δt (Observed) |
| ---------- | ---------- | ------------ | ------------- | ------------- |
| 10 Mbps    | 1518 B     | 1538 B       | 1.23 ms       | ✔ Match       |
| 100 Mbps   | 1518 B     | 1538 B       | 0.123 ms      | ✔ Match       |
| 1 Gbps     | 1518 B     | 1538 B       | 0.0123 ms     | ✔ Match       |

---

## 🚀 Engineering Implication

This lab establishes the foundation for:

* Packet Train analysis
* ACK Clock understanding
* Throughput anomalies
* Congestion behavior

---

## 🔗 Next Lab

👉 **Lab 02 — Serialization vs Throughput**

> When serialization delay is fixed,
> why does throughput fluctuate?

---

## 📖 Closing Thought

> **If you can measure time, you can explain the network.**

---

## ⭐ Repository Status

Work in progress — figures and packet captures will be added.


# TCP Timing Lab 01  
## Observing Transmission Delay (Serialization Delay)

> From theory to wire: making L / R visible.

---

## 📌 Overview

In the classic textbook *Computer Networking: A Top-Down Approach*, Transmission Delay (also known as Serialization Delay) is defined as:

L / R

- L: packet length (bits)  
- R: link rate (bps)

It is one of the simplest formulas in networking.

So simple that almost every network engineer *knows* it —  
yet almost no one has ever *seen* it on the wire.

This lab turns that formula into a measurable reality.

By observing inter-frame spacing (Δt) in real packet captures, we reveal that Ethernet serialization delay is a fixed physical time — directly observable, repeatable, and verifiable across link speeds.

---

## 🎯 Objective

- Make Transmission Delay observable  
- Map L / R → Δt (inter-frame spacing)  
- Validate theory using real packet captures  

---

## 🧠 Key Insight


Transmission Delay = Serialization Delay = Δt (inter-frame spacing)


What you see in packet captures is not an approximation.

It *is* the wire.

---

## 🧪 Experiment Setup

### Topology

![Experimental Setup](./figures/fig1-experimental-setup.svg)

### Traffic Generation

- Continuous TCP data transfer (HTTP download)  
- Full-sized Ethernet frames (1518 Bytes)  
- Back-to-back packet train under sustained throughput  

---

## ✅ Why This Measurement Is Valid

This experiment is not an approximation.  
It is a direct observation of a physical timing property on the wire.

### 1) No Intermediate Devices

- Direct back-to-back connection  
- No switches or routers  
- No queuing or forwarding delay  

### 2) Negligible Propagation Delay

- Very short cable  
- Propagation delay ≪ serialization delay  

### 3) Passive TAP Observation

- NetOptics Full-Duplex In-Line TAP  
- No packet modification  
- No timing distortion  

### 4) High-Precision Capture

- Dedicated NPM / protocol analyzer  
- Microsecond-level timestamp accuracy  

### 5) Δt Represents Serialization Delay


Δt = time between consecutive frames


On a saturated link:


Δt ≈ (Frame + Preamble + IFG) / Link Rate


---

### 🧠 Conclusion


This is not inferred.
This is not estimated.
This is directly observed.


---

## 👁️ What We Observe

### Packet Train

Under continuous transmission, packets form a **packet train**:


[Frame][Frame][Frame][Frame]
Δt Δt Δt


Each Δt reflects the time required to serialize one frame onto the link.

---

## 📊 Key Observation

| Link Speed | Observed Δt |
|------------|------------|
| 10 Mbps    | ≈ 1.23 ms  |
| 100 Mbps   | ≈ 0.123 ms |
| 1 Gbps     | ≈ 0.012–0.013 ms* |

\* Limited by analyzer timestamp resolution.

---

## 📊 Expected Results

| Link Speed | Frame Size | On-Wire Size | L/R (Frame) | Δt (Wire-Time) | Observed Δt |
|------------|------------|--------------|-------------|----------------|-------------|
| 10 Mbps    | 1518 B     | 1538 B       | 1.214 ms    | 1.230 ms       | Consistent  |
| 100 Mbps   | 1518 B     | 1538 B       | 0.121 ms    | 0.123 ms       | Consistent  |
| 1 Gbps     | 1518 B     | 1538 B       | 12.144 µs   | 12.304 µs      | ≈ 12–13 µs* |

\* Limited by analyzer timestamp resolution.

---

## 📈 Experimental Results (10 Mbps)

![10 Mbps Result](./figures/fig2-transmission-delay-10mbps.svg)

---

## 📈 Experimental Results (100 Mbps)

![100 Mbps Result](./figures/fig3-transmission-delay-100mbps.svg)

---

## 📈 Experimental Results (1 Gbps)

![1 Gbps Result](./figures/fig4-transmission-delay-1gbps.svg)

*Observed Δt is shown as ~0.012 ms due to timestamp quantization; theoretical value is ~12.304 µs.*

---

## 📐 Theoretical Derivation

Ethernet on-wire size includes:

- Frame: 1518 Bytes  
- Preamble + SFD: 8 Bytes  
- IFG: 12 Bytes  


Total = 1538 Bytes


Transmission delay:


Δt = 1538 × 8 / R


Example (10 Mbps):


Δt ≈ 1.23 ms


---

## 🔧 Key Technique

1) Design an isolated observation environment  
2) Use physical-layer TAP for non-intrusive capture  
3) Capture with high-precision analyzer  
4) Analyze Δt using Wireshark  

---

## ⚠️ Important Notes

### 1 Gbps Measurement Limitation

At 1 Gbps:

- Theoretical Δt ≈ 12.304 µs  
- Observed Δt ≈ 12 µs  

Due to timestamp resolution, single-frame measurement reaches quantization limits.

More accurate validation can be achieved by averaging across a packet train.

---

## ❗ Common Misconception

> Transmission Delay is theoretical

❌ Incorrect  
✔ It is a deterministic physical time on the wire

---

## 🚀 Conclusion


L / R is not just a formula.
It is a measurable physical reality.


If you can measure Δt,  
you are not reasoning about the network —  
you are observing it.
