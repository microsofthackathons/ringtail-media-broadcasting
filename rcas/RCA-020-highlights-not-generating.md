# [INC-20260429-020] 04/29/26 - P2 - Post-Game Highlights Not Generating Automatically

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 2 - Must Have |
| **Reporter** | Content Operations |
| **Assignee** | Highlights Platform Lead |
| **Resolution** | Resolved |
| **Components** | Highlights Generator, ML Pipeline, Content Index |
| **Affects Versions** | Highlights Service v1.4.0 |
| **Fix Versions** | Highlights Service v1.4.1 |
| **Labels** | Highlights, P2, ML, Content-Generation |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 4 hours 30 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 04/29/26 |
| **Updated** | 04/30/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260429-020 | Highlights Generation Failure | Closed |
| Related | CHG-5012 | ML Pipeline GPU Allocation Fix | Completed |

---

## Executive Summary

At **11:00 PM ET**, Content Operations noticed that automatic post-game highlight packages were not being generated for games that concluded throughout the evening. The highlight generation pipeline had stopped processing games approximately 4 hours earlier. The issue affected **12 completed games** with no automated highlights available. The root cause was GPU resource exhaustion in the ML cluster used for play detection and highlight extraction. The ML Platform team cleared stuck jobs and scaled up GPU resources, restoring highlight generation by **3:30 AM ET** the following morning.

---

## Root Cause

The highlight generation pipeline uses ML models running on GPU instances to detect key plays (dunks, three-pointers, blocks, etc.) and extract highlight clips. A large batch of historical content reprocessing jobs was submitted during the day for a content archive project, consuming all available GPU resources. When live games concluded and needed highlight processing, no GPU capacity was available. Jobs queued indefinitely, and the timeout/retry logic did not properly handle resource exhaustion, causing jobs to remain stuck rather than failing gracefully.

---

## Issue Classification

- Resource Contention
- Job Priority Misconfiguration
- Timeout/Retry Logic Deficiency

---

## Recovery

The ML Platform team identified GPU resource exhaustion through cluster monitoring. They terminated the lower-priority historical reprocessing jobs and implemented a GPU resource reservation for live highlight processing. The stuck highlight jobs were restarted with priority scheduling. Highlights for the 12 affected games were generated overnight and available by morning.

**Operator Override Applied:** ML Platform terminated batch processing jobs and reserved GPU resources for live content (per ML Resource Contention runbook).

---

## Permanent Corrective Action

1. Implemented GPU resource quotas with priority classes (live > on-demand > batch)
2. Added job queue monitoring with alerting when live content jobs wait > 30 minutes
3. Created automatic preemption of batch jobs when live content is queued
4. Fixed timeout/retry logic to fail gracefully and alert on resource exhaustion
5. Established ML cluster capacity planning for concurrent live and batch workloads
6. Added highlights availability monitoring with SLA alerting

---

## Process Improvement

> **Implement ML Resource Priority Classes and Automatic Batch Job Preemption**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **2:00 PM** | Historical content reprocessing batch submitted |
| **3:00 PM** | Batch jobs consume all GPU resources |
| **7:00 PM** | First games conclude, highlight jobs submitted |
| **7:15 PM** | Highlight jobs queued, no GPU resources available |
| **11:00 PM** | Content Ops notices no highlights generated for evening games |
| **11:15 PM** | P2 declared. Highlights Platform team notified. |
| **11:30 PM** | GPU resource exhaustion identified |
| **11:45 PM** | Historical batch identified as resource consumer |
| **12:00 AM** | Decision made to terminate batch jobs |
| **12:15 AM** | Batch jobs terminated, GPU resources freed |
| **12:30 AM** | GPU reservation implemented for live highlights |
| **1:00 AM** | Stuck highlight jobs restarted with priority |
| **3:30 AM** | All 12 games highlights generated and available |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | All platforms (on-demand highlights) |
| **Issue Start Time** | 04/29/26 7:00 PM ET |
| **Issue End Time** | 04/30/26 3:30 AM ET |
| **Resolving Teams** | ML Platform, Highlights Platform, Content Operations |
| **Games Impacted** | 12 completed games |
| **Event** | NBA Regular Season |

---

## Resolution Description

Post-game highlights failed to generate due to GPU resource exhaustion from batch historical processing jobs. Live highlight processing jobs queued indefinitely with no resources available. The ML Platform team terminated batch jobs, implemented resource reservations, and reprocessed the backlogged games overnight. Post-incident, GPU resource priority classes and automatic batch preemption were implemented.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#content-operations-alerts]
>
> Post-game highlights not generating for completed games. 12 games finished tonight with no automated highlights. Highlight pipeline appears stuck. No jobs completing. Users expecting highlights not finding content. ML Platform and Highlights team please investigate pipeline status and resource availability.
