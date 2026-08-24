# [INC-20260415-018] 04/15/26 - P2 - Video Quality Stuck on Low Bitrate

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 2 - Must Have |
| **Reporter** | Quality Engineering |
| **Assignee** | Video Platform Lead |
| **Resolution** | Resolved |
| **Components** | ABR Algorithm, Quality Selection, Bandwidth Estimation |
| **Affects Versions** | Player Core v4.5.0 |
| **Fix Versions** | ABR Config Update |
| **Labels** | Video-Quality, P2, ABR, All-Platforms |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 1 hour 50 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 04/15/26 |
| **Updated** | 04/16/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260415-018 | ABR Quality Stuck Issue | Closed |
| Related | CHG-4967 | ABR Algorithm Parameter Fix | Completed |

---

## Executive Summary

At **8:00 PM ET** during NBA Play-In Tournament games, users across all platforms reported video quality stuck at the lowest resolution (480p) despite having fast internet connections. The ABR (Adaptive Bitrate) algorithm was not upgrading quality even with abundant bandwidth. The issue affected approximately **340,000 viewers** watching Play-In games. The root cause was an ABR configuration parameter that was incorrectly set during a morning deployment, causing the algorithm to be overly conservative. The Video Platform team corrected the configuration, restoring normal quality adaptation by **9:50 PM ET**.

---

## Root Cause

A deployment that morning introduced new ABR parameters for improved buffering stability. One parameter, `quality_upgrade_bandwidth_threshold`, was accidentally set to `0.95` (95% confidence required) instead of `0.75` (75% confidence). This meant the algorithm required 95% certainty that bandwidth could sustain the higher quality before upgrading, a threshold rarely achieved in real-world conditions with network variability. As a result, the algorithm remained stuck at the initial low bitrate for nearly all users.

---

## Issue Classification

- Configuration Error
- Deployment Validation Gap
- ABR Parameter Misconfiguration

---

## Recovery

The Video Platform team identified the issue by comparing ABR telemetry between affected users and baseline metrics. The bandwidth measurements were normal, but quality upgrade events were near zero. The misconfigured threshold parameter was discovered in the deployment diff. A server-side configuration update corrected the parameter to 0.75. The change took effect within 5 minutes as players refreshed ABR configuration, and quality began adapting normally.

**Operator Override Applied:** Server-side ABR configuration update deployed without app update required, providing immediate fix.

---

## Permanent Corrective Action

1. Added ABR parameter validation to deployment pipeline (range checks)
2. Created ABR behavior baseline testing that verifies quality upgrades occur
3. Implemented real-time ABR quality distribution dashboard with alerting
4. Added peer review requirement for ABR algorithm parameter changes
5. Created automated canary testing that measures quality upgrade rates post-deployment

---

## Process Improvement

> **Implement ABR Parameter Validation and Quality Distribution Monitoring**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **9:00 AM** | Deployment with new ABR parameters pushed to production |
| **7:30 PM** | Play-In Tournament games begin |
| **8:00 PM** | User complaints about blurry/pixelated video begin |
| **8:15 PM** | Quality Engineering confirms 480p cap across platforms |
| **8:25 PM** | P2 declared. Video Platform team notified. |
| **8:40 PM** | ABR telemetry shows zero quality upgrade events |
| **8:55 PM** | Bandwidth measurements confirmed normal |
| **9:10 PM** | Morning deployment identified as potential cause |
| **9:25 PM** | Misconfigured threshold parameter (0.95 vs 0.75) found |
| **9:35 PM** | ABR configuration fix prepared |
| **9:40 PM** | Configuration update deployed |
| **9:50 PM** | Quality adaptation restored. Users see HD quality. |
| **10:00 PM** | Incident resolved. |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | All platforms |
| **Issue Start Time** | 04/15/26 8:00 PM ET |
| **Issue End Time** | 04/15/26 9:50 PM ET |
| **Resolving Teams** | Video Platform, Quality Engineering, NOC |
| **Users Impacted** | ~340,000 |
| **Event** | NBA Play-In Tournament |

---

## Resolution Description

Video quality was stuck at 480p due to an ABR configuration error that set the quality upgrade threshold too high (95% instead of 75%). The algorithm could not achieve the required confidence level for upgrading, leaving users at low resolution. A server-side configuration fix corrected the parameter immediately. Post-incident, ABR parameter validation and quality distribution monitoring were implemented.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#video-platform-alerts]
>
> Video quality extremely poor for Play-In Tournament games. Users reporting pixelated, blurry streams. Quality appears stuck at 480p even for users with fast connections. ABR not upgrading bitrate. All platforms affected. Video Platform team please investigate ABR algorithm behavior.
