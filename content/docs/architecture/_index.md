---
title: "Architecture"
description: "Compute, storage, networking, power, redundancy — and the tradeoffs that matter."
---

Architecture decisions at homelab scale are often made by feel rather than analysis. This section frames those decisions explicitly: what problem is being solved, what failure modes are introduced, and where the complexity is justified.

## Pages

- [High availability at homelab scale](/docs/architecture/ha-at-home/) — defining failure modes and matching solutions to them

## Principles

**Simpler systems fail more simply.** A complex architecture may have better theoretical availability but worse practical availability if it introduces failure modes you do not fully understand.

**Tradeoffs are real.** Every architectural decision trades something — cost, complexity, reliability, recovery time. State the tradeoffs rather than assuming the chosen design is optimal in all dimensions.

**Homelab scale is different.** Patterns that make sense at datacenter scale — Ceph, full N+2 clustering, multiple redundant network paths — carry disproportionate operational cost at one or two nodes. Choose designs sized for your actual scale.
