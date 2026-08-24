# Game Schedule Sync Issues

> **Operator Override Available:** NOC operators can trigger schedule refreshes and manually update game times when league feed sync fails.
>
> ⚡ = **Immediate Action.** Schedule refresh is safe to execute independently.

---

## Overview

This runbook covers procedures for handling game schedule synchronization issues. Schedule data drives the entire streaming experience including EPG (Electronic Program Guide), notifications, DVR scheduling, and content availability windows.

---

## When to Use

* Game times displaying incorrectly
* Games missing from schedule
* Duplicate game entries
* Pre-game/post-game windows incorrect
* League schedule changes not reflecting
* Time zone display issues

---

## Risk Assessment

| Action | Risk Level | Approval Required |
|--------|------------|-------------------|
| Force schedule refresh | Low | NOC can execute |
| Manual time correction | Low | Content Ops awareness |
| Add missing game manually | Medium | Content Ops required |
| Delete duplicate entries | Medium | Content Ops required |
| Bulk schedule update | High | Engineering required |

---

## Schedule Data Flow

```
League Provider → Schedule API → Content DB → Platform APIs → User Devices
                      ↓
                 Notification Service
                      ↓
                 DVR Scheduler
```

---

## Diagnostic Steps

### 1. ⚡ CHECK SCHEDULE SYNC STATUS

1. Open **Schedule Management Dashboard**
2. Review sync status:
   ```
   Last Successful Sync: [timestamp]
   Sync Interval: [every 5 minutes]
   League Feed Status: [healthy/degraded/failed]
   Games Updated: [count in last sync]
   Pending Changes: [count]
   ```

---

### 2. ⚡ VERIFY GAME DATA

1. Search for affected game:
   - By team(s)
   - By date
   - By game ID
2. Review game details:
   ```
   Game ID: [ID]
   Teams: [Home] vs [Away]
   Scheduled Time: [time with timezone]
   Venue: [location]
   Broadcast Status: [scheduled/live/completed]
   Last Updated: [timestamp]
   Source: [league feed/manual entry]
   ```

---

### 3. COMPARE WITH LEAGUE SOURCE

1. Check official league schedule
2. Compare times, teams, venue
3. Identify discrepancies
4. Determine if our data or league data is incorrect

---

## Operator Override Actions

### ⚡ FORCE SCHEDULE REFRESH (Low Risk)

Trigger immediate sync with league feed:

1. In Schedule Dashboard, click **Force Sync Now**
2. Wait for sync completion (1-2 minutes)
3. Verify affected games updated correctly
4. Check sync log for any errors

**When to Use:**
- Schedule appears stale
- After known league schedule changes
- When sync status shows delays

---

### ⚡ MANUAL GAME TIME CORRECTION (Low Risk)

For individual game time corrections:

1. Locate game in Schedule Management
2. Click **Edit Game**
3. Update fields:
   - Scheduled start time
   - Time zone
   - Pre-game window start
   - Broadcast end time
4. Add note: "Manual correction - [reason]"
5. Click **Save**

**Verification:**
- Check game appears correctly in EPG
- Verify notification timing updated
- Confirm DVR window adjusted

**Notify:** Content Ops of manual changes

---

### ADD MISSING GAME (Medium Risk)

If game not syncing from league feed:

1. Get **Content Ops** approval
2. In Schedule Management, click **Add Game Manually**
3. Enter required fields:
   ```
   League: [NBA/WNBA/EuroLeague/etc.]
   Home Team: [team]
   Away Team: [team]
   Game Date: [date]
   Start Time: [time with timezone]
   Venue: [location]
   Broadcast Type: [Live/Tape Delay]
   Blackout Regions: [if applicable]
   ```
4. Set source as "Manual Entry"
5. Add reason note

**Requires:** Content Ops approval

---

### REMOVE DUPLICATE ENTRIES (Medium Risk)

If duplicate games appear:

1. Identify all duplicate entries
2. Determine which entry is correct (check league ID)
3. Get **Content Ops** approval
4. Archive incorrect entries (don't delete)
5. Verify single correct entry remains

**Requires:** Content Ops approval

---

## Common Issues and Solutions

### Time Zone Display Issues

1. Verify game stored in UTC
2. Check user's time zone detection
3. Verify platform time zone conversion
4. May be client-side display bug

### Post-Game Schedule Changes

When league reschedules games:

1. League feed should update automatically
2. If not syncing, force refresh
3. Manual update as fallback
4. Notify users via push notification (Content Ops)

### Playoff Schedule TBD

For games shown as "TBD":

1. This is expected until earlier games complete
2. Monitor league feed for updates
3. Force sync after determining games complete
4. Schedule typically updates within 30 minutes

---

## Downstream Impact

Schedule changes affect:

| System | Impact | Action Required |
|--------|--------|-----------------|
| EPG | Display incorrect times | Auto-refreshes |
| Notifications | Wrong alert times | May need reschedule |
| DVR | Incorrect recording windows | Auto-adjusts |
| On-Demand | Availability windows | May need manual |
| Blackouts | Incorrect application | Verify rules |

---

## Escalation Procedures

### League Feed Outage

If league schedule feed completely unavailable:

1. Contact **League Data Integration**
2. Check league provider status
3. Prepare for manual updates if extended outage

### Bulk Schedule Issues

If many games affected:

1. Do not attempt bulk manual fixes
2. Contact **Engineering Team**
3. May need database-level intervention

### Time-Sensitive Corrections

For games starting within 1 hour:

1. Make immediate manual correction
2. Notify all downstream systems
3. Alert Content Ops and NOC supervisor

---

## Post-Procedure

* Document all manual changes in ticket
* Verify downstream systems updated
* Monitor for sync to overwrite manual changes
* Report persistent feed issues to League Integration

---

## Escalation Contacts

| Issue | Team | Contact |
|-------|------|---------|
| League feed issues | League Data Integration | #league-data-support |
| Schedule approvals | Content Operations | #content-operations |
| Notification issues | Engagement Platform | #notifications-platform |
| DVR issues | Video Platform | #video-platform |
| Database issues | Engineering | #platform-engineering |

---

## Related Runbooks

* League Data Feed Monitoring
* Push Notification Management
* DVR Recording Issues
* Content Availability Windows
