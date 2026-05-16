# 🌐 Network Project: Cairo Branch Handover

**Project:** Company X Site Design (AS 100 - Cairo)
**Date:** May 15, 2026
**Status:** Cairo Internal Configuration Complete; Awaiting Alexandria Handshake.

---

## 📍 1. Cairo Internal Configuration (Completed)

The Cairo branch consists of two buildings (B1 and B2) and three departments.

### IP Addressing Scheme (FLSM /24)

| Department | Building | Network IP | Gateway (Router Interface) |
|------------|----------|------------|---------------------------|
| Sales | Building 1 (B1) | 20.1.1.0/24 | R1 (eth0) |
| HR | Building 1 (B1) | 30.1.1.0/24 | R1 (eth1) |
| IT | Building 2 (B2) | 40.1.1.0/24 | R2 (eth0) |

### Internal Routing Protocol (EIGRP)

- **AS Number:** 100
- **Router-to-Router Links:**
  - R1 to R3: `10.1.1.0/30` (R1: `.1`, R3: `.2`)
  - R2 to R3: `10.1.2.0/30` (R2: `.1`, R3: `.2`)
- **Status:** All Cairo departments (20.x, 30.x, 40.x) are fully reachable via EIGRP.

---

## ☁️ 2. The Service Provider (SP) Cloud Link

Router 3 (R3/RW3) is configured as the **External Gateway** for Cairo to reach Alexandria.

| Parameter | Value |
|-----------|-------|
| Interface to Cloud | eth2 |
| Link IP (Cairo Side) | 10.1.3.1/30 |
| BGP Process | `router bgp 100` |
| Neighbor Configured | 10.1.3.2 with `remote-as 200` |
| Current State | **Active** — R3 is attempting to connect to Alexandria, but R4 is not yet responding |

---

## 🛠️ 3. Action Items for Alexandria (Partner Tasks)

To establish the BGP connection and ensure full **Network Reachability**, the Alexandria engineer must configure **Router 4 (R4/RW4)** with the following:

### A. Physical Link Setup

- **Interface to SP Cloud:** Use IP `10.1.3.2/30`
- **Gateway for Link:** `10.1.3.1`
- **Note:** Use `no shutdown` to ensure the link is administratively up.

### B. BGP Configuration (AS 200)

```
router bgp 200
neighbor 10.1.3.1 remote-as 100
```

> **Address Family:** Must use `address-family ipv4 unicast` for redistribution.

### C. Mutual Redistribution Bridge

Because Cairo uses EIGRP internally and the link to Alex uses BGP, R4 must perform **mutual redistribution**:

- **In BGP:** Redistribute the Alexandria Operation network (`50.1.1.0/24`) into BGP.
- **In Internal Protocol (EIGRP):** Redistribute BGP so the Alex branch knows how to reach Cairo's networks (20.x, 30.x, 40.x).

---

## ✅ 4. Final Verification

| Check | Command | Expected Result |
|-------|---------|-----------------|
| BGP Status | `show ip bgp summary` on R3 | State changes from `Active` to a prefix count |
| Routing Table | `show ip route` on R1 or R2 (Cairo) | `50.1.1.0/24` network appears |
| End-to-End Test | Ping from PC6 (Cairo Sales) to PC7/8 (Alexandria Operation) | Successful ping |

---

> **Note for AI Assistant:**
> The Cairo configuration is strictly using **EIGRP 100** for internal routing and **BGP 100** for external. Mutual redistribution has already been applied on R3. The Cairo side is ready; the partner needs to initiate the handshake from **AS 200**.
