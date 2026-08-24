# [INC-20260118-001] 01/18/26 - P1 - Complete Streaming Blackout During NBA Finals Game 4

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 1 - Critical |
| **Reporter** | NOC Supervisor |
| **Assignee** | Platform Engineering Lead |
| **Resolution** | Resolved |
| **Components** | Live Streaming, CDN, Origin Servers |
| **Affects Versions** | Platform v4.2.1 |
| **Fix Versions** | Platform v4.2.2 |
| **Labels** | NBA-Finals, P1, Streaming-Outage |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 47 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 01/18/26 |
| **Updated** | 01/19/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260118-001 | NBA Finals Game 4 Streaming Blackout | Closed |
| Related | CHG-4521 | Origin Server Capacity Increase | Completed |
| Related | PRB-892 | Auto-scaling Configuration Review | In Progress |

---

## Executive Summary

At **8:47 PM ET** during NBA Finals Game 4, the Operations Center detected a complete streaming blackout affecting all platforms (iOS, Android, Web, Smart TVs, and gaming consoles). Approximately **2.3 million concurrent viewers** were impacted. The root cause was identified as an origin server overload triggered when auto-scaling failed to provision additional capacity during a historic viewership surge at tip-off. The CDN team manually provisioned emergency origin capacity while the Platform Engineering team corrected the auto-scaling configuration. Normal streaming was restored by **9:34 PM ET**.

---

## Root Cause

The origin server cluster was configured with auto-scaling policies that had a **15-minute cooldown period** between scaling events. During the pre-game show, viewership gradually increased, triggering the first scaling event. However, the unprecedented spike at tip-off (viewership increased by **340% in 90 seconds**) occurred during the cooldown window, preventing additional capacity from being provisioned. The existing origin servers became overwhelmed, causing connection timeouts that cascaded to edge nodes, resulting in a complete streaming blackout across all platforms and regions.

---

## Issue Classification

- Infrastructure Capacity Failure
- Auto-scaling Configuration Deficiency
- Monitoring Gap

---

## Recovery

The CDN Operations team executed an emergency capacity override, manually spinning up **12 additional origin server instances** across three regions. Simultaneously, the Platform Engineering team reduced the auto-scaling cooldown period from 15 minutes to 2 minutes and increased the scaling step size from 2 to 6 instances per event. The Stats Overlay team confirmed data feeds were healthy once origin capacity was restored. Full streaming functionality was restored at **9:34 PM ET**, with viewers experiencing automatic reconnection within 2-3 minutes of service restoration.

---

## Permanent Corrective Action

1. Auto-scaling cooldown period reduced from 15 minutes to 2 minutes for high-priority events
2. Pre-event capacity provisioning protocol established (50% additional capacity pre-staged 2 hours before major events)
3. Implemented predictive scaling based on ticket sales and historical viewership data
4. Added real-time origin server health dashboard with 30-second refresh intervals
5. Created emergency capacity runbook with pre-authorized manual scaling procedures

---

## Process Improvement

> **Implement Predictive Capacity Planning for Major Sporting Events**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **8:30 PM** | Pre-game show streaming normally, viewership at 1.2M concurrent users |
| **8:42 PM** | Auto-scaling triggered, 2 origin instances added, cooldown begins |
| **8:47 PM** | Tip-off occurs, viewership spikes to 2.3M. Origin servers overloaded. |
| **8:48 PM** | CDN edge nodes begin reporting origin timeouts. Stream failures cascade. |
| **8:49 PM** | NOC declares P1 incident. Bridge opened immediately. |
| **8:52 PM** | Platform Engineering identifies auto-scaling cooldown as root cause |
| **8:55 PM** | CDN Ops begins emergency manual capacity provisioning |
| **9:05 PM** | 6 additional origin instances online in US-East region |
| **9:15 PM** | 6 additional instances online in US-West and EU-West regions |
| **9:20 PM** | Auto-scaling configuration updated, cooldown reduced to 2 minutes |
| **9:28 PM** | Origin server cluster stabilized, stream quality recovering |
| **9:34 PM** | Full service restoration confirmed across all platforms |
| **9:45 PM** | Viewership returns to pre-incident levels (2.1M concurrent) |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | iOS, Android, Web, Smart TV, Gaming Consoles |
| **Issue Start Time** | 01/18/26 8:47 PM ET |
| **Issue End Time** | 01/18/26 9:34 PM ET |
| **Resolving Teams** | Platform Engineering, CDN Operations, NOC |
| **Viewers Impacted** | ~2,300,000 |
| **Event** | NBA Finals Game 4 |

---

## Resolution Description

The streaming blackout during NBA Finals Game 4 was caused by auto-scaling configuration that could not respond fast enough to the unprecedented viewership surge at tip-off. The 15-minute cooldown period prevented the system from provisioning additional origin server capacity when it was critically needed. Emergency manual scaling by the CDN Operations team restored service within 47 minutes. Post-incident, auto-scaling policies were updated, and predictive capacity planning procedures were implemented for all major sporting events.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link - Finals War Room]
> **Thread:** [#noc-critical-incidents]
>
> CRITICAL: Complete streaming blackout affecting all platforms during NBA Finals Game 4 tip-off. Approximately 2.3M viewers impacted. Origin servers returning 503 errors. CDN edge nodes reporting connection timeouts. All hands on deck required. Platform Engineering, CDN Ops, and Stats team please join bridge immediately.
