# Stats API Failover Procedure

> **Operator Override Available:** NOC operators can switch to backup stats cache and adjust cache TTLs to reduce database load while Data Services investigates.
>
> ⚡ = **Immediate Action.** Safe to execute without engineering approval for cache operations.

---

## Overview

This runbook covers procedures for handling Stats API degradation, including failover to backup systems and cache tuning. Stats issues affect real-time game data displayed to viewers across all platforms.

---

## When to Use

* Stats overlay showing stale or frozen data
* Stats API latency exceeding SLA (>200ms p99)
* Stats database connection issues
* Message queue backups in stats pipeline
* Stats not updating during live games

---

## Risk Assessment

| Action | Risk Level | Approval Required |
|--------|------------|-------------------|
| Increase cache TTL | Low | NOC can execute |
| Switch to backup cache | Low | NOC can execute |
| Stats service restart | Medium | Data Services approval |
| Database failover | High | DBA Team required |

---

## Prerequisites

* Access to Stats Admin Portal
* Access to monitoring dashboards
* Confirmed stats-related issue (not encoding or overlay rendering)

---

## Diagnostic Steps

### 1. CHECK STATS API HEALTH

```
Dashboard: Stats Platform Health
URL: [monitoring-url]/stats-health

Check:
- API response latency (p50, p99)
- Error rate percentage
- Active connections to database
- Message queue depth
```

---

### 2. IDENTIFY ISSUE CATEGORY

| Symptom | Likely Cause | Action |
|---------|--------------|--------|
| Latency >500ms | Database query performance | Increase cache TTL |
| Queue depth >50,000 | Pipeline backup | Stop batch jobs |
| Connection errors | Database connectivity | Failover to backup |
| Stale data (>30s old) | Pipeline processing delay | Check source feed |

---

## Operator Override Actions

### ⚡ INCREASE CACHE TTL (Low Risk)

Reduces database load by serving cached data longer:

1. Log into **Stats Admin Portal**
2. Navigate to **Configuration > Cache Settings**
3. Change **Live Stats Cache TTL** from default (2s) to **10s**
4. Click **Apply Configuration**
5. Note: Stats will be up to 10 seconds delayed

**Revert:** Return TTL to 2 seconds when issue resolved

---

### ⚡ SWITCH TO BACKUP CACHE (Low Risk)

Uses pre-computed stats cache for critical game data:

1. In Stats Admin Portal, navigate to **Failover Controls**
2. Enable **Backup Cache Mode**
3. Select games to serve from backup
4. Click **Activate Backup**

**Limitations:**
- Advanced stats unavailable (basic box score only)
- Updates every 30 seconds instead of real-time

**Revert:** Disable Backup Cache Mode when primary restored

---

### STOP HISTORICAL SYNC JOBS (Medium Risk)

If batch jobs are consuming pipeline resources:

1. Navigate to **Jobs > Active Jobs**
2. Identify historical sync or batch processing jobs
3. Click **Terminate** for non-critical jobs
4. Note job IDs for restart after incident

**Requires:** Data Services awareness if job was scheduled

---

## Escalation Procedures

### Stats Service Restart

If operator actions don't resolve:

1. Contact **Data Services On-Call**
2. Request stats service pod restart
3. Expected impact: 30-60 second stats blackout

### Database Failover

If database connectivity fails:

1. Contact **DBA Team On-Call**
2. Request database replica promotion
3. Expected impact: 2-5 minute stats outage

---

## Verification

After any action:

1. Check Stats health dashboard
2. Monitor a live game stream for stats updates
3. Verify queue depth returning to normal
4. Confirm latency within SLA

---

## Post-Procedure

* Document actions taken in incident ticket
* If cache TTL was increased, schedule revert
* Notify Data Services of any job terminations
* Update timeline with stats restoration time

---

## Escalation Contacts

| Issue | Team | Contact Method |
|-------|------|----------------|
| Pipeline issues | Data Services | #stats-platform-alerts |
| Database issues | DBA Team | #dba-oncall |
| Source feed issues | League Data Integration | #league-data-support |
| Overlay rendering | Video Platform | #video-platform-alerts |

---

## Related Runbooks

* Database Connection Pool Exhaustion
* Message Queue Health Check
* League Data Feed Monitoring
