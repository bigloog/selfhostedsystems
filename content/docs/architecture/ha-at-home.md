---
title: "High availability at homelab scale"
description: "Define the failure you're surviving and choose the least complex design that meets it."
---

"High availability" at homelab scale usually means one of two things: a complex multi-node cluster, or a vague sense that things will probably be fine. Neither is a strategy.

HA is defined relative to a failure mode. Before choosing a design, define the failure you are trying to survive.

## Define the failure first

Common failure modes at homelab scale:

**Single disk failure** — a drive fails. RAID or ZFS protects against this. A backup does not (backups protect against data loss, not availability). Disk failure is the most common hardware event in a homelab context.

**Single host failure** — the machine itself fails (PSU, motherboard, memory). RAID does not protect against this. Surviving it requires a second host or the ability to restore to different hardware quickly.

**Router or network failure** — all services become unreachable even if the host is healthy. Redundant network paths or a failover router protect against this.

**Human error** — accidental deletion, misconfiguration, botched update. Backups with point-in-time recovery protect against this. Clustering does not. In practice, human error causes more homelab outages than hardware failure.

**Power failure** — all local systems go offline simultaneously. A UPS covers short outages and allows graceful shutdown. Off-site services are required to protect against extended power loss.

Most homelab HA projects target single-host failure. Most homelab outages are caused by human error or single-disk failure. Match the solution to the actual risk.

## The complexity trade-off

More complex systems fail in more complex ways.

A two-node Proxmox cluster introduces quorum issues, network split-brain, and synchronisation lag. A single well-configured node with tested backups and a documented recovery procedure can be more reliable in practice — and considerably easier to recover from when it does fail.

Ask: **does the complexity of the HA solution reduce more risk than it introduces?**

This is not an argument against HA. It is a requirement to be specific about what problem it solves.

## Common approaches at homelab scale

### RAID / ZFS mirrors

**Protects against:** single disk failure
**Does not protect against:** host failure, human error, accidental deletion
**Complexity:** low
**Suitable when:** disk failure is the primary concern, host is otherwise reliable

RAID is not a backup. It provides availability — the system continues running after a disk fails — but not recovery. If you delete something, the deletion is mirrored immediately. RAID + backup covers both disk failure and data loss.

ZFS RAIDZ or mirroring is a reasonable default for storage nodes and small servers. Combined with off-host backups, this addresses the two most common homelab failure modes without significant operational overhead.

### Backup with defined RTO

**Protects against:** disk failure, host failure, human error
**Does not protect against:** extended outages during recovery
**Complexity:** low
**Suitable when:** service can tolerate hours of downtime during recovery

This approach is underrated. A service with tested backups and a documented recovery procedure has a defined RTO. If that RTO is acceptable, a complex HA setup is unnecessary overhead.

For most homelab services, an RTO of "recovered within 4 hours" is perfectly acceptable. Name the number. If it's acceptable, stop there.

### Two-node cluster (Proxmox, etc.)

**Protects against:** single host failure
**Does not protect against:** cluster split-brain without a quorum device, shared storage failure
**Complexity:** medium to high
**Suitable when:** host failure is unacceptable and shared storage or replication is available

Two-node clusters are the minimum configuration for automatic failover but the most problematic quorum configuration. Proxmox requires a witness (third node or quorum device) for reliable split-brain prevention. Without it, a network partition between two nodes can cause both to believe the other has failed, resulting in data corruption or service duplication.

Shared storage (Ceph, NFS, iSCSI) introduces additional failure modes but allows live migration. Local storage with replication (Proxmox Backup Server sync, BorgBackup over SSH) avoids shared storage dependencies at the cost of additional recovery steps.

### Container and VM orchestration (k3s, Nomad)

**Protects against:** workload failure, single-node failure across a cluster
**Does not protect against:** full cluster failure, persistent storage failure
**Complexity:** high
**Suitable when:** multiple worker nodes are available, stateless workloads are dominant

Stateful workloads (databases, file storage, message queues) are significantly harder to run reliably in distributed orchestration environments. Persistent volume handling across nodes requires careful configuration and testing. The failure modes of distributed storage (Longhorn, Rook-Ceph) are non-trivial.

For a homelab, this approach is worth learning if you intend to operate similar systems professionally. It is not the right choice if the primary goal is reliable service delivery rather than learning the stack.

## Matching the solution to the failure

| Failure to survive | Minimum appropriate solution |
|---|---|
| Single disk failure | RAID / ZFS mirror |
| Data loss / human error | Tested backups with point-in-time recovery |
| Single host failure | Backup + defined RTO, or hot standby host |
| Coordinated multi-service failover | Cluster with quorum device |
| Site-level failure | Off-site backups + documented rebuild process |

Start with the row that represents your most likely or most impactful failure mode. Add complexity only when there is a specific, evaluated reason.

## What "available" actually means

Define availability per service, not for the homelab as a whole.

Some services (media streaming, home automation dashboards) can tolerate hours of downtime without meaningful impact. Others (DNS, authentication, network routing) cause cascading failures across other services when unavailable.

Prioritise availability work on services that other services depend on. Accept lower availability for services that are merely inconvenient when unavailable. Most homelabs have two or three genuinely critical services. The rest can tolerate a recovery window.
