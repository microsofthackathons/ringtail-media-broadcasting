# Ad Insertion Troubleshooting

> **Operator Override Available:** NOC operators can disable ad insertion for specific streams to resolve playback issues as a temporary workaround.
>
> ⚡ = **Immediate Action.** Disabling ads for a stream is reversible and safe during emergencies.

---

## Overview

This runbook covers procedures for troubleshooting ad insertion issues during live streams and on-demand playback. Ad problems can cause playback failures, infinite loops, audio issues, or poor user experience.

---

## When to Use

* Streams failing during ad breaks
* Infinite ad playback loops
* Ads not loading (long black screens)
* Audio/video issues during ads
* Ad pod duration exceeding limits
* VAST/VMAP response failures

---

## Risk Assessment

| Action | Risk Level | Approval Required |
|--------|------------|-------------------|
| Check ad health status | Low | NOC can execute |
| Disable ads for single stream | Medium | Ad Ops awareness |
| Reduce ad frequency | Medium | Ad Ops required |
| Disable ads platform-wide | High | Ad Ops + Revenue Lead |

---

## Ad Insertion Architecture

```
Stream Request → Ad Decision Server → VAST Response → 
                        ↓
                   Ad Selection
                        ↓
    Video Player ← Ad Creative ← Ad CDN
```

---

## Diagnostic Steps

### 1. ⚡ CHECK AD SYSTEM HEALTH

1. Open **Ad Operations Dashboard**
2. Review system status:
   ```
   Ad Decision Server: [healthy/degraded]
   VAST Response Rate: [percentage]
   Ad Fill Rate: [percentage]
   Average Pod Duration: [seconds]
   Creative Error Rate: [percentage]
   ```

---

### 2. ⚡ CHECK SPECIFIC STREAM ADS

1. Search by stream ID or game ID
2. Review ad insertion status:
   ```
   Stream: [Stream ID]
   Ad Insertion: [enabled/disabled]
   Last Ad Request: [timestamp]
   Response Status: [success/fail]
   Current Pod: [position in stream]
   ```

---

### 3. IDENTIFY ISSUE CATEGORY

| Symptom | Likely Cause | Immediate Action |
|---------|--------------|------------------|
| Black screen during ads | Creative not loading | Check ad CDN |
| Stream fails in ad break | VAST timeout | Disable ads for stream |
| Infinite ad loop | Ad server logic error | Disable ads for stream |
| Audio only (no video) | Creative encoding | Report to Ad Ops |
| Excessive ad length | Pod duration misconfigured | Reduce pod length |

---

## Operator Override Actions

### ⚡ CHECK AD CREATIVE DELIVERY

Verify ads are being delivered from CDN:

1. In Ad Dashboard, go to **Creative Delivery**
2. Enter stream ID
3. Review creative status:
   ```
   Creative ID: [id]
   CDN URL: [url]
   Delivery Status: [success/failed]
   Error: [if applicable]
   ```

**If creative failing:** CDN issue, not ad logic issue

---

### ⚡ DISABLE ADS FOR SPECIFIC STREAM (Medium Risk)

For streams failing during ad breaks:

1. Open **Stream Ad Control**
2. Search for affected stream
3. Click **Disable Ad Insertion**
4. Set reason: "Emergency - playback failure"
5. Set duration: [Until end of event / Manual reset]
6. Click **Apply**

**Impact:** Revenue loss for that stream's ad inventory

**Notify:** Ad Operations that ads were disabled

**Revert:** Re-enable after stream ends or issue resolved

---

### REDUCE AD FREQUENCY (Medium Risk)

If ads causing repeated issues:

1. Get **Ad Ops** approval
2. In **Ad Configuration**
3. Locate stream or event
4. Adjust settings:
   - Reduce ads per hour
   - Shorten max pod duration
   - Increase minimum gap between breaks
5. Apply changes

**Requires:** Ad Operations approval

---

### DISABLE ADS FOR PLATFORM (High Risk)

**ONLY for platform-wide ad system failure:**

1. Get approval from:
   - Ad Operations Lead
   - Revenue/Business Lead
   
2. In **Ad Master Control**:
   - Navigate to **Emergency Controls**
   - Select **Disable All Ad Insertion**
   - Document approval and reason
   - Set duration (max 4 hours)
   - Click **Execute**

3. Monitor playback health
4. Re-enable as soon as ad system stable

**Requires:** VP-level approval for extended outage

---

## Common Issues and Solutions

### VAST Timeout

**Symptoms:** Long pause before/during ads, then skip or fail

**Solutions:**
1. Check ad decision server latency
2. Increase client-side timeout (if configurable)
3. Disable ads if persistent

### Ad Creative Won't Play

**Symptoms:** Black screen, audio only, or freeze during ad

**Solutions:**
1. Verify creative encoding compatibility
2. Check ad CDN delivery
3. Report creative ID to Ad Ops for removal

### Infinite Ad Loop

**Symptoms:** Same ad plays repeatedly, stream doesn't resume

**Solutions:**
1. Disable ads for affected stream immediately
2. Report to Ad Ops for server-side investigation
3. May indicate tracking pixel failure

### Ad/Content Audio Mismatch

**Symptoms:** Audio levels very different between ads and content

**Solutions:**
1. Report to Ad Ops for audio normalization
2. Not typically an emergency action

---

## Escalation Procedures

### Ad Server Outage

If ad decision server down:
1. Contact **Ad Operations** immediately
2. Consider platform-wide ad disable
3. Coordinate with Revenue team

### Third-Party Ad Provider Issues

If issue is with external ad providers:
1. Contact **Ad Operations**
2. They coordinate with ad partners
3. May need to disable specific ad sources

### Revenue Impact Assessment

For any significant ad disruption:
1. Document duration and scope
2. Notify **Ad Operations**
3. They will assess revenue impact

---

## Post-Procedure

* Document all ad disable actions
* Note streams affected and duration
* Re-enable ads when issue resolved
* Report persistent issues to Ad Ops for RCA

---

## Escalation Contacts

| Issue | Team | Contact |
|-------|------|---------|
| Ad insertion logic | Ad Operations | #ad-operations |
| Creative issues | Ad Creative Team | Via Ad Ops |
| CDN delivery | CDN Operations | #cdn-operations |
| Player integration | Video Platform | #video-platform |
| Revenue questions | Ad Revenue | Via Ad Ops |

---

## Related Runbooks

* VAST/VMAP Error Handling
* Ad CDN Health Check
* Video Player Troubleshooting
