# [INC-20260203-007] 02/03/26 - P2 - Android Excessive Buffering on 5G Networks

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 2 - Must Have |
| **Reporter** | NOC Engineer |
| **Assignee** | Android Platform Lead |
| **Resolution** | Resolved |
| **Components** | Android App, Adaptive Bitrate Logic, Network Detection |
| **Affects Versions** | Android App v7.9.1 |
| **Fix Versions** | Android App v7.9.2 |
| **Labels** | Android, Buffering, P2, 5G, ABR |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 4 hours |
| **Original Estimate** | Not Specified |
| **Created** | 02/03/26 |
| **Updated** | 02/04/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260203-007 | Android Buffering on 5G | Closed |
| Related | CHG-4612 | ABR Algorithm Update for 5G Networks | Completed |

---

## Executive Summary

Starting **7:00 PM ET**, Android users on 5G networks reported excessive buffering during live NBA games. The issue affected approximately **120,000 users** across multiple carriers. The root cause was an adaptive bitrate (ABR) algorithm bug that misclassified 5G network conditions, requesting bitrates higher than the actual throughput. The Android team pushed a server-side ABR configuration update that reduced initial bitrate requests for 5G connections, while a permanent app fix was prepared for the next release.

---

## Root Cause

The ABR algorithm used network type detection to set initial bitrate targets. The code mapped 5G connections to an aggressive bitrate profile (25 Mbps initial), based on theoretical 5G speeds. However, real-world 5G performance varies significantly by carrier, location, and network congestion. During peak evening hours, actual 5G throughput for many users was 8-15 Mbps, but the player continued requesting 25 Mbps streams, causing constant buffer underruns as the player couldn't download segments fast enough.

---

## Issue Classification

- Algorithm Deficiency
- Network Condition Assumptions
- Missing Real-World Testing

---

## Recovery

The Android team identified the ABR misconfiguration through player telemetry analysis. A server-side ABR configuration update was pushed that changed the 5G initial bitrate from 25 Mbps to 12 Mbps (matching 4G LTE profiles). This allowed the ABR algorithm to properly ramp up based on actual measured throughput rather than assumed network capability. Buffering complaints decreased by 85% within 30 minutes of the configuration push.

**Operator Override Applied:** Server-side ABR configuration reduced 5G initial bitrate, providing immediate relief without app update.

---

## Permanent Corrective Action

1. Updated ABR algorithm to use measured throughput instead of network type assumptions
2. Implemented 3-second throughput measurement before initial bitrate selection
3. Added carrier-specific ABR profiles based on collected performance data
4. Created network condition simulation testing for QA (variable 5G performance)
5. Added ABR performance monitoring dashboard with buffering correlation

---

## Process Improvement

> **Implement Throughput-Based ABR Initialization Instead of Network Type Assumptions**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **7:00 PM** | NBA games tip off, Android buffering complaints begin |
| **7:15 PM** | NOC identifies pattern: buffering concentrated on Android 5G users |
| **7:20 PM** | P2 incident declared. Android team notified. |
| **7:35 PM** | Player telemetry analysis shows ABR requesting 25 Mbps on 5G |
| **7:50 PM** | Actual 5G throughput measured at 8-15 Mbps during peak hours |
| **8:05 PM** | Root cause confirmed: aggressive 5G bitrate profile |
| **8:20 PM** | Server-side ABR config prepared with conservative 5G profile |
| **8:30 PM** | ABR configuration pushed to production |
| **8:45 PM** | Buffering complaints decrease 60% |
| **9:00 PM** | Buffering down 85%, within acceptable thresholds |
| **11:00 PM** | Incident resolved. Games complete without further issues. |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | Android only (5G connections) |
| **Issue Start Time** | 02/03/26 7:00 PM ET |
| **Issue End Time** | 02/03/26 9:00 PM ET |
| **Resolving Teams** | Android Mobile Team, Video Platform, NOC |
| **Users Impacted** | ~120,000 |
| **Event** | NBA Regular Season Games |

---

## Resolution Description

Android users on 5G networks experienced excessive buffering due to an ABR algorithm that assumed theoretical 5G speeds rather than measuring actual throughput. The algorithm requested 25 Mbps streams on connections delivering only 8-15 Mbps, causing constant buffer underruns. A server-side configuration update reduced the 5G initial bitrate to match real-world conditions, resolving buffering within 30 minutes. Post-incident, the ABR algorithm was updated to use measured throughput instead of network type assumptions.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#android-platform-alerts]
>
> Elevated buffering complaints from Android users during tonight's NBA games. Pattern suggests 5G connections are disproportionately affected. Users reporting constant "Loading" spinner. Player telemetry shows high buffer underrun rates. Android and Video Platform teams please investigate ABR behavior on 5G networks.
