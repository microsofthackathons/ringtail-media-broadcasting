# Quality of Experience (QoE) Degradation Response

> **Operator Override Available:** NOC operators can enable conservative streaming modes and adjust quality thresholds to improve user experience during degradation events.
>
> ⚡ = **Immediate Action.** Quality configuration changes are reversible and safe.

---

## Overview

This runbook covers procedures for responding to Quality of Experience (QoE) degradation affecting large numbers of users. QoE issues include buffering, low video quality, high startup times, and playback failures.

---

## When to Use

* Elevated buffering rates across platform
* Video quality metrics below thresholds
* High video startup time (TTFF)
* Rebuffering ratio above acceptable levels
* Playback failure rates elevated
* User complaints about quality spike

---

## QoE Metrics Reference

| Metric | Good | Acceptable | Degraded | Critical |
|--------|------|------------|----------|----------|
| Buffering Ratio | <1% | 1-3% | 3-5% | >5% |
| TTFF (startup) | <2s | 2-4s | 4-6s | >6s |
| Error Rate | <0.5% | 0.5-1% | 1-2% | >2% |
| Avg Bitrate | >6 Mbps | 4-6 Mbps | 2-4 Mbps | <2 Mbps |

---

## Risk Assessment

| Action | Risk Level | Approval Required |
|--------|------------|-------------------|
| Check QoE dashboards | Low | NOC can execute |
| Enable conservative ABR | Low | NOC can execute |
| Reduce max bitrate | Medium | Video Platform awareness |
| Disable specific features | Medium | Feature owner required |
| Emergency quality mode | High | Engineering Lead |

---

## Diagnostic Steps

### 1. ⚡ CHECK QOE DASHBOARD

1. Open **QoE Monitoring Dashboard**
2. Review current metrics:
   ```
   Global Buffering Ratio: [percentage]
   Average TTFF: [seconds]
   Playback Error Rate: [percentage]
   Average Bitrate: [Mbps]
   Concurrent Viewers: [count]
   ```

---

### 2. ⚡ IDENTIFY DEGRADATION SCOPE

Narrow down the affected population:

| Filter | Check |
|--------|-------|
| Platform | iOS, Android, Web, TV |
| Region | Geographic distribution |
| ISP | Specific carrier issues |
| Content | Specific game or all |
| Device | Specific models |

---

### 3. IDENTIFY ROOT CAUSE CATEGORY

| Symptom Pattern | Likely Cause | Primary Team |
|-----------------|--------------|--------------|
| Global degradation | Origin/CDN issue | Platform Engineering |
| Regional degradation | CDN POP issue | CDN Operations |
| Platform-specific | Player issue | Mobile/Web Team |
| ISP-specific | Peering issue | Network Operations |
| Content-specific | Encoding issue | Encoding Team |

---

## Operator Override Actions

### ⚡ ENABLE CONSERVATIVE ABR MODE (Low Risk)

Reduce aggressive bitrate requests to improve stability:

1. Open **Player Configuration Portal**
2. Navigate to **ABR Settings**
3. Enable **Conservative Mode**:
   ```
   Initial Bitrate: Reduce to 2 Mbps (from 6)
   Upgrade Threshold: Increase buffer requirement
   Downgrade Sensitivity: More aggressive
   ```
4. Apply to affected platforms
5. Click **Deploy Configuration**

**Impact:** Users start at lower quality but ramp up, reducing initial buffering

**Revert:** Disable Conservative Mode when issue resolved

---

### ⚡ REDUCE MAXIMUM BITRATE (Low Risk)

Cap maximum available quality:

1. In Player Configuration, go to **Bitrate Limits**
2. Set maximum bitrate:
   - Normal: 25 Mbps (4K)
   - Conservative: 12 Mbps (1080p max)
   - Emergency: 6 Mbps (720p max)
3. Apply to affected scope
4. Deploy configuration

**Impact:** Users won't receive highest quality streams

**When to Use:**
- Origin server overload
- CDN capacity constraints
- ISP bandwidth issues

**Revert:** Restore normal limits when capacity available

---

### ENABLE LOW-BANDWIDTH MODE (Medium Risk)

For severe capacity constraints:

1. Get **Video Platform** awareness
2. In **Emergency Quality Controls**:
   - Enable **Low Bandwidth Mode**
   - Settings applied:
     - Max 720p
     - Reduced framerate options
     - Compressed audio codec
3. Affects all users on platform

**Impact:** Significant quality reduction but improved playback success

**Requires:** Video Platform team awareness

---

### DISABLE BANDWIDTH-INTENSIVE FEATURES (Medium Risk)

Reduce load by disabling optional features:

| Feature | Bandwidth Saving | Impact |
|---------|------------------|--------|
| 4K/HDR streams | High | Quality reduction |
| Multi-angle cameras | Medium | Feature loss |
| Picture-in-Picture | Low | Feature loss |
| Instant replay | Low | Feature loss |

1. Get approval from feature owner
2. In **Feature Flags**, disable selected features
3. Monitor QoE improvement
4. Re-enable when capacity restored

---

## Issue-Specific Actions

### High Rebuffering

1. Enable conservative ABR
2. Reduce max bitrate if needed
3. Check CDN health by region
4. Check origin server capacity

### High Startup Time (TTFF)

1. Reduce initial bitrate
2. Check manifest service latency
3. Check authentication service latency
4. Check CDN first-byte time

### High Error Rate

1. Check for specific error codes
2. Route to appropriate team based on error
3. May indicate infrastructure failure
4. See: Live Stream Health Monitoring runbook

### Low Average Bitrate

1. Check if intentional (low bandwidth mode active)
2. Check ABR algorithm decisions
3. Verify all quality renditions available
4. Check user bandwidth measurements

---

## Escalation Triggers

Escalate to P1 if:
- Buffering ratio >10% for >5 minutes
- Playback error rate >5%
- Complete stream failure
- Issue affecting major live event

Escalate to P2 if:
- Buffering ratio 5-10% for >10 minutes
- Playback error rate 2-5%
- Quality significantly degraded

---

## Post-Procedure

* Document all configuration changes
* Set reminders to revert temporary changes
* Note metric improvements after each action
* Capture before/after QoE snapshots for RCA

---

## Escalation Contacts

| Issue | Team | Contact |
|-------|------|---------|
| CDN issues | CDN Operations | #cdn-operations |
| Origin issues | Platform Engineering | #platform-engineering |
| Player issues | Video Platform | #video-platform |
| Mobile-specific | Mobile Teams | #mobile-platform |
| Encoding issues | Encoding Team | #encoding-platform |
| Network/ISP | Network Operations | #network-operations |

---

## Related Runbooks

* CDN Edge Node Health
* Origin Server Scaling
* Player Error Troubleshooting
* Network Peering Issues
