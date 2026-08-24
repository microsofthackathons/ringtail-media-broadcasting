# Smart TV and Connected Device Troubleshooting

> **Operator Override Available:** NOC operators can trigger device-specific cache flushes and enable legacy compatibility modes for affected device categories.
>
> ⚡ = **Immediate Action.** Safe to execute for device-specific issues.

---

## Overview

This runbook covers procedures for troubleshooting issues on Smart TVs and connected devices including Samsung, LG, Roku, Amazon Fire TV, Apple TV, gaming consoles (Xbox, PlayStation), and other OTT platforms.

---

## When to Use

* Smart TV app failing to launch
* Device-specific playback failures
* App crashes on specific device models
* Certificate errors on older devices
* Connected device firmware compatibility issues

---

## Supported Devices Reference

| Platform | App Version | Min Firmware | Team Owner |
|----------|-------------|--------------|------------|
| Samsung Tizen | 4.x | 2019+ | Connected Devices |
| LG webOS | 3.x | 2018+ | Connected Devices |
| Roku | 5.x | OS 10+ | Connected Devices |
| Amazon Fire TV | 4.x | Fire OS 6+ | Connected Devices |
| Apple TV | 2.x | tvOS 14+ | iOS Team |
| Xbox | 3.x | Latest | Gaming Platforms |
| PlayStation | 3.x | Latest | Gaming Platforms |

---

## Risk Assessment

| Action | Risk Level | Approval Required |
|--------|------------|-------------------|
| Device-specific cache flush | Low | NOC can execute |
| Enable legacy mode | Low | NOC can execute |
| Device config override | Medium | Connected Devices awareness |
| App takedown from store | High | Product + Engineering Lead |

---

## Diagnostic Steps

### 1. ⚡ IDENTIFY AFFECTED DEVICE SCOPE

Gather information:
- Device platform (Samsung, LG, etc.)
- Device model year / firmware version
- App version installed
- Error message displayed
- Percentage of device category affected

---

### 2. ⚡ CHECK DEVICE PLATFORM HEALTH

1. Open **Connected Devices Dashboard**
2. Select affected platform
3. Review metrics:
   ```
   Error Rate by Device: [percentage]
   Crash Rate: [crashes per 1000 sessions]
   App Launch Success: [percentage]
   API Error Rate: [percentage by endpoint]
   ```

---

### 3. IDENTIFY ISSUE CATEGORY

| Symptom | Likely Cause | Action |
|---------|--------------|--------|
| App won't launch | Crash on init | Check crash logs |
| Black screen | Rendering issue | Enable legacy mode |
| Certificate error | TLS issue | Flush cert cache |
| "Update Required" | Forced update | Verify store version |
| Playback fails | DRM/codec issue | Check device capabilities |

---

## Operator Override Actions

### ⚡ DEVICE-SPECIFIC CDN CACHE FLUSH (Low Risk)

For certificate or static asset issues on specific devices:

1. Open **CDN Management Portal**
2. Navigate to **Device-Specific Purge**
3. Select affected device category
4. Choose purge scope:
   - [ ] SSL/TLS certificates
   - [ ] Static assets
   - [ ] App configuration
5. Click **Execute Purge**

**When to Use:**
- Certificate rotation not propagating to older devices
- Static assets showing old versions
- Configuration changes not reaching devices

---

### ⚡ ENABLE LEGACY COMPATIBILITY MODE (Low Risk)

For older devices having compatibility issues:

1. Open **Device Configuration Portal**
2. Navigate to **Compatibility Settings**
3. Select affected device category and model year range
4. Enable **Legacy Mode**:
   - [ ] Legacy video codec (H.264 only)
   - [ ] Legacy DRM (Widevine L3)
   - [ ] Legacy API endpoints
   - [ ] Reduced feature set
5. Click **Apply**

**Impact:** Affected devices use simplified player with fewer features

**When to Use:**
- Newer features causing crashes on older firmware
- Codec compatibility issues
- DRM level mismatches

---

### ENABLE MAINTENANCE MODE FOR DEVICE (Medium Risk)

Show maintenance message instead of crashing:

1. In Device Configuration Portal, go to **Maintenance Mode**
2. Select affected device category
3. Enable maintenance mode
4. Set custom message:
   ```
   We're experiencing technical difficulties on this device.
   Our team is working to resolve this. Please try again soon.
   ```
5. Set estimated duration

**Requires:** Connected Devices team awareness

**When to Use:**
- Critical issue affecting device category
- While fix is being deployed
- To prevent repeated crash loops

---

## Platform-Specific Troubleshooting

### Samsung Tizen

**Common Issues:**
- TLS certificate chain problems on 2019-2021 models
- Memory pressure on 2018 and older models
- Firmware-specific playback bugs

**Quick Checks:**
1. Verify cert chain includes intermediate certs
2. Check app memory usage in Tizen logs
3. Test on multiple firmware versions

### LG webOS

**Common Issues:**
- WebSocket connection limits
- DRM session management on older models
- App hibernation state recovery

**Quick Checks:**
1. Verify WebSocket connection count
2. Check DRM license acquisition logs
3. Test app resume from background

### Roku

**Common Issues:**
- Channel certification requirements
- Memory limits on older sticks
- Network timeout handling

**Quick Checks:**
1. Review Roku developer logs
2. Check channel certification status
3. Test on low-memory devices

### Gaming Consoles

**Common Issues:**
- Console-specific DRM requirements
- HDR/Dolby Vision compatibility
- Controller input handling

**Quick Checks:**
1. Verify platform-specific DRM
2. Test HDR fallback behavior
3. Check console certification status

---

## Escalation Procedures

### App Store Issues

If app needs emergency update:
1. Contact **Connected Devices Team**
2. Prepare hotfix build
3. Submit to device manufacturer for expedited review

### Firmware Compatibility Issues

If device firmware causing issues:
1. Document affected firmware versions
2. Contact **Connected Devices Team**
3. May require manufacturer coordination

### DRM Issues

If DRM-related on specific devices:
1. Contact **Content Security Team**
2. Verify DRM level requirements
3. Check device DRM certifications

---

## Post-Procedure

* Document affected device scope in ticket
* Note any compatibility modes enabled
* Schedule follow-up to disable temporary modes
* Track device-specific metrics for improvement

---

## Escalation Contacts

| Platform | Team | Contact Method |
|----------|------|----------------|
| Samsung/LG/Roku/Fire TV | Connected Devices | #connected-devices |
| Apple TV | iOS Team | #ios-platform |
| Xbox/PlayStation | Gaming Platforms | #gaming-platforms |
| DRM issues | Content Security | #content-security |
| CDN issues | CDN Operations | #cdn-operations |

---

## Related Runbooks

* Certificate Rotation Procedure
* DRM Troubleshooting
* Device Firmware Compatibility Matrix
