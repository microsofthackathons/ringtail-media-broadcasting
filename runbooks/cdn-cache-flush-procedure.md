# CDN Cache Flush Procedure

> **Operator Override Available:** NOC operators can execute this procedure independently as a temporary workaround while engineering teams investigate root cause.
>
> ⚡ = **Immediate Action.** Safe to execute without engineering approval.

---

## Overview

This runbook covers procedures for flushing CDN cache to resolve content delivery issues. Cache flushes can resolve stale content, certificate propagation delays, and static asset issues.

---

## When to Use

* Users seeing outdated content after deployments
* TLS certificate updates not propagating
* Static assets (JavaScript, CSS, images) returning old versions
* Manifest files containing stale segment references
* Regional content not updating after schedule changes

---

## Risk Assessment

| Action | Risk Level | Approval Required |
|--------|------------|-------------------|
| Path-specific flush | Low | NOC can execute |
| Single region flush | Low | NOC can execute |
| Global cache flush | Medium | L2 Manager sign-off |
| Full CDN purge | High | Engineering Lead required |

---

## Prerequisites

* Access to CDN Management Portal
* Confirmed symptom that is cache-related (not origin)
* Knowledge of affected content paths or regions

---

## Steps

### 1. ⚡ IDENTIFY CACHE TARGET

Determine what needs to be flushed:

| Symptom | Flush Target | Example Path |
|---------|--------------|--------------|
| Old static assets | Static asset path | `/static/*` |
| Stale video manifests | Manifest path | `/live/*/manifest.m3u8` |
| Old certificate | SSL cache | Global SSL flush |
| Regional content issue | Specific region | `us-east`, `eu-west` |

---

### 2. ⚡ SELECT FLUSH SCOPE

**Path-Specific Flush (Recommended):**
```
Path: /static/js/player-*.js
Regions: All
```

**Regional Flush:**
```
Path: /*
Regions: us-east-1
```

**Global Flush (L2 Approval):**
```
Path: /*
Regions: All
```

---

### 3. ⚡ EXECUTE CACHE FLUSH

1. Log into **CDN Management Portal**
2. Navigate to **Cache Operations > Purge**
3. Enter the target path pattern
4. Select target regions
5. Add ticket number in the "Reason" field
6. Click **Execute Purge**

---

### 4. VERIFY FLUSH COMPLETION

* Check CDN portal for purge status (typically 2-5 minutes)
* Verify affected content returns updated version
* Check response headers for cache status (`X-Cache: MISS` indicates fresh fetch)

---

### 5. MONITOR FOR SIDE EFFECTS

After a large flush, monitor:

* Origin server load (may spike as cache refills)
* CDN hit rate (will temporarily decrease)
* User-reported issues (should resolve)

---

## Post-Procedure

* Update incident ticket with flush details
* Notify engineering team of flush execution
* If issue persists, escalate to CDN Operations team

---

## Rollback

Cache flushes cannot be "rolled back" but the cache will naturally repopulate from origin. If a flush causes issues:

1. Check origin server health
2. Verify origin content is correct
3. Contact CDN Operations if issues persist

---

## Escalation

| Condition | Escalate To |
|-----------|-------------|
| Flush not completing | CDN Operations |
| Origin overloaded post-flush | Platform Engineering |
| Incorrect content from origin | Content Delivery Team |
| Global flush required | L2 Manager, then CDN Ops |

---

## Related Runbooks

* Certificate Expiration Response
* Static Asset Deployment Issues
* Origin Server Health Check
