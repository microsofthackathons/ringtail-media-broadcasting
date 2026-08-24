# [INC-20260315-013] 03/15/26 - P2 - Closed Captioning Not Displaying on Any Platform

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 2 - Must Have |
| **Reporter** | Accessibility Team |
| **Assignee** | Caption Services Lead |
| **Resolution** | Resolved |
| **Components** | Caption Service, WebVTT Processing, Accessibility |
| **Affects Versions** | Caption Service v1.8.0 |
| **Fix Versions** | Caption Service v1.8.1 |
| **Labels** | Accessibility, P2, Captions, All-Platforms |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 1 hour 30 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 03/15/26 |
| **Updated** | 03/16/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260315-013 | Closed Captioning Failure | Closed |
| Related | CHG-4823 | Caption Service Recovery | Completed |

---

## Executive Summary

At **8:00 PM ET** during NCAA Tournament coverage, closed captions stopped displaying across all platforms. Users who rely on captions for accessibility were unable to follow game commentary. The issue affected approximately **35,000 users** with caption preferences enabled. The root cause was a caption processing service that lost connectivity to the live caption feed from the broadcast partner. The Caption Services team reestablished the connection and implemented a failover to the backup caption source, restoring captions by **9:30 PM ET**.

---

## Root Cause

The live caption feed is received from the broadcast partner over a dedicated network connection. A network maintenance event at the partner's facility caused a brief (90-second) connectivity interruption. Normally, the caption service would reconnect automatically. However, a bug in the reconnection logic caused the service to enter an invalid state where it believed it was connected but was not receiving data. The service continued running without error, but no captions were being processed or distributed.

---

## Issue Classification

- Third-Party Dependency
- Reconnection Logic Bug
- Monitoring Gap (No Caption Data Flow Alert)

---

## Recovery

The Caption Services team identified the connectivity issue through network monitoring and caption service logs. They manually restarted the caption service to reset the connection state. While waiting for the primary feed to stabilize, they also activated the backup caption source (secondary broadcast partner feed). Captions began appearing within minutes of the service restart.

**Operator Override Applied:** NOC activated backup caption feed (per Accessibility Runbook) while primary feed was being diagnosed.

---

## Permanent Corrective Action

1. Fixed reconnection logic bug to properly detect disconnected state
2. Implemented "caption data flow" monitoring with alerting when no captions received for 60 seconds
3. Added automatic failover to backup caption source after 2 minutes of no data
4. Created caption service health check that validates actual data flow, not just connection state
5. Established regular testing of backup caption feed during non-critical hours

---

## Process Improvement

> **Implement Caption Data Flow Monitoring and Automatic Failover to Backup Caption Source**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **7:45 PM** | Broadcast partner network maintenance begins (unannounced) |
| **7:46 PM** | Caption feed connectivity briefly interrupted |
| **7:48 PM** | Caption service enters invalid "connected but not receiving" state |
| **8:00 PM** | Users begin reporting missing captions |
| **8:15 PM** | Accessibility team escalates. P2 declared. |
| **8:25 PM** | Caption Services team investigates service logs |
| **8:35 PM** | Invalid connection state identified |
| **8:45 PM** | NOC activates backup caption feed (per runbook) |
| **8:55 PM** | Caption service manually restarted |
| **9:00 PM** | Captions begin appearing from backup feed |
| **9:15 PM** | Primary caption feed restored and verified |
| **9:30 PM** | Full caption functionality confirmed. Incident resolved. |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | All platforms |
| **Issue Start Time** | 03/15/26 7:48 PM ET |
| **Issue End Time** | 03/15/26 9:30 PM ET |
| **Resolving Teams** | Caption Services, Accessibility Team, NOC |
| **Users Impacted** | ~35,000 (users with captions enabled) |
| **Event** | NCAA Tournament First Round |

---

## Resolution Description

Closed captioning failed due to a caption service bug that entered an invalid state after a brief upstream connectivity interruption. The service believed it was connected but was not receiving caption data. The NOC activated the backup caption feed while Caption Services restarted the primary service. Post-incident, caption data flow monitoring and automatic failover were implemented.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#accessibility-alerts]
>
> URGENT: Closed captions not displaying on any platform. NCAA Tournament games in progress. Accessibility issue affecting deaf and hard-of-hearing viewers. Caption service shows "healthy" but no captions appearing. Caption Services team please investigate data flow from broadcast partner.
