# [INC-20260422-019] 04/22/26 - P2 - Pre-Game Show Content Missing From Streams

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 2 - Must Have |
| **Reporter** | Production Operations |
| **Assignee** | Content Delivery Lead |
| **Resolution** | Resolved |
| **Components** | Content Scheduling, Stream Switcher, Pre-Game Production |
| **Affects Versions** | Stream Scheduler v2.2.0 |
| **Fix Versions** | Stream Scheduler v2.2.1 |
| **Labels** | Pre-Game, P2, Scheduling, Content |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 1 hour 20 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 04/22/26 |
| **Updated** | 04/23/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260422-019 | Pre-Game Content Missing | Closed |
| Related | CHG-4989 | Stream Scheduler Fix | Completed |

---

## Executive Summary

At **7:00 PM ET**, users tuning in for NBA Playoff pre-game shows saw a "Content Starting Soon" slate instead of the scheduled pre-game programming that had begun 30 minutes earlier. The issue affected approximately **215,000 viewers** across all platforms. The root cause was a daylight saving time edge case in the stream scheduler that calculated the pre-game start time incorrectly for one day each year. The Content Delivery team manually overrode the stream schedule and pushed the correct feed, restoring pre-game content by **7:20 PM ET**.

---

## Root Cause

The stream scheduler used a timezone-aware calculation to determine when to switch from the "starting soon" slate to the live pre-game feed. However, a bug in the DST transition handling caused the scheduler to calculate the pre-game start time as 1 hour later than scheduled on specific dates when DST offset changes coincided with historical schedule data. The pre-game show was airing in the production facility, but the scheduler wasn't switching the public stream to that feed because it believed the show hadn't started yet.

---

## Issue Classification

- Timezone/DST Bug
- Stream Scheduling Logic Error
- Edge Case Handling

---

## Recovery

The Content Delivery team identified the scheduling mismatch through production monitoring dashboards that showed the pre-game feed was active but not being distributed. They manually overrode the stream scheduler to force the switch to the pre-game feed. Users received the live pre-game content within 5 minutes of the override. The DST bug was flagged for immediate fix.

**Operator Override Applied:** Production Operations executed manual stream switch (per Content Delivery runbook), bypassing the automated scheduler.

---

## Permanent Corrective Action

1. Fixed DST transition handling in stream scheduler timezone calculations
2. Added comprehensive unit tests for all DST edge cases globally
3. Implemented stream schedule validation that compares against production feed status
4. Created pre-game content delivery verification check 15 minutes before scheduled start
5. Added automated alerting when scheduled content doesn't match production feed

---

## Process Improvement

> **Implement Stream Schedule Validation Against Production Feed Status**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **6:30 PM** | Pre-game show begins in production facility |
| **7:00 PM** | Users tune in expecting pre-game content |
| **7:02 PM** | Reports that "starting soon" slate still showing |
| **7:08 PM** | Production confirms pre-game is airing, not reaching viewers |
| **7:10 PM** | P2 declared. Content Delivery team notified. |
| **7:15 PM** | Stream scheduler showing pre-game not yet started |
| **7:18 PM** | DST calculation bug suspected |
| **7:20 PM** | Production Ops executes manual stream override |
| **7:22 PM** | Pre-game content now reaching viewers |
| **7:30 PM** | Root cause confirmed as DST transition bug |
| **7:45 PM** | Incident resolved. Users watching pre-game normally. |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | All platforms |
| **Issue Start Time** | 04/22/26 7:00 PM ET |
| **Issue End Time** | 04/22/26 7:22 PM ET |
| **Resolving Teams** | Content Delivery, Production Operations, NOC |
| **Users Impacted** | ~215,000 |
| **Event** | NBA Playoffs First Round |

---

## Resolution Description

Pre-game content was not reaching viewers due to a DST-related bug in the stream scheduler that miscalculated the pre-game start time by 1 hour. The scheduler believed the show hadn't started and continued displaying the "starting soon" slate. Production Operations executed a manual stream override to push the correct feed. Post-incident, DST handling was fixed and stream schedule validation was implemented.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#production-operations]
>
> Pre-game show not reaching viewers. Users seeing "starting soon" slate. Production confirms pre-game is live in the studio. Stream scheduler appears to have wrong start time. NBA Playoffs pre-game should have started 30 minutes ago. Content Delivery please investigate scheduler. Production Ops standing by for manual override.
