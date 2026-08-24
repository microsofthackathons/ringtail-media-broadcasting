# [INC-20260408-017] 04/08/26 - P2 - AirPlay Streaming Disconnecting Repeatedly

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 2 - Must Have |
| **Reporter** | iOS Support Team |
| **Assignee** | iOS Platform Lead |
| **Resolution** | Resolved |
| **Components** | iOS App, AirPlay Integration, AVPlayer |
| **Affects Versions** | iOS App v8.5.2 |
| **Fix Versions** | iOS App v8.5.3 |
| **Labels** | AirPlay, P2, iOS, Streaming |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 3 hours 45 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 04/08/26 |
| **Updated** | 04/09/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260408-017 | AirPlay Disconnection Issue | Closed |
| Related | CHG-4934 | iOS AirPlay Session Handling Fix | Completed |

---

## Executive Summary

At **7:30 PM ET**, iOS users reported that AirPlay streaming to Apple TV and AirPlay-compatible TVs disconnected repeatedly during live games. Sessions would drop every 3-5 minutes and sometimes fail to reconnect. The issue affected approximately **28,000 users** streaming via AirPlay. The root cause was an iOS app update that incorrectly handled AirPlay session keep-alive signals during advertisement breaks. The iOS team pushed a backend configuration change to modify ad break handling, providing immediate relief while a permanent app fix was prepared.

---

## Root Cause

iOS app version 8.5.2 introduced improved advertisement tracking. The new code modified how the AVPlayer handled content transitions during ad breaks. When streaming via AirPlay, the session keep-alive mechanism was inadvertently suspended during ad transitions. If the ad break exceeded 15 seconds (the AirPlay keep-alive timeout), the session would disconnect. Since most ad breaks are 30-90 seconds, nearly all AirPlay users experienced disconnections during commercial breaks.

---

## Issue Classification

- Code Defect (Ad Handling)
- AirPlay Session Management
- Testing Gap (AirPlay + Ads)

---

## Recovery

The iOS team identified the correlation between ad breaks and disconnections through user session telemetry. The immediate mitigation was a backend configuration change that shortened ad pods to 10-second micro-breaks for AirPlay sessions, keeping them under the keep-alive timeout. This provided immediate relief without requiring an app update. A permanent fix was developed that maintains AirPlay keep-alive signals during ad transitions.

**Operator Override Applied:** Backend team modified ad pod configuration for AirPlay sessions, providing immediate workaround while app fix was prepared.

---

## Permanent Corrective Action

1. Fixed AirPlay session keep-alive handling during ad transitions
2. Added AirPlay-specific test scenarios to ad integration test suite
3. Implemented AirPlay session monitoring with disconnection alerting
4. Created ad break duration limits per streaming protocol
5. Added automated AirPlay testing to release validation pipeline

---

## Process Improvement

> **Implement Streaming Protocol-Specific Testing for Ad Integration Changes**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **7:30 PM** | NBA games tip off, AirPlay usage increases |
| **7:45 PM** | First reports of AirPlay disconnections |
| **8:00 PM** | Pattern identified: disconnections during ad breaks |
| **8:10 PM** | P2 declared. iOS team begins investigation. |
| **8:30 PM** | Telemetry shows disconnections correlated with ad pods |
| **8:50 PM** | iOS 8.5.2 ad handling changes identified as cause |
| **9:15 PM** | Backend team prepares ad pod duration modification |
| **9:30 PM** | Short ad pod config deployed for AirPlay sessions |
| **9:45 PM** | AirPlay disconnections decrease significantly |
| **10:15 PM** | Permanent app fix development begins |
| **11:15 PM** | Incident resolved with backend workaround in place |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | iOS AirPlay to Apple TV and AirPlay TVs |
| **Issue Start Time** | 04/08/26 7:30 PM ET |
| **Issue End Time** | 04/08/26 11:15 PM ET |
| **Resolving Teams** | iOS Team, Ad Operations, Backend Platform |
| **Users Impacted** | ~28,000 |
| **Event** | NBA Playoff Race Games |

---

## Resolution Description

AirPlay streaming disconnections were caused by iOS app changes that suspended AirPlay session keep-alive signals during advertisement transitions. When ad breaks exceeded the 15-second keep-alive timeout, sessions disconnected. The Backend team implemented a workaround by shortening ad pods for AirPlay sessions while a permanent iOS fix was developed. Post-incident, streaming protocol-specific testing was added for ad integration changes.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#ios-platform-alerts]
>
> AirPlay users reporting frequent disconnections during live games. Sessions drop every few minutes. Appears to happen during commercials. Games in progress with playoff implications. iOS team please investigate AirPlay session handling. Ad Operations please stand by for potential ad config changes.
