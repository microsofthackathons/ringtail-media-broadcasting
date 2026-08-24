# [INC-20260115-005] 01/15/26 - P1 - Live Game DVR/Rewind Functionality Complete Failure

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 1 - Critical |
| **Reporter** | QA Team Lead |
| **Assignee** | Video Platform Lead |
| **Resolution** | Resolved |
| **Components** | DVR Service, Segment Storage, Timeline API |
| **Affects Versions** | DVR Service v2.3.0 |
| **Fix Versions** | DVR Service v2.3.1 |
| **Labels** | DVR, P1, Live-Streaming, Critical-Feature |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 1 hour 12 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 01/15/26 |
| **Updated** | 01/16/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260115-005 | DVR Rewind Failure | Closed |
| Related | CHG-4489 | DVR Segment Storage Migration | Completed |
| Related | PRB-887 | DVR Service Health Check Enhancement | Completed |

---

## Executive Summary

At **8:02 PM ET** during EuroLeague Final Four coverage, users across all platforms reported complete inability to use DVR functionality (rewind, pause, resume) on live streams. The live feed continued playing but users could not access any content from earlier in the broadcast. The root cause was a failed storage migration for the DVR segment cache that left the service pointing to an empty storage account. The Video Platform team redirected the DVR service to the original storage location and initiated a segment backfill, restoring full DVR functionality by **9:14 PM ET**.

---

## Root Cause

A planned storage migration was scheduled to move DVR segment cache from legacy storage to a new high-performance storage account. The migration runbook specified a **5 AM ET** maintenance window, but an incorrect automation script timestamp triggered the storage cutover at **5 AM UTC** (12:00 AM ET). The cutover switched the DVR service to the new storage account before segment data had been migrated. When live games began, new segments were being written to the new storage, but the segment index still referenced the old storage location. This mismatch caused all DVR requests to fail. The migration team was unaware of the premature cutover until user reports surfaced.

---

## Issue Classification

- Automation Script Error
- Migration Process Failure
- Timezone Handling Deficiency

---

## Recovery

The Video Platform team identified the storage mismatch by comparing DVR service logs with storage metrics. The immediate fix was to update the DVR service configuration to point back to the original storage account that still contained the segment data. Additionally, the team initiated an emergency segment sync to copy new segments (written to the new storage during the outage) back to the original storage. DVR functionality was restored for new playback requests at **8:55 PM ET**, with full historical content availability by **9:14 PM ET**.

---

## Permanent Corrective Action

1. All automation scripts updated to use explicit timezone specifications (e.g., "05:00 America/New_York")
2. Migration runbooks now require pre-migration validation step confirming data integrity at destination
3. Added DVR health check that validates segment storage accessibility before accepting traffic
4. Implemented storage cutover circuit breaker requiring minimum data sync threshold (95%) before switching
5. Created DVR emergency failback runbook with pre-authorized procedures
6. Added monitoring for storage account segment count mismatches

---

## Process Improvement

> **Standardize Timezone Handling in All Automation Scripts and Migration Runbooks**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **12:00 AM** | Automation script triggers premature storage cutover (should have been 5 AM ET) |
| **7:30 PM** | EuroLeague Final Four coverage begins |
| **7:45 PM** | Live streams functioning normally, DVR writes going to new (empty) storage |
| **8:02 PM** | First user reports of DVR rewind not working |
| **8:08 PM** | NOC confirms DVR failure across all platforms. P1 declared. |
| **8:12 PM** | Bridge opened. Video Platform team joins. |
| **8:25 PM** | Storage mismatch identified - DVR pointing to empty new storage |
| **8:30 PM** | Root cause identified - premature migration cutover at 12 AM |
| **8:38 PM** | Decision made to revert DVR config to original storage |
| **8:45 PM** | DVR service configuration updated. Service restarting. |
| **8:55 PM** | DVR functionality restored for new playback requests |
| **9:05 PM** | Emergency segment sync completing for content written during outage |
| **9:14 PM** | Full DVR functionality restored including all historical content |
| **9:20 PM** | Incident marked resolved. |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | All platforms |
| **Issue Start Time** | 01/15/26 8:02 PM ET |
| **Issue End Time** | 01/15/26 9:14 PM ET |
| **Resolving Teams** | Video Platform, Storage Engineering, NOC |
| **Viewers Impacted** | ~650,000 attempting DVR operations |
| **Event** | EuroLeague Final Four |

---

## Resolution Description

The DVR failure was caused by a timezone error in a migration automation script that triggered storage cutover at 5 AM UTC instead of 5 AM ET, 8 hours before the planned maintenance window. This left the DVR service pointing to an empty storage account while live games were in progress. The Video Platform team reverted the DVR configuration to the original storage location, restoring functionality within 72 minutes. Post-incident, all automation scripts were updated to use explicit timezone specifications.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#video-platform-alerts]
>
> CRITICAL: DVR functionality completely broken for all live streams. Users cannot rewind, pause, or resume. Timeline scrubber shows no available content. Live playback continues but any DVR operation fails. EuroLeague Final Four games in progress. Video Platform and Storage teams please join bridge immediately. This is a critical feature for our premium subscribers.
