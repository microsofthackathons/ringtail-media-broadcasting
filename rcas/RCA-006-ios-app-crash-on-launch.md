# [INC-20260122-006] 01/22/26 - P2 - iOS App Crash on Launch for Version 8.4.2

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 2 - Must Have |
| **Reporter** | Mobile Support Team |
| **Assignee** | iOS Platform Lead |
| **Resolution** | Resolved |
| **Components** | iOS App, Authentication SDK, App Launch Flow |
| **Affects Versions** | iOS App v8.4.2 |
| **Fix Versions** | iOS App v8.4.3 |
| **Labels** | iOS, App-Crash, P2, Mobile |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 2 hours 45 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 01/22/26 |
| **Updated** | 01/23/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260122-006 | iOS App Crash on Launch | Closed |
| Related | CHG-4534 | iOS Emergency App Store Release | Completed |

---

## Executive Summary

At **6:30 PM ET**, customer support began receiving elevated reports of the iOS app crashing immediately upon launch for users who had updated to version 8.4.2. The issue affected approximately **45,000 users** who updated within the first 2 hours of the release. The root cause was a null pointer exception in the authentication SDK initialization when users had previously logged out before the update. The Mobile team pushed an emergency App Store release (v8.4.3) and enabled a server-side feature flag to bypass the problematic code path. Users with automatic updates received the fix within 4 hours.

---

## Root Cause

Version 8.4.2 introduced a new authentication caching mechanism to improve login performance. The new code assumed that a cached user token file would always exist, even for logged-out users. When users who had previously logged out updated to 8.4.2, the missing token file triggered a null pointer exception during app initialization, causing an immediate crash. The issue was not caught in QA because test devices always had existing tokens from test accounts.

---

## Issue Classification

- Code Defect
- Testing Gap
- Edge Case Handling

---

## Recovery

The iOS team identified the crash signature in crash reporting tools within 30 minutes. A hotfix was developed adding null checks to the token file handling. While waiting for App Store review (expedited), the backend team enabled a feature flag to disable the new authentication caching, allowing affected users who force-quit and relaunched to successfully open the app. App Store approved v8.4.3 within 3 hours. Users with automatic updates received the fix overnight.

**Operator Override Applied:** Backend feature flag disabled new auth caching, providing immediate workaround while permanent fix propagated through App Store.

---

## Permanent Corrective Action

1. Added QA test case: "Fresh install with logged-out state prior to update"
2. Implemented defensive null checks for all file-based caching mechanisms
3. Added pre-release smoke testing on devices with various authentication states
4. Created crash rate monitoring dashboard with automatic alerting for new releases
5. Implemented staged rollout for App Store releases (10% → 50% → 100%)

---

## Process Improvement

> **Implement Staged App Store Rollouts and Authentication State Matrix Testing**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **4:00 PM** | iOS v8.4.2 released to App Store |
| **6:30 PM** | Customer support reports elevated crash complaints |
| **6:45 PM** | NOC confirms crash spike in monitoring. P2 declared. |
| **6:50 PM** | iOS team begins investigating crash reports |
| **7:15 PM** | Null pointer exception in auth SDK identified as root cause |
| **7:30 PM** | Hotfix development begins |
| **7:45 PM** | Backend team enables feature flag to bypass problematic code |
| **8:00 PM** | Users who force-quit can now successfully launch app |
| **8:15 PM** | Hotfix (v8.4.3) submitted to App Store with expedited review request |
| **9:00 PM** | App Store expedited review approved |
| **9:15 PM** | v8.4.3 available for download |
| **11:00 PM** | Automatic updates begin rolling out to affected users |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | iOS only |
| **Issue Start Time** | 01/22/26 4:00 PM ET (release time) |
| **Issue End Time** | 01/22/26 9:15 PM ET (fix available) |
| **Resolving Teams** | iOS Mobile Team, Backend Platform, NOC |
| **Users Impacted** | ~45,000 |
| **Event** | Regular Season Games |

---

## Resolution Description

The iOS app crash was caused by a null pointer exception in new authentication caching code that failed to handle the case of users who were logged out before updating. The iOS team pushed an emergency hotfix through App Store expedited review while the backend team enabled a feature flag workaround for immediate relief. Post-incident, staged rollout procedures and enhanced authentication state testing were implemented.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#mobile-support-escalations]
>
> URGENT: Elevated crash reports for iOS app. Users reporting immediate crash on launch after updating to v8.4.2. Crash rate spike visible in monitoring. Approximately 45K affected users. Games tonight at 7:30 PM. iOS team please investigate crash signatures and identify root cause.
