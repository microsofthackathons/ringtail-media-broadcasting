# Live Encoder Emergency Restart Procedure

> **Requires Broadcast Engineering Coordination:** Encoder restarts cause brief stream interruptions. Coordinate with Broadcast Engineering before execution.
>
> ⚠️ = **Caution.** This procedure will interrupt the live stream for 15-30 seconds.

---

## Overview

This runbook covers emergency procedures for restarting live encoders during broadcast issues. Encoder restarts should only be performed when encoding issues cannot be resolved through configuration changes.

---

## When to Use

* Encoder in error state and not recovering
* Audio/video sync drift exceeding acceptable threshold
* Encoder output quality degraded
* Encoder unresponsive to configuration changes
* Multi-bitrate ladder issues requiring full reset

---

## Risk Assessment

| Action | Risk Level | Approval Required |
|--------|------------|-------------------|
| Encoder config refresh | Low | Encoding Team can execute |
| Single encoder restart | Medium | Broadcast Engineering coord |
| Encoder failover | Medium | Broadcast Engineering coord |
| Full encoder cluster restart | High | Broadcast Engineering Lead |

---

## Prerequisites

* Broadcast Engineering notified and on standby
* Backup encoder ready (if available)
* Commercial break window identified (preferred)
* Incident ticket created with timeline

---

## Decision Matrix: Restart vs. Other Actions

| Issue | Try First | If Fails, Then |
|-------|-----------|----------------|
| A/V sync drift | Config adjustment | Encoder restart |
| Video artifacts | Source feed check | Encoder restart |
| Bitrate issues | ABR config | Encoder restart |
| Encoder error state | Process restart | Full restart |
| Network issues | Network check | Failover |

---

## Pre-Restart Checklist

Before initiating restart:

- [ ] Broadcast Engineering notified
- [ ] Backup encoder status confirmed
- [ ] Next commercial break time noted
- [ ] Failover procedure ready as backup
- [ ] CDN team notified (may need cache handling)

---

## Procedure

### 1. NOTIFY BROADCAST ENGINEERING

Contact Broadcast Engineering:
- Channel: #broadcast-engineering
- Bridge: Production War Room

**Message Template:**
```
ENCODER RESTART REQUIRED
Game: [Game ID / Teams]
Encoder: [Encoder ID]
Issue: [Brief description]
Proposed window: [Time, ideally commercial break]
Expected interruption: 15-30 seconds

Please confirm ready for restart.
```

---

### 2. PREPARE FOR RESTART

1. Open **Encoder Management Console**
2. Locate affected encoder
3. Verify current encoder status and settings
4. Note current configuration for reference
5. Enable **Restart Mode** (prepares backup encoder to warm standby)

---

### 3. ⚠️ INITIATE ENCODER FAILOVER (Preferred)

If failover encoder available:

1. In Encoder Console, select **Failover Controls**
2. Click **Initiate Failover**
3. Monitor switchover status
4. Confirm secondary encoder taking traffic
5. Restart primary encoder while secondary active

**Viewer Impact:** Minimal (1-2 second switch)

---

### 4. ⚠️ DIRECT ENCODER RESTART (If No Failover)

If failover not available:

1. Confirm with Broadcast Engineering: ready to restart
2. Wait for commercial break if possible
3. In Encoder Console, click **Stop Encoder**
4. Wait for encoder to fully stop (10-15 seconds)
5. Click **Start Encoder**
6. Monitor startup sequence
7. Verify output quality and A/V sync

**Viewer Impact:** 15-30 second interruption

---

### 5. POST-RESTART VERIFICATION

Immediately after restart:

1. Verify encoder status shows "Running"
2. Check all bitrate renditions active
3. Verify A/V sync within tolerance (<50ms)
4. Confirm CDN receiving new segments
5. Check viewer-facing stream quality

**Verification Dashboard:**
```
Encoder Status: Running
Output Bitrates: 720p, 1080p, 4K (all green)
A/V Sync: -12ms (within tolerance)
Segment Generation: Active
CDN Ingestion: Healthy
```

---

### 6. NOTIFY TEAMS

After successful restart:

```
ENCODER RESTART COMPLETE
Game: [Game ID / Teams]
Encoder: [Encoder ID]
Restart Time: [HH:MM:SS]
Stream Restored: [HH:MM:SS]
Total Interruption: [seconds]

Monitoring for any additional issues.
```

---

## Failback Procedure

If restarted encoder is backup:

1. Monitor primary encoder health
2. When primary stable, schedule failback
3. Preferably during next commercial break
4. Follow same failover procedure in reverse
5. Verify primary encoder healthy post-failback

---

## Troubleshooting Restart Failures

### Encoder Won't Stop

1. Check for stuck transcoding processes
2. Force kill encoder process if necessary
3. Contact Encoding Team for assistance

### Encoder Won't Start

1. Verify input feed is present
2. Check for resource conflicts
3. Verify licensing/activation status
4. Contact Encoding Team immediately

### Quality Issues After Restart

1. Verify source feed quality
2. Check encoder configuration restored correctly
3. Compare to pre-restart settings
4. May need manual configuration adjustment

---

## Escalation

| Issue | Escalate To |
|-------|-------------|
| Encoder won't restart | Encoding Team Lead |
| Repeated issues after restart | Platform Engineering |
| Source feed issues | Broadcast Operations |
| CDN not receiving | CDN Operations |

---

## Post-Procedure

* Document restart in incident ticket
* Note precise interruption duration
* Capture any viewer impact metrics
* Schedule post-incident review if repeated issues

---

## Escalation Contacts

| Role | Team | Contact |
|------|------|---------|
| Encoder issues | Encoding Team | #encoding-platform |
| Broadcast coordination | Broadcast Engineering | #broadcast-engineering |
| Source feed issues | Production Operations | #production-ops |
| CDN issues | CDN Operations | #cdn-operations |

---

## Related Runbooks

* Encoder Configuration Management
* A/V Sync Troubleshooting
* Broadcast Source Feed Issues
* CDN Origin Ingestion Problems
