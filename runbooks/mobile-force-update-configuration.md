# Mobile App Force Update Configuration

> **Requires Engineering Approval:** Force update pushes require Mobile Team sign-off due to user experience impact.
>
> ⚠️ = **Caution.** This procedure affects all users on specified app versions.

---

## Overview

This runbook covers procedures for configuring force update prompts to require users to update their mobile apps. Use when critical bugs or security issues require immediate remediation across the user base.

---

## When to Use

* Critical security vulnerability discovered in released app version
* App-breaking bug affecting significant user population
* Backend API deprecation requiring client update
* Regulatory compliance requiring specific app version

---

## Risk Assessment

| Action | Risk Level | Approval Required |
|--------|------------|-------------------|
| Soft prompt (dismissible) | Low | Mobile Team Lead |
| Hard force update | High | Mobile Team Lead + L2 Manager |
| Minimum version blocking | High | Mobile Team Lead + Product |

---

## Prerequisites

* Fixed app version available in App Store / Play Store
* Confirmed which versions are affected
* Mobile Team approval obtained
* Stakeholder notification prepared

---

## Decision Matrix

| Scenario | Update Type | User Experience |
|----------|-------------|-----------------|
| Minor bug fix | No force update | Normal update flow |
| Significant bug | Soft prompt | Dismissible dialog on launch |
| Critical bug | Hard force update | Cannot use app until updated |
| Security issue | Hard force update | Immediate block |

---

## Procedure

### 1. OBTAIN APPROVALS

**Required sign-offs:**
- [ ] Mobile Team Lead (iOS or Android as applicable)
- [ ] L2 Manager (for hard force update)
- [ ] Product Manager (if affecting app store metrics)

Document approvals in incident ticket.

---

### 2. ⚠️ CONFIGURE FORCE UPDATE (iOS)

1. Log into **Mobile Configuration Portal**
2. Navigate to **iOS > Version Control**
3. Set fields:
   ```
   Minimum Supported Version: [new version]
   Force Update Type: [soft/hard]
   Affected Versions: [comma-separated list]
   Update Message: [user-facing message]
   Link: App Store URL
   ```
4. Click **Save Draft**
5. Review configuration in staging environment
6. Click **Publish**

---

### 3. ⚠️ CONFIGURE FORCE UPDATE (Android)

1. Log into **Mobile Configuration Portal**
2. Navigate to **Android > Version Control**
3. Set fields:
   ```
   Minimum Supported Version: [new version]
   Force Update Type: [soft/hard]
   Affected Versions: [comma-separated list]
   Update Message: [user-facing message]
   Link: Play Store URL
   ```
4. Click **Save Draft**
5. Review configuration in staging environment
6. Click **Publish**

---

### 4. CRAFT USER MESSAGE

**Template for critical updates:**
```
A required update is available. Please update to continue 
watching live games and accessing content.

Version [X.X.X] includes important fixes for:
- [Brief description of issue]

Thank you for your patience!
```

---

### 5. NOTIFY STAKEHOLDERS

Before publishing, notify:
- Customer Support (prepare for increased contacts)
- Social Media team (prepare response template)
- NOC (monitor for related issues)

---

### 6. MONITOR UPDATE ADOPTION

After publishing:
1. Monitor app version distribution in analytics
2. Track customer support ticket volume
3. Watch for reports of update issues
4. Track App Store / Play Store review sentiment

---

## Rollback Procedure

If force update causes issues:

1. Navigate to **Version Control** in Mobile Configuration Portal
2. Increase **Minimum Supported Version** cooldown
3. Or change **Force Update Type** to "none"
4. Publish immediately
5. Notify Mobile Team to prepare hotfix if app issue

---

## Special Considerations

### App Store Review Delays

* iOS App Store reviews can take 24-48 hours
* Use TestFlight for pre-release validation
* Request expedited review for critical fixes

### Play Store Staged Rollouts

* Android allows percentage-based rollouts
* Consider 10% → 50% → 100% for non-critical updates
* Critical security issues should be 100% immediate

### Users Without App Store Access

* Some users on older devices may not be able to update
* Consider grace period for hard force update
* Document affected device/OS combinations

---

## Escalation

| Issue | Escalate To |
|-------|-------------|
| Force update not triggering | Mobile Team |
| Users unable to update | App Store / Play Store support |
| High support ticket volume | Customer Experience Lead |
| Update causing new issues | Mobile Team (immediate) |

---

## Related Runbooks

* iOS App Emergency Release Procedure
* Android App Emergency Release Procedure
* Customer Communication Templates
