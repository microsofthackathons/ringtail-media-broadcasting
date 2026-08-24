# Regional Blackout Override Procedure

> **Requires Content Operations/Legal Coordination:** Blackout overrides affect contractual obligations. Execute only with explicit approval.
>
> ⚠️ = **Caution.** Incorrect blackout configuration can have legal and contractual implications.

---

## Overview

This runbook covers procedures for handling regional blackout issues, including emergency overrides when blackouts are incorrectly applied or need to be lifted for legitimate reasons. Blackouts are typically controlled by content rights agreements with leagues and broadcasters.

---

## When to Use

* Users incorrectly blocked from content they should access
* Blackout rules not applying correctly (users seeing blocked content)
* Emergency blackout lift approved by Legal/Content Ops
* Blackout data feed issues from league provider
* Regional detection errors affecting user access

---

## Risk Assessment

| Action | Risk Level | Approval Required |
|--------|------------|-------------------|
| Verify blackout status | Low | NOC can execute |
| Refresh blackout data | Low | NOC can execute |
| User-specific override | Medium | Content Ops required |
| Regional override | High | Legal + Content Ops required |
| Complete blackout disable | Critical | VP-level approval |

---

## Understanding Blackouts

### Blackout Types

| Type | Description | Typical Duration |
|------|-------------|------------------|
| Local | Home market teams | Game duration |
| National | Nationally televised games | Game duration |
| League-wide | League policy events | Event duration |
| International | Territorial rights | Varies |

### Blackout Determination Flow

```
User Request → Location Detection → Blackout Rules Check → 
               ↓                    ↓
           (IP Geolocation)    (League Feed + Local Rules)
               ↓                    ↓
           Region Determined → Rule Match → Block or Allow
```

---

## Diagnostic Steps

### 1. ⚡ VERIFY USER'S BLACKOUT STATUS

1. Open **Blackout Verification Tool**
2. Enter user information:
   - User ID or email
   - Content ID (game/event)
3. View blackout determination:
   ```
   User Location: [City, State]
   Detected IP: [IP address]
   Content: [Game details]
   Blackout Status: [Blocked/Allowed]
   Blackout Reason: [Rule that applied]
   ```

---

### 2. ⚡ VERIFY BLACKOUT RULES SOURCE

1. Open **Blackout Rules Dashboard**
2. Check league data feed status:
   ```
   Last Sync: [timestamp]
   Sync Status: [Success/Failed]
   Rules Count: [number]
   Feed Health: [healthy/degraded]
   ```

---

### 3. IDENTIFY ISSUE CATEGORY

| Symptom | Likely Cause | Action |
|---------|--------------|--------|
| Users incorrectly blocked | Geolocation error | Verify location detection |
| Wrong blackout applied | Rules data issue | Refresh blackout data |
| All users blocked | Feed sync failure | Emergency rules refresh |
| Users incorrectly allowed | Rules not applying | Check rule engine |

---

## Operator Override Actions

### ⚡ REFRESH BLACKOUT DATA (Low Risk)

Force re-sync of blackout rules from league:

1. In Blackout Rules Dashboard, click **Force Sync**
2. Wait for sync to complete (typically 1-2 minutes)
3. Verify rule count matches expected
4. Test with affected user scenario

**When to Use:**
- Rules data appears stale
- After league schedule changes
- When feed status shows issues

---

### ⚡ VERIFY GEOLOCATION ACCURACY (Low Risk)

Check if user's location is being detected correctly:

1. Open **Geolocation Debug Tool**
2. Enter user's IP address
3. Compare detected location with user-reported location
4. If mismatch, check IP database update status

**Common Issues:**
- VPN/proxy detection
- Corporate network IP ranges
- Mobile carrier IP misclassification

---

### USER-SPECIFIC BLACKOUT OVERRIDE (Medium Risk)

For individual users incorrectly blocked:

1. Verify user's actual location (may require ID verification)
2. Get **Content Operations** approval
3. In **User Management Portal**:
   - Search for user
   - Navigate to **Blackout Exemptions**
   - Add exemption:
     ```
     Reason: [Geolocation error / Legal exemption / etc.]
     Content Scope: [Specific game / All content]
     Duration: [One-time / Event duration / Permanent]
     Approved By: [Content Ops name]
     ```
4. Document approval in ticket

**Requires:** Content Operations approval

---

### REGIONAL BLACKOUT OVERRIDE (High Risk)

For region-wide blackout issues:

1. **Obtain explicit approval from:**
   - Content Operations Manager
   - Legal representative
   
2. Document approval:
   ```
   Override Request: [Description]
   Region Affected: [Geographic scope]
   Content Affected: [Games/events]
   Reason: [Technical error / Legal approval / etc.]
   Approved By: [Names and timestamps]
   ```

3. In **Blackout Admin Console**:
   - Navigate to **Regional Overrides**
   - Create override rule:
     - Region: [affected area]
     - Content: [affected games]
     - Override Type: [allow/block]
     - Duration: [start and end time]
   - Submit for approval workflow

**Requires:** Legal + Content Ops explicit approval

---

## Escalation Procedures

### Geolocation Provider Issues

If IP geolocation data is incorrect:
1. Contact **Platform Engineering**
2. Check geolocation database update status
3. May need to submit correction to geolocation vendor

### League Data Feed Issues

If league blackout feed is failing:
1. Contact **League Data Integration Team**
2. Check league provider status page
3. May need manual rule entry as temporary measure

### Legal/Contractual Questions

For any questions about blackout applicability:
1. **Do not override without approval**
2. Contact **Content Operations**
3. They will coordinate with Legal if needed

---

## Post-Procedure

* Document all override actions with approvals
* Set expiration reminders for temporary overrides
* Report geolocation errors to vendor if applicable
* Review for patterns indicating systemic issues

---

## Escalation Contacts

| Issue | Team | Contact |
|-------|------|---------|
| User verification | Customer Support | Escalation queue |
| Override approval | Content Operations | #content-operations |
| Legal questions | Legal Team | Via Content Ops |
| Geolocation issues | Platform Engineering | #platform-engineering |
| League feed issues | League Data Integration | #league-data-support |

---

## Related Runbooks

* Geolocation Service Health Check
* League Data Feed Monitoring
* User Verification Procedures
