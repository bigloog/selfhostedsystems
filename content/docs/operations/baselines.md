---
title: "Operational baselines"
description: "The minimum operational bar before you add complexity."
---

Most self-hosted systems fail for operational reasons, not technical ones. Before adding a new service, automating something, or scaling anything — establish these baselines.

## Backups with restore tests

A backup file is not a backup. It is a file.

A backup is only verified when you have completed a restore: taken the data, applied it to a system, and confirmed it works. Everything before that is hope.

**Minimum requirements:**

- Automated backup process (not manual, not "I'll do it later")
- Off-host copy (local-only backups fail when the host fails)
- Documented restore procedure
- Regular restore tests — scheduled, run, and recorded

See [Backups that survive operator error](/docs/operations/backups/) for implementation guidance.

## Monitoring with actionable alerts

An alert that fires without telling you what to do is noise. Noise gets ignored.

Monitoring at homelab scale does not need to be complex. It needs to be specific about what broke and what the expected response is.

**Minimum requirements:**

- Service health checks on critical services
- Disk space alerts before disks fill — not when they fill
- Each alert has a documented response, even if the response is "reboot it"
- Alerts go somewhere you will actually see them (not just a log nobody reads)

**Avoid:**

- Alerting on everything, which trains you to ignore alerts
- Monitoring dashboards with no linked runbooks
- Alerts with no clear severity or expected action

## Patch cadence

Software with unpatched vulnerabilities is a liability. Patches require a predictable cadence — not urgency-driven firefighting after a CVE is published.

**Minimum requirements:**

- A defined schedule for applying security patches (monthly is a reasonable default)
- A list of what is running and what version it is on (an inventory, even a simple one)
- A process for testing patches before applying them to critical systems — even if that process is "boot a test VM"
- Notes on end-of-life software, so you know when you're running something unsupported

Homelab systems often run software indefinitely. The version installed is the version that runs. Build the habit of regular patching before it becomes a problem.

## Recovery runbooks

You will need to recover a system at the worst possible time: at 2am, stressed, having not thought about this system in six months.

A runbook is a written document that describes how to recover a service. It does not need to be long. It needs to exist.

**Minimum content:**

- How to start, stop, and restart each service
- Where the data lives and how to restore it
- What the dependencies are — what else needs to be running first
- What "working" looks like — how to verify the restore was successful

**Where to store runbooks:**

- Off the failing system (a runbook on a crashed server is not useful)
- Somewhere with version history — a git repository is fine
- Where you will actually look when things are broken

## Checking the baseline

Before adding any new service or complexity, run through this checklist:

1. Are backups running and tested?
2. Are critical services being monitored?
3. Are patches current?
4. Does a runbook exist?

If the answer to any of these is no, that is more important than the new feature.
