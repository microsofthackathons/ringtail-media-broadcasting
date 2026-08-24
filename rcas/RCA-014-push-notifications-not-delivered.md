# [INC-20260320-014] 03/20/26 - P2 - Game Start Push Notifications Not Delivered

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 2 - Must Have |
| **Reporter** | Engagement Team |
| **Assignee** | Notifications Lead |
| **Resolution** | Resolved |
| **Components** | Push Notification Service, Schedule API, APNS/FCM |
| **Affects Versions** | Notification Service v2.4.0 |
| **Fix Versions** | Notification Service v2.4.1 |
| **Labels** | Notifications, P2, Push, Engagement |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 2 hours 15 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 03/20/26 |
| **Updated** | 03/21/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260320-014 | Push Notifications Not Delivered | Closed |
| Related | CHG-4856 | Notification Queue Scaling | Completed |

---

## Executive Summary

At **7:00 PM ET**, game start notifications failed to deliver for 8 NBA games tipping off simultaneously. Approximately **1.2 million users** with notifications enabled did not receive their scheduled game alerts. The root cause was a notification queue backup caused by a surge in subscription validation requests that exhausted the notification service's connection pool to the user preference database. The Notifications team cleared the queue backlog and scaled up database connections, restoring notification delivery by **9:15 PM ET**.

---

## Root Cause

The notification service validates user subscription preferences before sending each push notification. When 8 games tipped off simultaneously, the service attempted to validate and send approximately 1.2 million notifications within a 5-minute window. The preference database connection pool (sized for 100 concurrent connections) was exhausted, causing validation queries to queue. As the queue grew, notifications missed their delivery windows and were eventually discarded by the "stale notification" cleanup job (configured to drop notifications older than 15 minutes).

---

## Issue Classification

- Capacity Planning Gap
- Connection Pool Sizing
- Burst Traffic Handling

---

## Recovery

The Notifications team identified the connection pool exhaustion through database monitoring. They increased the connection pool from 100 to 500 connections and disabled the stale notification cleanup job temporarily. The backlogged notifications were reprocessed with "game in progress" messaging instead of "game starting now." For users whose original notifications had already been discarded, a follow-up notification was sent manually.

**Operator Override Applied:** NOC disabled stale notification cleanup job to preserve queued notifications during recovery.

---

## Permanent Corrective Action

1. Increased preference database connection pool to 500 connections
2. Implemented notification batching for simultaneous game starts
3. Added connection pool utilization monitoring with 70% alerting threshold
4. Extended stale notification window from 15 to 30 minutes
5. Created pre-computation of subscriber lists 15 minutes before scheduled notifications
6. Implemented queue depth monitoring with automatic scaling triggers

---

## Process Improvement

> **Implement Notification Pre-computation and Connection Pool Auto-scaling**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **6:45 PM** | Notification service begins processing game start alerts |
| **6:50 PM** | Surge of 1.2M notifications enters validation queue |
| **6:55 PM** | Connection pool reaches 100% utilization |
| **7:00 PM** | Games tip off. Zero notifications delivered. |
| **7:05 PM** | NOC notices notification delivery rate at 0 |
| **7:10 PM** | P2 declared. Notifications team joins investigation. |
| **7:25 PM** | Connection pool exhaustion identified as root cause |
| **7:35 PM** | Database connection pool increase initiated |
| **7:45 PM** | NOC disables stale notification cleanup |
| **8:00 PM** | Connection pool scaled to 500, queue beginning to drain |
| **8:30 PM** | Backlogged notifications processing with updated messaging |
| **9:00 PM** | Follow-up notifications sent for discarded alerts |
| **9:15 PM** | All queued notifications delivered. Incident resolved. |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | iOS and Android push notifications |
| **Issue Start Time** | 03/20/26 7:00 PM ET |
| **Issue End Time** | 03/20/26 9:15 PM ET |
| **Resolving Teams** | Notifications Team, DBA Team, NOC |
| **Users Impacted** | ~1,200,000 |
| **Event** | NBA Regular Season (8 concurrent tip-offs) |

---

## Resolution Description

Game start notifications failed due to database connection pool exhaustion when 8 games tipped off simultaneously. The notification service could not validate user preferences fast enough, causing notifications to queue and eventually be discarded as stale. The team scaled database connections and reprocessed the backlog. Post-incident, notification pre-computation and auto-scaling were implemented.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#notifications-platform]
>
> Game start notifications not delivered for 7 PM tip-offs. 8 games started with zero alerts sent. Notification queue depth extremely high. Users missing game starts. Notifications and DBA teams please investigate database connectivity and queue status. NOC can disable cleanup jobs if requested.
