# GPS Fleet Tracking over Congested LTE — QoS Queuing Optimization

**Simulation study of queuing disciplines (FIFO vs PQ vs WFQ) for real-time GPS traffic from an urban bus fleet sharing a congested LTE uplink with best-effort passenger traffic.**

| | |
|---|---|
| **Type** | Network simulation — QoS / traffic engineering |
| **Authors** | Samuel Sánchez García & Mayah Drayton (joint project) |
| **Context** | ECE 540, University of New Mexico, 2026 |
| **Simulator** | NetSim (LTE-Advanced topology) |
| **Status** | Complete — **all results are simulated (proof of concept)** |

---

## Problem

Smart-city bus fleets transmit small, periodic GPS packets (RTPS class) over the same LTE network as heavy passenger traffic (streaming/downloads, Best Effort class). Under congestion, Head-of-Line Blocking at the bottleneck queue delays GPS packets until the location data is obsolete — breaking the real-time SLA that ETA systems depend on.

## Scenario

- 10 UEs (buses), Random Waypoint mobility at 15 m/s
- GPS traffic: 10 RTPS flows, 150-byte UDP packets, 1 packet/s
- Congestion traffic: 5 BE flows (1024-byte packets) deliberately exceeding the router's output link capacity
- QoS applied at the backhaul router (Layer 3); latency measured end-to-end at the server

## Results (simulated)

| Queuing discipline | Avg GPS delay | SLA |
|---|---|---|
| FIFO (baseline) | **760 ms** | Critical failure |
| PQ / WFQ | **1.5 ms** | Compliant (**99.8% reduction**) |

- GPS packet delivery ratio: 100% in all scenarios (the critical flow is never lost)
- BE loss: constant 27.5%, confirming the offered load exceeded link capacity by design — QoS fixed latency without worsening low-priority loss
- **Recommended policy: WFQ** — meets the real-time target while guaranteeing a minimum bandwidth quota to BE traffic (no starvation), unlike strict PQ

## Scope & honesty note

In a real LTE deployment, the dominant uplink queuing delay originates at the **eNodeB MAC scheduler (Layer 2)**, not at a Layer-3 router. This study applies QoS at the router and is therefore a **demonstration of traffic classification effectiveness**, not an end-to-end LTE air-interface solution. This constraint is analyzed explicitly in the report, and the conclusion — that recognizing and prioritizing the RTPS class is what mitigates the delay — holds at either layer.

## Repository contents

```
docs/       Full report (PDF): topology, methodology, results, critical analysis
figures/    Delay comparison, throughput plots (link + application layer)
```

## Skills demonstrated

`QoS` `Weighted Fair Queuing` `Priority Queuing` `traffic classification` `Head-of-Line Blocking` `LTE architecture (MAC vs backhaul)` `SLA-driven design` `NetSim`
