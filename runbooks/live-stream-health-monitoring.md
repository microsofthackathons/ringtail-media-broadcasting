# Live Stream Health Monitoring and Triage

> **Operator Actions Available:** NOC operators can perform stream health checks and initial triage to identify issue category before engineering escalation.
>
> ⚡ = **Immediate Action.** Execute to gather diagnostic information.

---

## Overview

This runbook covers procedures for monitoring live stream health during NBA, WNBA, and international basketball game broadcasts. Use to quickly identify streaming issues and route to the correct engineering team.

---

## When to Use

* Users reporting stream unavailability
* Elevated buffering complaints
* Video quality issues during live games
* Audio problems (sync, missing, distorted)
* Stream startup failures

---

## Quick Reference: Issue Routing

| Symptom | Primary Team | Secondary Team |
|---------|--------------|----------------|
| No stream available | Production Operations | Platform Engineering |
| Buffering/loading | CDN Operations | Video Platform |
| Low video quality | Video Platform | Encoding Team |
| Audio issues | Encoding Team | Broadcast Engineering |
| Stats overlay missing | Data Services | Video Platform |
| Captions missing | Caption Services | Accessibility Team |
| DRM errors | Video Platform | Content Security |
| Regional blackout issues | Content Ops | Legal/Compliance |

---

## Diagnostic Steps

### 1. ⚡ CHECK STREAM AVAILABILITY

1. Open **Stream Health Dashboard**
2. Locate the affected game/event
3. Verify stream status indicators:
   - 🟢 Green: Stream healthy
   - 🟡 Yellow: Degraded performance
   - 🔴 Red: Stream unavailable

---

### 2. ⚡ VERIFY ENCODER STATUS

1. Navigate to **Encoder Dashboard**
2. Check affected game encoder:
   ```
   Status: [Running/Stopped/Error]
   Input Feed: [Present/Absent]
   Output Bitrates: [list of active renditions]
   Audio Channels: [count and status]
   ```

**If encoder shows error:** Escalate to Encoding Team immediately

---

### 3. ⚡ CHECK CDN HEALTH

1. Open **CDN Health Dashboard**
2. Review metrics for affected regions:
   ```
   Cache Hit Rate: [should be >95%]
   Origin Response Time: [should be <200ms]
   Error Rate: [should be <0.1%]
   Active Edge Nodes: [count by region]
   ```

**If CDN degraded:** See CDN Cache Flush Procedure

---

### 4. ⚡ VERIFY MANIFEST AVAILABILITY

Test manifest URLs for affected stream:

1. Navigate to **Stream Test Tool**
2. Enter stream ID or game ID
3. Click **Test Manifest**
4. Review results:
   ```
   Master Manifest: [200 OK / Error]
   Variant Manifests: [count successful]
   Segment Availability: [percentage]
   ```

**If manifest errors:** Escalate to Video Platform

---

### 5. CHECK ORIGIN SERVER HEALTH

1. Open **Origin Server Dashboard**
2. Verify origin cluster status:
   ```
   Healthy Nodes: [count]
   Request Rate: [requests/second]
   Response Latency: [p50, p99]
   Error Rate: [percentage]
   ```

**If origin degraded:** Escalate to Platform Engineering

---

## Issue-Specific Triage

### Buffering Issues

1. Check user's reported bitrate vs available bandwidth
2. Review CDN cache hit rate
3. Check origin server load
4. Verify segment availability

**Quick fix:** If CDN cache hit rate low, execute cache warm-up

### Video Quality Issues

1. Verify encoder output bitrates
2. Check ABR algorithm requests vs delivery
3. Review source feed quality
4. Check transcoding pipeline status

**Quick fix:** For ABR issues, check server-side config changes

### Audio Issues

1. Verify encoder audio input status
2. Check audio/video sync offset
3. Review audio codec configuration
4. Check for commentary feed status

**Quick fix:** Audio issues typically require Encoding Team

### Stream Startup Failures

1. Test DRM license acquisition
2. Verify authentication flow
3. Check content entitlement
4. Review player initialization logs

**Quick fix:** If DRM failure, check license server status

---

## Severity Classification

| Impact | Severity | Response Time |
|--------|----------|---------------|
| All users, all streams | P1 | Immediate |
| Single major game affected | P1 | Immediate |
| Multiple games degraded | P2 | 15 minutes |
| Single game degraded | P2 | 15 minutes |
| Subset of users affected | P3 | 30 minutes |

---

## Escalation Paths

### P1 Escalation (Critical)

1. Open video bridge immediately
2. Page primary on-call for affected systems
3. Notify L2 Manager
4. Begin incident timeline documentation

### P2 Escalation (High)

1. Create incident ticket
2. Notify relevant team in Slack
3. Request engineer join investigation
4. Monitor for escalation to P1

---

## Post-Triage Actions

* Document all diagnostic findings in ticket
* Include dashboard screenshots if helpful
* Note any temporary mitigations applied
* Estimate user impact and affected games

---

## Related Runbooks

* CDN Cache Flush Procedure
* Encoder Emergency Restart
* Origin Server Failover
* DRM License Server Health Check
