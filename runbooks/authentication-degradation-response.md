# Authentication Service Degradation Response

> **Operator Override Available:** NOC operators can flush authentication caches and enable emergency bypass mode to reduce auth service load during degradation events.
>
> ⚡ = **Immediate Action.** Cache flush is low-risk and can be executed immediately.

---

## Overview

This runbook covers procedures for responding to authentication service degradation. Auth issues prevent users from logging in and can cause session timeouts for existing users.

---

## When to Use

* Users unable to log in across platforms
* Elevated "session expired" errors
* Authentication API latency spikes
* Session validation failures
* OAuth token refresh failures

---

## Risk Assessment

| Action | Risk Level | Approval Required |
|--------|------------|-------------------|
| Auth cache flush | Low | NOC can execute |
| Session extension | Low | NOC can execute |
| Emergency bypass mode | Medium | Identity Platform awareness |
| Auth service restart | High | Identity Platform required |
| Database failover | High | DBA Team required |

---

## Diagnostic Steps

### 1. ⚡ CHECK AUTH SERVICE HEALTH

```
Dashboard: Identity Platform Health
URL: [monitoring-url]/auth-health

Metrics:
- Login success rate (should be >99%)
- Token validation latency (should be <100ms)
- Active sessions count
- Database connection pool utilization
```

---

### 2. IDENTIFY ISSUE CATEGORY

| Symptom | Likely Cause | NOC Action |
|---------|--------------|------------|
| All logins failing | Service down | Escalate immediately |
| Logins slow | Database latency | Flush auth cache |
| Sessions timing out | Token validation slow | Extend session TTL |
| Regional login issues | Regional service issue | Check regional health |
| OAuth errors | Token service issue | Check OAuth provider |

---

## Operator Override Actions

### ⚡ FLUSH AUTHENTICATION CACHE (Low Risk)

Clears cached tokens and forces fresh validation:

1. Log into **Auth Admin Portal**
2. Navigate to **Cache Management**
3. Select **Authentication Token Cache**
4. Click **Flush Cache**
5. Confirm action

**Expected Impact:** Brief increase in database load as cache refills

**When to Use:**
- After auth service deployment
- When stale tokens causing issues
- After certificate rotation

---

### ⚡ EXTEND SESSION TTL (Low Risk)

Reduces session validation frequency:

1. In Auth Admin Portal, go to **Session Configuration**
2. Locate **Session Token TTL**
3. Increase from default (30 min) to **120 min**
4. Click **Apply**

**Revert:** Return TTL to 30 minutes when issue resolved

**When to Use:**
- High session validation load
- Database connection issues
- During major events with high user count

---

### ENABLE EMERGENCY BYPASS MODE (Medium Risk)

Allows existing sessions to continue without validation:

1. Navigate to **Emergency Controls**
2. Enable **Bypass Session Validation**
3. Set duration (max 2 hours)
4. Click **Activate**

**Limitations:**
- New logins still require full auth
- Compromised sessions remain active
- Audit logging reduced

**Requires:** Identity Platform team notification

**When to Use:**
- Auth database completely unavailable
- P1 event during major game

---

## Escalation Procedures

### Auth Service Restart

If operator actions don't resolve:

1. Contact **Identity Platform On-Call**
2. Request auth service rolling restart
3. Expected impact: Users may need to re-login

### Database Connection Pool Increase

If connection pool exhausted:

1. Contact **Identity Platform On-Call**
2. Request connection pool scaling
3. Monitor pool utilization after increase

### Database Failover

If primary database unavailable:

1. Contact **DBA Team On-Call**
2. Request read replica promotion
3. Expected impact: 2-5 minute login outage

---

## Platform-Specific Considerations

### Mobile Apps

* Mobile apps cache auth tokens locally
* Users may not notice auth issues immediately
* Force logout requires app update or server flag

### Web Platform

* Browser tokens refresh more frequently
* Users will see issues sooner
* Can clear cookies as user workaround

### Smart TVs

* Device authentication uses longer-lived tokens
* Issues may take hours to manifest
* Device re-linking may be required

---

## Verification

After any action:

1. Test login flow on affected platforms
2. Monitor login success rate dashboard
3. Check authentication latency metrics
4. Verify session validation working

---

## Post-Procedure

* Document actions taken in incident ticket
* If TTL was extended, schedule revert
* Notify Identity Platform of any bypass activation
* Update incident timeline

---

## Escalation Contacts

| Issue | Team | Contact Method |
|-------|------|----------------|
| Auth service issues | Identity Platform | #identity-platform-alerts |
| Database issues | DBA Team | #dba-oncall |
| OAuth provider issues | Identity Platform | Vendor support ticket |
| CDN auth cache | CDN Operations | #cdn-operations |

---

## Related Runbooks

* Database Connection Pool Exhaustion
* OAuth Provider Failover
* Session Token Migration
