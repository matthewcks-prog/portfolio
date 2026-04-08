# mini-fabric-lab

## Overview
A reproducible network fabric lab built to demonstrate topology-as-code, configs-as-code, and verification-as-code using Containerlab, FRRouting, and Python.

## Problem
I wanted a project that showed more than application development: something that demonstrated routing fundamentals, automation, reproducibility, and systematic validation.

## What I built
- 2 spine routers
- 2 leaf routers
- 1 dual-homed edge router
- 2 host subnets
- OSPF underlay
- iBGP overlay with route reflectors
- eBGP edge peering
- Python healthcheck and evidence collection

## Key technical decisions
### Why OSPF underlay + iBGP overlay
OSPF is the simplest IGP for a small fabric — zero pre-configuration between neighbors on a point-to-point link, fast convergence, and it naturally distributes loopback /32 addresses that iBGP needs as stable peer addresses. Using an IGP for underlay and BGP for overlay is the pattern used in production data-center fabrics (e.g., Clos designs), so it directly reflects real-world architecture.
### Why route reflectors
In a full-mesh iBGP design, every router must peer with every other router — n(n-1)/2 sessions. Route reflectors break that scaling problem: leaves only need sessions to the two spines. In this lab the saving is small, but the pattern scales identically to a 100-leaf fabric. Spines are the natural RR placement because they already have physical connectivity to every leaf.
### Why next-hop-self
When spine1 (RR) reflects a route originally advertised by edge1 (172.16.0.1) to leaf1, the BGP next-hop is still 172.16.0.1 — an address leaf1 has no OSPF route to. next-hop-self on the spine rewrites that next-hop to the spine's own loopback (10.255.0.11), which leaf1 does have an OSPF route to. Without this, reflected routes are unreachable at the leaves.
### Why policy filtering at the edge
edge1 uses default-originate (a static Null0 black-hole triggers the conditional) rather than a network statement, which is the production-safe way to originate a default.

...

## Validation
- OSPF neighbours formed successfully
- BGP sessions established
- Default route received from edge
- Leaf subnets advertised correctly
- End-to-end host connectivity verified
- Failure scenario tested and recovered

## What I learned
- The value of stable loopback-based peering
- Why route reflectors matter for scale
- How next-hop handling affects reachability
- How automated verification improves confidence and debugging speed

## What I would do next
- ECMP
- BFD
- CI-based lab regression testing
- automated failure injection
