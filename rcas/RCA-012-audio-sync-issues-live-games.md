# [INC-20260308-012] 03/08/26 - P2 - Live Game Audio Sync Issues Across Platforms

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 2 - Must Have |
| **Reporter** | Broadcast Engineering |
| **Assignee** | Encoding Team Lead |
| **Resolution** | Resolved |
| **Components** | Live Encoder, Audio Processing, Synchronization |
| **Affects Versions** | Encoder Config v2.1.0 |
| **Fix Versions** | Encoder Config v2.1.1 |
| **Labels** | Audio, P2, Encoder, Sync, All-Platforms |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 55 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 03/08/26 |
| **Updated** | 03/09/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260308-012 | Audio Sync Issues | Closed |
| Related | CHG-4789 | Encoder Audio Pipeline Correction | Completed |

---

## Executive Summary

At **7:45 PM ET** during a EuroLeague playoff game, viewers reported significant audio-video sync issues. Commentary was approximately **300-500ms ahead** of the video action. The issue affected all platforms receiving the live feed. The root cause was a misconfigured audio processing pipeline in the live encoder that introduced additional video latency without compensating audio delay. The Encoding team corrected the configuration and restarted the affected encoder, restoring proper sync by **8:40 PM ET**.

---

## Root Cause

A firmware update to the live encoding hardware 48 hours prior changed the default video processing pipeline from "low latency" to "high quality" mode. The high quality mode adds approximately 400ms of video processing latency for enhanced frame interpolation, but the audio pipeline was not correspondingly delayed. This caused audio to arrive ahead of video for all viewers receiving the affected feed. The issue was not detected during the firmware update because test content did not include live commentary.

---

## Issue Classification

- Firmware Update Side Effect
- Audio-Video Pipeline Mismatch
- Testing Gap (No Live Commentary Test)

---

## Recovery

The Encoding team identified the sync offset using broadcast monitoring tools that measure A/V alignment. They updated the encoder configuration to add a 400ms audio delay to compensate for the video processing latency. The encoder was restarted with the new configuration. A brief 15-second interruption occurred during the restart, after which audio and video were properly synchronized.

**Operator Override Applied:** Broadcast Engineering performed live encoder restart per emergency encoder restart runbook.

---

## Permanent Corrective Action

1. Created encoder firmware update checklist including A/V sync verification
2. Added automated A/V sync monitoring with alerting at 100ms offset
3. Implemented live commentary test requirement for encoder configuration changes
4. Created encoder configuration backup/restore procedure
5. Added A/V sync check to pre-game broadcast verification checklist

---

## Process Improvement

> **Implement Automated A/V Sync Monitoring and Encoder Firmware Update Testing Protocol**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **7:00 PM** | EuroLeague playoff game coverage begins |
| **7:15 PM** | Game tips off, first viewer complaints about audio |
| **7:45 PM** | Elevated complaints reach threshold, P2 declared |
| **7:50 PM** | Encoding team joins investigation |
| **8:00 PM** | A/V sync offset measured at 350-450ms |
| **8:10 PM** | Recent firmware update identified as potential cause |
| **8:20 PM** | Video processing mode confirmed as "high quality" (adds latency) |
| **8:25 PM** | Audio delay compensation configured (400ms) |
| **8:35 PM** | Encoder restart initiated (15-second interruption) |
| **8:40 PM** | Encoder back online with proper A/V sync |
| **8:45 PM** | User complaints cease. Incident resolved. |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | All platforms (source encoder issue) |
| **Issue Start Time** | 03/08/26 7:15 PM ET |
| **Issue End Time** | 03/08/26 8:40 PM ET |
| **Resolving Teams** | Encoding Team, Broadcast Engineering, NOC |
| **Viewers Impacted** | ~180,000 (all viewers of affected feed) |
| **Event** | EuroLeague Playoff Game |

---

## Resolution Description

Audio-video sync issues were caused by an encoder firmware update that changed the video processing mode without adjusting audio delay compensation. The Encoding team corrected the configuration by adding appropriate audio delay and restarted the encoder. Post-incident, A/V sync monitoring was implemented and encoder firmware update procedures were enhanced to include sync verification.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#broadcast-engineering-alerts]
>
> Audio sync issues reported on EuroLeague playoff game. Commentary appears 300-500ms ahead of video. Issue affects all platforms. Encoding team please check encoder A/V sync configuration. Broadcast Engineering standing by for encoder intervention if needed.
