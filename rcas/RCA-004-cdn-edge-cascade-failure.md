# [INC-20260228-004] 02/28/26 - P1 - CDN Edge Node Cascade Failure Affecting All Regions

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 1 - Critical |
| **Reporter** | CDN Operations Engineer |
| **Assignee** | CDN Platform Architect |
| **Resolution** | Resolved |
| **Components** | CDN Edge Nodes, Load Balancer, Origin Shield |
| **Affects Versions** | CDN Config v5.4.2 |
| **Fix Versions** | CDN Config v5.4.3 |
| **Labels** | CDN, P1, Multi-Region, Cascade-Failure |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 52 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 02/28/26 |
| **Updated** | 03/01/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260228-004 | CDN Edge Cascade Failure | Closed |
| Related | CHG-4756 | CDN Health Check Interval Optimization | Completed |
| Related | PRB-941 | Edge Node Isolation Circuit Breaker | In Progress |

---

## Executive Summary

At **9:15 PM ET** during WNBA All-Star Game coverage, CDN edge nodes began failing across all regions in a cascading pattern. Within 8 minutes, 73% of edge nodes were marked unhealthy, resulting in severe streaming degradation and complete outages for approximately **1.8 million viewers**. The root cause was an overly aggressive health check configuration combined with a temporary origin latency spike that caused edge nodes to be prematurely marked unhealthy. As traffic shifted to remaining healthy nodes, they too became overwhelmed and failed their health checks, creating a cascade effect. CDN Operations disabled aggressive health checks and manually restored edge nodes, achieving full recovery by **10:07 PM ET**.

---

## Root Cause

A CDN configuration change deployed 48 hours earlier modified edge node health check parameters to be more aggressive: failure threshold reduced from 5 to 2 consecutive failures, and timeout reduced from 10 seconds to 3 seconds. During the WNBA All-Star Game, a brief origin latency spike (caused by a garbage collection pause) resulted in health check responses exceeding the 3-second timeout. Edge nodes that failed 2 consecutive checks were immediately marked unhealthy and removed from rotation. Traffic shifted to the remaining healthy nodes, which then became overloaded and also failed their health checks. This cascade spread across all regions within 8 minutes.

---

## Issue Classification

- Configuration Change Side Effect
- Health Check Misconfiguration
- Missing Circuit Breaker Pattern

---

## Recovery

CDN Operations disabled the aggressive health check configuration and reverted to previous thresholds (5 failures, 10-second timeout). The NOC executed the CDN Emergency Recovery runbook, which included manual edge node restoration and traffic rebalancing. The CDN team worked region-by-region to bring nodes back online: US-East first (highest viewer concentration), then US-West, EU-West, and APAC. Origin servers were monitored throughout to ensure they could handle the restored traffic. Full service was restored by **10:07 PM ET**.

---

## Permanent Corrective Action

1. Reverted health check thresholds to production-proven configuration
2. Implemented graduated health check degradation (warn → degrade → remove) instead of binary healthy/unhealthy
3. Added circuit breaker pattern to prevent cascade failures (max 20% of edge nodes can be removed within 5 minutes)
4. Created CDN configuration change review board requiring approval for health check parameter changes
5. Added origin latency monitoring with automatic health check relaxation during spikes
6. Implemented health check configuration canary process (single region for 24 hours before global rollout)

---

## Process Improvement

> **Implement Circuit Breaker Patterns and Graduated Health Check Degradation for CDN Edge Nodes**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **9:12 PM** | Origin server experiences brief latency spike (GC pause, 4.2 seconds) |
| **9:13 PM** | US-East edge nodes begin failing health checks |
| **9:15 PM** | 15 edge nodes marked unhealthy in US-East. Traffic shifts. |
| **9:16 PM** | NOC receives edge node failure alerts. Monitoring dashboard shows cascade. |
| **9:17 PM** | P1 declared. Bridge opened immediately. |
| **9:18 PM** | Cascade spreads to US-West region |
| **9:20 PM** | EU-West and APAC regions affected. 73% of global edge nodes unhealthy. |
| **9:23 PM** | CDN Operations identifies aggressive health check config as cause |
| **9:28 PM** | Health check configuration reverted to previous thresholds |
| **9:35 PM** | NOC begins executing CDN Emergency Recovery runbook |
| **9:42 PM** | US-East edge nodes restored and accepting traffic |
| **9:50 PM** | US-West region restored |
| **9:58 PM** | EU-West region restored |
| **10:07 PM** | APAC region restored. Full service recovery confirmed. |
| **10:15 PM** | Incident marked resolved. All metrics nominal. |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | All platforms |
| **Issue Start Time** | 02/28/26 9:15 PM ET |
| **Issue End Time** | 02/28/26 10:07 PM ET |
| **Resolving Teams** | CDN Operations, Platform Engineering, NOC |
| **Edge Nodes Affected** | 73% of global edge node fleet |
| **Viewers Impacted** | ~1,800,000 |
| **Event** | WNBA All-Star Game |

---

## Resolution Description

The CDN edge cascade failure was triggered by an overly aggressive health check configuration that marked nodes unhealthy after just 2 failures with a 3-second timeout. A brief origin latency spike caused initial health check failures, and the resulting traffic shift overloaded remaining nodes, creating a cascade effect across all regions. CDN Operations reverted the health check configuration and manually restored edge nodes region-by-region. Post-incident, circuit breaker patterns and graduated health check degradation were implemented to prevent similar cascades.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#cdn-operations-critical]
>
> CRITICAL: CDN edge nodes failing in cascade pattern across all regions. Currently 73% of edge fleet marked unhealthy. WNBA All-Star Game in progress with 1.8M viewers affected. Streaming completely down for many users, severe buffering for others. CDN Ops, Platform Engineering, and Origin team please join bridge. NOC standing by to execute emergency recovery runbook on instruction.
