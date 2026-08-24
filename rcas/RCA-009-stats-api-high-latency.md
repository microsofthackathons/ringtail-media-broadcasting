# [INC-20260217-009] 02/17/26 - P2 - Stats API High Latency Causing Delayed Game Updates

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 2 - Must Have |
| **Reporter** | Stats Platform Monitor |
| **Assignee** | Data Services Lead |
| **Resolution** | Resolved |
| **Components** | Stats API, Database Cluster, Query Optimizer |
| **Affects Versions** | Stats API v2.9.0 |
| **Fix Versions** | Stats API v2.9.1 |
| **Labels** | Stats, P2, Latency, Database |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 2 hours 30 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 02/17/26 |
| **Updated** | 02/18/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260217-009 | Stats API Latency | Closed |
| Related | CHG-4678 | Database Index Optimization | Completed |
| Related | PRB-928 | Query Pattern Analysis Implementation | In Progress |

---

## Executive Summary

At **8:00 PM ET** during a 6-game WNBA opening night slate, users began reporting delayed stats updates. Live scores were showing 15-30 seconds behind actual game action, and player statistics were updating inconsistently. The root cause was a missing database index on a frequently queried table that degraded query performance under high concurrent load. The DBA team added the missing index during live traffic, and the Data Services team increased cache TTLs to reduce database pressure, restoring normal latency by **10:30 PM ET**.

---

## Root Cause

A database schema migration 2 weeks prior added a new column to the game_events table for enhanced player tracking. The migration script did not include an index on this column, which became a filter condition in multiple high-frequency queries. Under normal load, query performance was acceptable (50-100ms). However, with 6 concurrent games generating thousands of events per minute, the full table scans caused query latency to spike to 2-5 seconds, well above the 200ms SLA. The stats refresh pipeline backed up, causing visible delays for users.

---

## Issue Classification

- Schema Migration Gap
- Performance Testing Deficiency
- Index Management Oversight

---

## Recovery

The DBA team identified the missing index through database query analyzer tools. Adding an index to a live production database required careful execution to avoid locking issues. The DBA used an online index creation approach that minimized blocking. Meanwhile, the Data Services team increased cache TTLs from 2 seconds to 10 seconds to reduce database query volume. Stats latency began improving immediately after the index was created, returning to normal levels within 30 minutes.

**Operator Override Applied:** NOC increased stats cache TTL via admin portal, reducing database load while permanent fix was applied.

---

## Permanent Corrective Action

1. Implemented mandatory index review for all schema migrations
2. Added performance testing requirement for migrations affecting high-traffic tables
3. Created automated index suggestion tool based on query patterns
4. Added database query latency monitoring with alerting at 150ms p99
5. Implemented weekly slow query analysis and optimization reviews

---

## Process Improvement

> **Implement Mandatory Index Review and Performance Testing for Schema Migrations**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **7:30 PM** | WNBA opening night games begin (6 concurrent) |
| **8:00 PM** | Stats API latency spike detected (p99 > 2 seconds) |
| **8:10 PM** | User reports of delayed scores and stats begin |
| **8:15 PM** | P2 incident declared. Data Services team notified. |
| **8:25 PM** | Database query analyzer shows full table scans |
| **8:40 PM** | Missing index on game_events.player_tracking_id identified |
| **8:55 PM** | NOC increases cache TTL to 10 seconds (per runbook) |
| **9:00 PM** | Stats latency improves slightly with caching |
| **9:15 PM** | DBA begins online index creation |
| **9:45 PM** | Index creation completes (2.1M rows indexed) |
| **10:00 PM** | Query latency returns to normal (p99 < 100ms) |
| **10:15 PM** | Cache TTL reverted to 2 seconds |
| **10:30 PM** | Incident resolved. Stats performing within SLA. |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | All platforms (stats overlay) |
| **Issue Start Time** | 02/17/26 8:00 PM ET |
| **Issue End Time** | 02/17/26 10:30 PM ET |
| **Resolving Teams** | Data Services, DBA Team, NOC |
| **Games Impacted** | 6 WNBA opening night games |
| **Event** | WNBA Season Opener |

---

## Resolution Description

Stats API latency degraded during WNBA opening night due to a missing database index on a recently added column. The full table scans caused queries to spike from 50ms to 2-5 seconds under concurrent game load. The NOC temporarily increased cache TTLs to reduce database pressure while the DBA team added the missing index. Post-incident, mandatory index review was added to the schema migration process.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#stats-platform-alerts]
>
> Stats API latency elevated during WNBA opening night. Users reporting scores 15-30 seconds behind. Stats overlay showing stale data. 6 concurrent games in progress. P99 latency at 2+ seconds (SLA: 200ms). Data Services and DBA teams please investigate database performance. NOC can adjust cache settings if requested.
