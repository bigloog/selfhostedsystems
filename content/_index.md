---
title: "Self Hosted Systems"
description: "Practical guidance for running self-hosted infrastructure with business-grade operational discipline."
lead: "Reducing operational mistakes in self-hosted infrastructure — homelabs built with a production mindset."
---

If it wouldn't survive a restore test, a maintenance window, or a bad day, it doesn't count.

---

## Start here

- [Operational baselines](/docs/operations/baselines/) — the minimum bar before adding complexity
- [Backups that survive operator error](/docs/operations/backups/) — strategy, restore testing, and documentation
- [High availability at homelab scale](/docs/architecture/ha-at-home/) — define the failure, choose the minimum design that survives it

---

## What this site is

A reference for engineers running serious homelabs — people who want operational habits *before* systems matter.

Not gear reviews. Not affiliate lists. Not "look at my rack." The focus is **operational reality**.

---

## Why homelabs fail in business contexts

Most self-hosted systems fail for predictable reasons:

- backups that have never been restored
- single points of failure hidden by convenience
- monitoring that alerts, but doesn't inform action
- "temporary" setups that quietly become permanent

Homelabs are safe places to learn these lessons — *if* you treat them as such.
