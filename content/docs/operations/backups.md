---
title: "Backups that survive operator error"
description: "A backup that hasn't been restored is a hope, not a backup."
---

Backups fail in two ways: they don't exist, or they exist but haven't been tested. Both failures are equally bad when you need to recover.

## What a backup actually needs to do

A backup must let you recover a specific service, to a specific point in time, within a defined window, without the assistance of the failing system.

That sentence contains four requirements:

- **Specific service** — not "I have backups", but "I can restore this service"
- **Specific point in time** — your RPO defines how much data loss is acceptable
- **Defined time window** — your RTO defines how long recovery can take
- **Without the failing system** — local-only backups fail when the local system fails

## RPO and RTO

**RPO (Recovery Point Objective)** is the maximum acceptable data loss, measured in time. If your RPO is 24 hours, you accept losing up to a day of data. If your backups run nightly, your actual RPO is "up to 24 hours" — regardless of how often you tell yourself you have daily backups.

**RTO (Recovery Time Objective)** is the maximum acceptable downtime. If you can tolerate 4 hours of unavailability during recovery, your RTO is 4 hours. Slow restores from cold storage can exceed RTO even when the backup itself is intact.

State RPO and RTO before choosing backup tooling. The numbers do not need to be precise — they need to be stated so that your backup strategy can be evaluated against them.

## The minimum backup architecture

**3-2-1 as a baseline:**

- **3** copies of data
- **2** different storage media
- **1** copy off-site (or at minimum, off-host)

For a homelab this typically means:

- Local backup (fast restore, survives accidental deletion)
- Network-attached backup on a second device (survives local disk or host failure)
- Off-site or cloud backup (survives physical events: fire, theft, hardware failure)

You do not need all three immediately. Start with at minimum: an automated local backup and an off-host copy.

## Automated backups

Manual backups fail. People forget, get busy, or assume someone else did it.

Backups must run automatically on a schedule. The schedule should match your RPO. If your RPO is 24 hours, backups must run at least daily.

**Commonly used tools:**

- **restic** — encrypted, deduplicated, supports multiple storage backends (local, SFTP, S3-compatible, and others)
- **BorgBackup** — similar to restic; mature, efficient deduplication
- **rsync** — simple and widely supported; not deduplicated, no built-in encryption — suitable for supplementary copies, not as the primary backup tool

For application data (databases, mail stores, etc.), use application-aware backup methods. A filesystem snapshot of a running database may be internally inconsistent. Use `pg_dump`, `mysqldump`, or equivalent application-level exports before snapshotting.

## Off-host copies

A backup that lives on the same host as the data it protects is not a backup. It is a copy. Copies fail when the host fails.

Off-host options:

- A second local machine or NAS
- Cloud object storage (Backblaze B2, Wasabi, AWS S3, or similar)
- A remote VPS with sufficient storage

Encrypt backups before they leave your control. Assume that any remote storage could be accessed by someone other than you. restic and BorgBackup both encrypt by default.

## Restore testing

This is the part most people skip. It is the most important part.

**Schedule restore tests** — not "I'll test it when I need it." A calendar event, recurring, at whatever interval makes sense for the service. Quarterly at minimum. Monthly for critical services.

**What a restore test requires:**

1. A target environment separate from the production system (a VM, a spare machine, a temporary container)
2. The actual backup data applied to that environment
3. Verification that the service functions correctly against that data
4. A written record of the test: date, what was restored, what the result was

A restore test that is not documented did not happen. Future-you will not remember whether the backup works.

**Common restore test failures:**

- Backup ran, but the backup storage is no longer accessible (credentials rotated, destination full, network changed)
- Backup files exist but are corrupted or incomplete
- Restore procedure is outdated — the service was upgraded, the procedure wasn't
- Restore completes but the application data is wrong — a misconfiguration was backed up faithfully

## Encryption

Encrypt backups in transit and at rest, particularly off-host copies. This is not optional if backups contain credentials, personal data, API keys, or anything you would not want readable by a third party.

restic and BorgBackup both encrypt by default. Store the encryption passphrase somewhere you can access it when the primary system is unavailable — a password manager, a physically secured document, or a separate secrets store. A backup encrypted with a passphrase you have lost is a backup you cannot restore.

## Documentation

Write down and keep current:

- What is being backed up
- Where backups are stored (paths, endpoints, credentials location)
- How to access them (tools required, passphrase location)
- The restore procedure for each service, step by step
- When the last restore test was performed and what the result was

Store this documentation off the failing system. A runbook that lives only on the server being recovered is useless.
