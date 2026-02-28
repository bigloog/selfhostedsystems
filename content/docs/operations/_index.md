---
title: "Operations"
description: "Backups, monitoring, patching, lifecycle, and incident habits that prevent outages."
---

Most self-hosted outages are operational failures, not hardware failures. A disk that fails is recoverable. A backup that was never tested is not.

This section covers the practices that prevent operational failures and reduce the cost of the ones that happen anyway.

## Pages

- [Operational baselines](/docs/operations/baselines/) — the minimum bar before adding any complexity
- [Backups that survive operator error](/docs/operations/backups/) — backup strategy, restore testing, and documentation

## Principles

**Operations before features.** A new service is only as reliable as the operational practices around it. If the backup is untested, the service is more fragile than it appears.

**Runbooks before incidents.** Writing recovery procedures while things are working is much easier than writing them while things are broken.

**Alert less, but meaningfully.** An alert that fires without indicating a required action is noise. Noise is ignored.
