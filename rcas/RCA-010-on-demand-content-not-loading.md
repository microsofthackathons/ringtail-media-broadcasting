# [INC-20260224-010] 02/24/26 - P2 - On-Demand Content Not Loading on Web Platform

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 2 - Must Have |
| **Reporter** | Web Platform Engineer |
| **Assignee** | Content Delivery Lead |
| **Resolution** | Resolved |
| **Components** | On-Demand Service, Content Manifest, CDN |
| **Affects Versions** | Web Player v6.1.0 |
| **Fix Versions** | Content Manifest Service v3.2.1 |
| **Labels** | OnDemand, P2, Web, Content-Delivery |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 1 hour 45 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 02/24/26 |
| **Updated** | 02/25/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260224-010 | On-Demand Content Load Failure | Closed |
| Related | CHG-4702 | Manifest Service Scaling Update | Completed |

---

## Executive Summary

At **10:00 AM ET**, web platform users reported inability to play on-demand content (game replays, highlights, classic games). Live streaming was unaffected. The issue impacted approximately **65,000 users** attempting to access on-demand content. The root cause was a content manifest service pod that entered a crash loop after running out of memory due to a memory leak. The Platform team scaled up additional manifest service pods and restarted the unhealthy pod, restoring on-demand access by **11:45 AM ET**.

---

## Root Cause

The content manifest service caches manifest files (HLS playlists) in memory to reduce origin requests. A code change 3 days prior introduced a memory leak where expired cache entries were not being properly evicted. Over 72 hours, memory usage grew until the pod exceeded its memory limit and was OOM-killed by Kubernetes. The pod restart was attempted but immediately crashed again due to loading the corrupted cache. With only 3 manifest service pods, the remaining 2 pods became overloaded and also began failing health checks.

---

## Issue Classification

- Memory Leak
- Insufficient Redundancy
- Cache Management Deficiency

---

## Recovery

The Platform team identified the manifest service crash loop through Kubernetes monitoring. They scaled the deployment from 3 to 6 pods, which distributed load and allowed healthy pods to recover. The crashing pod was manually deleted, causing Kubernetes to spawn a fresh instance with empty cache. The memory leak was mitigated by reducing cache max-age from 24 hours to 4 hours (configurable without deployment).

**Operator Override Applied:** NOC requested CDN to increase origin shielding for manifest requests, reducing load on manifest service during recovery.

---

## Permanent Corrective Action

1. Fixed memory leak in cache eviction logic
2. Increased manifest service replicas from 3 to 6 for redundancy
3. Implemented memory usage monitoring with alerting at 80% threshold
4. Added automatic pod restart when memory exceeds 90% for 5 minutes
5. Created weekly memory usage trend analysis for early leak detection

---

## Process Improvement

> **Implement Memory Usage Monitoring and Automatic Pod Recovery for Critical Services**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **9:45 AM** | Manifest service pod #1 OOM-killed |
| **9:46 AM** | Pod restart attempt fails (corrupted cache) |
| **9:50 AM** | Remaining pods experience increased load |
| **10:00 AM** | Users begin reporting on-demand content failures |
| **10:10 AM** | NOC identifies pattern: only on-demand affected |
| **10:15 AM** | P2 incident declared. Platform team notified. |
| **10:30 AM** | Manifest service crash loop identified |
| **10:40 AM** | Memory leak suspected based on usage trend |
| **10:50 AM** | NOC requests CDN origin shielding increase |
| **11:00 AM** | Platform team scales deployment to 6 pods |
| **11:10 AM** | New pods healthy, traffic redistributing |
| **11:20 AM** | Crashed pod deleted, fresh instance spawned |
| **11:30 AM** | Cache max-age reduced to 4 hours |
| **11:45 AM** | On-demand content fully restored |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | Web platform (on-demand only) |
| **Issue Start Time** | 02/24/26 10:00 AM ET |
| **Issue End Time** | 02/24/26 11:45 AM ET |
| **Resolving Teams** | Platform Engineering, CDN Operations, NOC |
| **Users Impacted** | ~65,000 |
| **Event** | Non-live content (replays, highlights) |

---

## Resolution Description

On-demand content failures were caused by a manifest service memory leak that led to OOM crashes. The crash loop and insufficient pod redundancy caused cascading failures. The Platform team scaled up additional pods and fixed the immediate issue by reducing cache TTL. The NOC coordinated with CDN to reduce origin load during recovery. Post-incident, memory monitoring and automatic recovery mechanisms were implemented.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#web-platform-alerts]
>
> On-demand content not loading on web platform. Users see loading spinner indefinitely. Game replays, highlights, and classic games all affected. Live streaming appears to work normally. Manifest service health check showing issues. Platform and CDN teams please investigate.
