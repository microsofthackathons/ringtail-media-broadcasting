# [INC-20260210-008] 02/10/26 - P2 - Smart TV App Unable to Load on Samsung Devices

---

## Ticket Metadata

| Field | Value |
|-------|-------|
| **Status** | Closed |
| **Project** | Live Media Operations |
| **Type** | RCA |
| **Priority** | 2 - Must Have |
| **Reporter** | Connected Devices Team |
| **Assignee** | Smart TV Platform Lead |
| **Resolution** | Resolved |
| **Components** | Samsung TV App, TLS Certificate, CDN Configuration |
| **Affects Versions** | Samsung TV App v4.2.0 |
| **Fix Versions** | Infrastructure Update |
| **Labels** | SmartTV, Samsung, P2, Certificate, TLS |
| **Remaining Estimate** | Not Specified |
| **Time Spent** | 1 hour 15 minutes |
| **Original Estimate** | Not Specified |
| **Created** | 02/10/26 |
| **Updated** | 02/11/26 |

---

## Issue Links

### Post-Incident Reviews

| Relationship | Ticket | Title | Status |
|---|---|---|---|
| Reviews | INC-20260210-008 | Samsung TV App Load Failure | Closed |
| Related | CHG-4645 | Intermediate Certificate Chain Update | Completed |
| Related | PRB-912 | Certificate Chain Monitoring Implementation | Completed |

---

## Executive Summary

At **6:15 PM ET**, users of Samsung Smart TVs (2019-2021 models) reported the app displaying an error screen when attempting to launch. The issue affected approximately **85,000 Samsung TV users**. The root cause was an expired intermediate TLS certificate in the CDN certificate chain that older Samsung TV firmware did not handle gracefully. The DevOps team updated the CDN certificate chain configuration, and functionality was restored by **7:30 PM ET**.

---

## Root Cause

The CDN TLS configuration included an intermediate certificate (issued by a third-party CA) that expired on 02/10/26. Modern browsers and operating systems handled this gracefully by using cached alternative trust paths, but Samsung Smart TVs running firmware versions prior to 2022 had limited certificate chain recovery capabilities. When these devices attempted to establish TLS connections to the CDN, the expired intermediate certificate caused SSL handshake failures, preventing the app from loading any content.

---

## Issue Classification

- Certificate Expiration
- Third-Party Dependency (CA)
- Device Compatibility Gap

---

## Recovery

The DevOps team identified the expired intermediate certificate through SSL checker tools. They updated the CDN certificate configuration to use the current intermediate certificate and ensured the full valid chain was included. The CDN team flushed edge node certificate caches to propagate the updated chain. Samsung TV app functionality was restored within 75 minutes.

**Operator Override Applied:** NOC instructed CDN team to flush certificate caches globally, accelerating propagation of the fix.

---

## Permanent Corrective Action

1. Implemented automated certificate chain monitoring with 30-day expiration alerts
2. Added intermediate certificate expiration tracking to certificate management system
3. Created device compatibility matrix for TLS requirements (Samsung, LG, Roku, etc.)
4. Scheduled quarterly certificate chain health audits
5. Added synthetic monitoring from Samsung TV user agents to detect similar issues early

---

## Process Improvement

> **Implement End-to-End Certificate Chain Monitoring Including Intermediate Certificates**

---

## Incident Timeline

| Time | Event |
|------|-------|
| **12:01 AM** | Intermediate certificate expires (unnoticed) |
| **6:15 PM** | Customer support receives first Samsung TV complaints |
| **6:25 PM** | NOC correlates reports: Samsung 2019-2021 models only |
| **6:30 PM** | P2 incident declared. Connected Devices team notified. |
| **6:40 PM** | DevOps begins SSL chain investigation |
| **6:55 PM** | Expired intermediate certificate identified |
| **7:05 PM** | Updated certificate chain prepared |
| **7:15 PM** | CDN certificate configuration updated |
| **7:20 PM** | NOC requests CDN cache flush (per runbook) |
| **7:25 PM** | Certificate caches flushed globally |
| **7:30 PM** | Samsung TV apps begin loading successfully |
| **7:45 PM** | Incident resolved. Certificate monitoring enhancement planned. |

---

## Additional Details

| Field | Value |
|-------|-------|
| **Request Type** | RCA |
| **Impacted Platforms** | Samsung Smart TVs (2019-2021 firmware) |
| **Issue Start Time** | 02/10/26 12:01 AM ET (cert expiry) |
| **Issue End Time** | 02/10/26 7:30 PM ET |
| **Resolving Teams** | DevOps, CDN Operations, Connected Devices Team |
| **Users Impacted** | ~85,000 |
| **Event** | NBA Regular Season |

---

## Resolution Description

Samsung Smart TV (2019-2021 models) users were unable to load the app due to an expired intermediate TLS certificate that older Samsung firmware could not recover from. DevOps updated the CDN certificate chain configuration and the NOC coordinated a global cache flush. Post-incident, automated certificate chain monitoring was implemented to provide advance warning of all certificate expirations including intermediate certificates.

---

## Description / Initial Report

> **Bridge:** [Video Bridge Link]
> **Thread:** [#connected-devices-support]
>
> Samsung TV App not loading for users. Error screen displayed. Pattern appears to be Samsung models from 2019-2021. Other Smart TV platforms (LG, Roku) unaffected. Games start at 7:30 PM. Connected Devices and DevOps teams please investigate. NOC standing by for cache flush if needed.
