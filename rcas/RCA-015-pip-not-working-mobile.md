# [INC-20260325-015] 03/25/26 - P2 - Picture-in-Picture Not Working on Mobile Apps

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 2 - Must Have |
| **Reporter** | Mobile QA Team |
| **Assignee** | Mobile Platform Lead |
| **Resolution** | Resolved |
| **Components** | iOS App, Android App, PiP Implementation |
| **Affects Versions** | iOS v8.5.0, Android v7.10.0 |
| **Fix Versions** | iOS v8.5.1, Android v7.10.1 |
| **Labels** | Mobile, P2, PiP, Feature-Failure |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 4 hours |
| **Original Estimate** | Not Specified |
| **Created** | 03/25/26 |
| **Updated** | 03/26/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260325-015 | PiP Feature Failure | Closed |
| Related | CHG-4892 | PiP Entitlement Flag Fix | Completed |

---

## Executive Summary

At **3:00 PM ET**, users on both iOS and Android reported that Picture-in-Picture (PiP) mode was not activating when leaving the app during live streams. The feature that allows users to watch games in a floating window while using other apps had stopped working. The issue affected approximately **150,000 users** who attempted to use PiP during the afternoon. The root cause was a backend feature flag change that inadvertently disabled PiP entitlement checks. The Platform team corrected the feature flag configuration, restoring PiP functionality by **7:00 PM ET**.

---

## Root Cause

A backend configuration change was made to update premium subscription tier benefits. The change introduced a new entitlement flag structure that replaced the legacy format. The PiP feature checked for the old flag format (`pip_enabled: true`) which no longer existed in the response. Without a valid entitlement, the mobile apps silently disabled PiP functionality. The configuration change was tested only for the new features being added, not for regression on existing features like PiP.

---

## Issue Classification

- Configuration Change Side Effect
- Regression Testing Gap
- Feature Flag Migration Issue

---

## Recovery

The Mobile team identified that PiP was receiving "not entitled" responses from the entitlement API. The Backend team traced this to the recent flag structure change. The immediate fix was to add backwards compatibility by including both old and new flag formats in the entitlement response. This required no mobile app update and took effect immediately for all users.

**Operator Override Applied:** Backend team added backwards-compatible flag format, providing immediate fix without mobile app release cycle.

---

## Permanent Corrective Action

1. Implemented feature flag deprecation policy requiring 30-day overlap period
2. Added regression test suite covering all premium feature entitlements
3. Created feature flag change review process requiring feature owner sign-off
4. Implemented entitlement response validation monitoring
5. Added mobile app telemetry for feature activation failures

---

## Process Improvement

> **Implement Feature Flag Deprecation Policy and Entitlement Regression Testing**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **11:00 AM** | Backend configuration change deployed (new entitlement format) |
| **3:00 PM** | Afternoon games begin, PiP usage attempts increase |
| **3:15 PM** | First user complaints about PiP not working |
| **3:30 PM** | Mobile QA confirms PiP failure on both platforms |
| **3:45 PM** | P2 declared. Mobile and Backend teams notified. |
| **4:00 PM** | Mobile team identifies "not entitled" response from API |
| **4:30 PM** | Backend team traces to entitlement flag change |
| **5:00 PM** | Root cause confirmed: old flag format removed |
| **5:30 PM** | Backwards-compatible flag format developed |
| **6:00 PM** | Configuration update deployed to production |
| **6:15 PM** | PiP functionality begins restoring (no app update needed) |
| **7:00 PM** | Full PiP functionality confirmed. Incident resolved. |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | iOS and Android |
| **Issue Start Time** | 03/25/26 11:00 AM ET |
| **Issue End Time** | 03/25/26 7:00 PM ET |
| **Resolving Teams** | Mobile Team, Backend Platform, NOC |
| **Users Impacted** | ~150,000 |
| **Event** | March Madness Sweet Sixteen |

---

## Resolution Description

Picture-in-Picture functionality failed due to a backend configuration change that removed legacy entitlement flag format. Mobile apps relied on the old format and received "not entitled" responses, silently disabling PiP. The Backend team added backwards compatibility to the entitlement API, providing an immediate fix without requiring mobile app updates. Post-incident, feature flag deprecation policies and entitlement regression testing were implemented.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#mobile-platform-alerts]
>
> Picture-in-Picture not working on iOS and Android. Users cannot watch in floating window when leaving the app. March Madness games in progress. Premium feature affecting paying subscribers. Mobile and Backend teams please investigate PiP entitlement responses.
