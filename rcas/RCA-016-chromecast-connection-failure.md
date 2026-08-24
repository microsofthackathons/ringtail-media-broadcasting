# [INC-20260402-016] 04/02/26 - P2 - Chromecast Streaming Failing to Connect

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 2 - Must Have |
| **Reporter** | Connected Devices Support |
| **Assignee** | Chromecast Integration Lead |
| **Resolution** | Resolved |
| **Components** | Chromecast Receiver, Cast SDK, Streaming Manifest |
| **Affects Versions** | Cast Receiver v3.1.0 |
| **Fix Versions** | Cast Receiver v3.1.1 |
| **Labels** | Chromecast, P2, Cast, Connected-Devices |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 2 hours 30 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 04/02/26 |
| **Updated** | 04/03/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260402-016 | Chromecast Connection Failure | Closed |
| Related | CHG-4912 | Cast Receiver DRM Update | Completed |

---

## Executive Summary

At **6:00 PM ET**, users attempting to cast live games to Chromecast devices experienced connection failures. The cast session would initiate but fail with a generic error before playback began. The issue affected approximately **45,000 Chromecast users**. The root cause was a DRM license server endpoint change that the Chromecast receiver was not updated to use. The Video Platform team updated the Cast receiver configuration to use the new DRM endpoint, restoring Chromecast functionality by **8:30 PM ET**.

---

## Root Cause

A planned migration of the DRM license server to a new endpoint was completed 72 hours prior. All platform players (iOS, Android, Web, Smart TVs) were updated during the migration. However, the Chromecast Cast receiver was managed by a separate deployment pipeline and was overlooked during the migration. When users attempted to cast, the receiver requested DRM licenses from the deprecated endpoint, which returned 404 errors, causing playback initialization to fail.

---

## Issue Classification

- Deployment Coordination Gap
- Migration Oversight
- System Inventory Incomplete

---

## Recovery

The Video Platform team identified the DRM endpoint mismatch by analyzing Cast receiver logs. The fix required updating the Chromecast receiver application hosted on Google's Cast infrastructure. An expedited deployment was pushed, and Google's Cast infrastructure propagated the update within 90 minutes. Users who restarted their Chromecast devices received the update immediately; others received it as Cast sessions were reestablished.

**Operator Override Applied:** NOC communicated via social media that users should restart Chromecast devices to expedite receiving the fix.

---

## Permanent Corrective Action

1. Added Chromecast receiver to infrastructure change management checklist
2. Created comprehensive system inventory including all third-party hosted components
3. Implemented DRM endpoint health monitoring across all platforms including Cast
4. Added automated testing for Chromecast receiver against DRM endpoints
5. Created deployment dependency mapping to prevent future migration oversights

---

## Process Improvement

> **Implement Comprehensive System Inventory and Third-Party Component Change Management**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **6:00 PM** | NBA games begin, Chromecast usage increases |
| **6:10 PM** | First reports of Chromecast connection failures |
| **6:30 PM** | Pattern identified: all Chromecast casts failing |
| **6:40 PM** | P2 declared. Video Platform team notified. |
| **7:00 PM** | Cast receiver logs show DRM 404 errors |
| **7:15 PM** | DRM endpoint mismatch identified |
| **7:25 PM** | Connection to recent DRM migration established |
| **7:40 PM** | Cast receiver update prepared with correct DRM endpoint |
| **7:55 PM** | Update submitted to Google Cast infrastructure |
| **8:10 PM** | Google begins propagating receiver update |
| **8:20 PM** | NOC posts device restart guidance on social channels |
| **8:30 PM** | Chromecast functionality restored. Incident resolved. |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | Chromecast devices |
| **Issue Start Time** | 04/02/26 6:00 PM ET |
| **Issue End Time** | 04/02/26 8:30 PM ET |
| **Resolving Teams** | Video Platform, Connected Devices, NOC |
| **Users Impacted** | ~45,000 |
| **Event** | Regular Season Games |

---

## Resolution Description

Chromecast casting failed because the Cast receiver was not updated during a DRM license server migration 72 hours prior. The receiver attempted to contact the deprecated DRM endpoint, causing playback failures. The Video Platform team deployed an updated Cast receiver to Google's infrastructure, restoring functionality within 2.5 hours. Post-incident, system inventory and change management processes were enhanced to include all third-party hosted components.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#connected-devices-support]
>
> Chromecast users unable to cast live games. Sessions initiate but fail before playback. Error message: "Unable to load video." All Chromecast models affected. NBA games in progress. Video Platform team please investigate Cast receiver and DRM handshake.
