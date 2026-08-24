# [INC-20260205-002] 02/05/26 - P1 - Stats Overlay System Failure During Multiple Live Games

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 1 - Critical |
| **Reporter** | Stats Platform Engineer |
| **Assignee** | Data Services Lead |
| **Resolution** | Resolved |
| **Components** | Stats API, Data Pipeline, Overlay Renderer |
| **Affects Versions** | Stats Service v2.8.0 |
| **Fix Versions** | Stats Service v2.8.1 |
| **Labels** | Stats, Multi-Game, P1, Data-Pipeline |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 1 hour 23 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 02/05/26 |
| **Updated** | 02/06/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260205-002 | Stats Overlay System Failure | Closed |
| Related | CHG-4687 | Stats Message Queue Capacity Upgrade | Completed |
| Related | PRB-923 | Stats Pipeline Rate Limiting Implementation | In Progress |

---

## Executive Summary

At **7:12 PM ET** on a Thursday night with **8 concurrent NBA games**, the stats overlay system failed across all live streams. Viewers saw either frozen stats, missing player information, or placeholder text ("--" for all statistics). The root cause was a message queue overflow in the stats ingestion pipeline caused by a bulk historical data sync that was inadvertently triggered during peak live game hours. The Data Services team disabled the historical sync job and increased message queue capacity, restoring full stats functionality by **8:35 PM ET**.

---

## Root Cause

A scheduled job designed to sync historical player statistics from the league's official data provider was misconfigured with an incorrect cron expression. Instead of running at **3:00 AM ET** during low-traffic hours, the job was set to run at **3:00 PM ET**. The job had a 4-hour runtime, meaning it was processing bulk historical data during the **7:00 PM - 11:00 PM** prime time window when 8 live games were being broadcast. The message queue (RabbitMQ) for stats ingestion became overwhelmed, causing real-time stats updates to be dropped. The overlay service received stale or empty data, resulting in frozen/missing stats across all platforms.

---

## Issue Classification

- Configuration Error
- Job Scheduling Deficiency
- Capacity Planning Gap

---

## Recovery

The Data Services team identified the runaway historical sync job and terminated it immediately. The NOC executed the Stats API failover runbook, switching to the backup stats cache while the message queue drained. The Platform team increased RabbitMQ message queue capacity from 100,000 to 500,000 messages and added rate limiting for batch operations. Stats overlays began recovering at **8:20 PM ET** with full restoration by **8:35 PM ET**.

---

## Permanent Corrective Action

1. Implemented job scheduling validation requiring peer review for any changes to production cron expressions
2. Added rate limiting for all batch/historical sync jobs (max 1,000 records/second)
3. Increased message queue capacity with auto-scaling based on queue depth
4. Created production job scheduling blackout windows during prime time (6 PM - 12 AM ET)
5. Added real-time monitoring dashboard showing batch job impact on live stats pipeline
6. Implemented circuit breaker pattern to isolate batch jobs from real-time feeds

---

## Process Improvement

> **Implement Production Job Scheduling Governance and Prime Time Blackout Windows**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **3:00 PM** | Historical stats sync job inadvertently triggers (should have been 3:00 AM) |
| **6:45 PM** | First NBA games of the evening tip off |
| **7:00 PM** | Message queue depth reaches 80,000 messages (normal: ~5,000) |
| **7:12 PM** | Stats overlay failures detected across multiple streams. NOC alerted. |
| **7:15 PM** | P1 incident declared. Bridge opened. |
| **7:22 PM** | Data Services joins bridge, begins investigating stats pipeline |
| **7:35 PM** | Historical sync job identified as cause. Job terminated. |
| **7:40 PM** | NOC executes Stats API failover to backup cache (per runbook) |
| **7:55 PM** | Message queue begins draining, depth decreasing |
| **8:10 PM** | Queue depth returns to normal levels |
| **8:20 PM** | Stats overlays begin recovering, some games restored |
| **8:35 PM** | Full stats functionality restored across all live games |
| **8:45 PM** | Incident marked resolved. Post-incident review scheduled. |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | All platforms (iOS, Android, Web, Smart TV) |
| **Issue Start Time** | 02/05/26 7:12 PM ET |
| **Issue End Time** | 02/05/26 8:35 PM ET |
| **Resolving Teams** | Data Services, Platform Engineering, NOC |
| **Games Impacted** | 8 concurrent NBA games |
| **Event** | Regular Season Thursday Night Slate |

---

## Resolution Description

The stats overlay failure was caused by a misconfigured historical data sync job that ran during prime time instead of overnight. The job overwhelmed the message queue responsible for real-time stats ingestion, causing stats updates to be dropped across all 8 concurrent live games. The Data Services team terminated the job and the NOC executed the stats failover runbook to restore functionality. New governance policies were implemented to prevent batch jobs from running during prime time hours.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#stats-platform-alerts]
>
> URGENT: Stats overlay showing frozen/missing data across ALL live games tonight. 8 games currently in progress. Seeing "--" placeholders for player stats, score not updating. Stats API health check shows message queue at critical depth. Data Services and Platform Engineering please join bridge.
