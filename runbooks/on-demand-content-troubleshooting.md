# On-Demand Content Troubleshooting

> **Operator Override Available:** NOC operators can trigger content re-ingestion and clear manifest caches to resolve common on-demand content issues.
>
> ⚡ = **Immediate Action.** Safe to execute for content-specific issues.

---

## Overview

This runbook covers procedures for troubleshooting on-demand content issues including game replays, highlights, classic games, and archived content. On-demand issues do not affect live streaming.

---

## When to Use

* Game replays not available after expected processing time
* Highlights not playing or showing errors
* Classic games / archived content not loading
* Content showing wrong metadata (title, thumbnail)
* Content available on some platforms but not others

---

## Risk Assessment

| Action | Risk Level | Approval Required |
|--------|------------|-------------------|
| Re-trigger content ingestion | Low | NOC can execute |
| Clear manifest cache | Low | NOC can execute |
| Reset content metadata | Low | Content Ops awareness |
| Emergency content publish | Medium | Content Ops required |
| Storage failover | High | Platform Engineering |

---

## Content Pipeline Overview

```
Source → Ingestion → Transcoding → Packaging → CDN → User
         (VOD Ingest) (Encode)    (Manifest)   (Edge)
```

Each stage can fail independently. Identify which stage has the issue.

---

## Diagnostic Steps

### 1. ⚡ CHECK CONTENT STATUS

1. Open **Content Management Portal**
2. Search for affected content by:
   - Game ID
   - Content title
   - Date
3. Review content status:
   ```
   Ingestion: [Complete/Processing/Failed]
   Transcoding: [Complete/Processing/Failed]
   Publishing: [Published/Pending/Failed]
   Platforms: [list of published platforms]
   ```

---

### 2. IDENTIFY FAILURE POINT

| Status | Issue Location | Action |
|--------|----------------|--------|
| Ingestion Failed | Source/ingest pipeline | Re-trigger ingestion |
| Transcoding Failed | Encoding pipeline | Contact Encoding Team |
| Publishing Failed | Distribution pipeline | Re-trigger publish |
| Published but unavailable | CDN/Cache issue | Clear manifest cache |
| Platform-specific failure | Platform integration | Check platform status |

---

## Operator Override Actions

### ⚡ RE-TRIGGER CONTENT INGESTION (Low Risk)

For content stuck in failed or incomplete state:

1. In Content Management Portal, locate affected content
2. Click **Actions > Re-ingest Content**
3. Select source:
   - [ ] Original source
   - [ ] Backup source (if available)
4. Click **Start Ingestion**
5. Monitor progress (typical time: 15-30 minutes)

**When to Use:**
- Ingestion status shows "Failed"
- Content was partially ingested
- Source file was updated

---

### ⚡ CLEAR MANIFEST CACHE (Low Risk)

For content published but not playing:

1. Navigate to **CDN Controls** in Content Portal
2. Enter content ID
3. Click **Clear Manifest Cache**
4. Wait 2-3 minutes for propagation

**When to Use:**
- Content shows "Published" but won't play
- Manifest returns stale or empty segments
- After content re-packaging

---

### ⚡ FORCE CONTENT REPUBLISH (Low Risk)

Push content to specific platform:

1. In Content Management Portal, locate content
2. Click **Publishing > Platform Status**
3. Select platform(s) showing issues
4. Click **Force Republish**

**When to Use:**
- Content available on some platforms but not others
- Metadata updates not reflecting
- After platform-specific fixes

---

### RESET CONTENT METADATA (Medium Risk)

If metadata is incorrect:

1. Navigate to **Content Details**
2. Click **Edit Metadata**
3. Correct fields:
   - Title
   - Description
   - Thumbnail URL
   - Duration
   - Categories/Tags
4. Click **Save and Republish**

**Requires:** Content Ops awareness if significant changes

---

## Common Issues and Solutions

### "Content Not Found" Error

1. Verify content exists in CMS
2. Check publishing status for user's platform
3. Verify user has entitlement to content
4. Check regional availability settings

### "Unable to Play" Error

1. Clear manifest cache
2. Test playback in Content Preview tool
3. Verify transcoded renditions exist
4. Check DRM packaging status

### Highlights Not Generating

1. Check ML processing pipeline status
2. Verify source game recording completed
3. Check GPU resource availability
4. See: ML Resource Contention runbook

### Wrong Thumbnail/Title

1. Verify metadata in CMS is correct
2. Clear CDN metadata cache
3. Force republish to affected platforms
4. Check for duplicate content entries

---

## Escalation Procedures

### Transcoding Failure

1. Contact **Encoding Team**
2. Provide content ID and error logs
3. May require manual transcoding job

### Storage Issues

1. Contact **Platform Engineering**
2. Check storage health dashboard
3. May require storage failover

### DRM Packaging Issues

1. Contact **Content Security Team**
2. Verify DRM license configuration
3. May require re-packaging with new keys

---

## Post-Procedure

* Update incident ticket with actions taken
* Notify Content Operations of any metadata changes
* If bulk issue, identify affected content scope
* Document any patterns for root cause analysis

---

## Escalation Contacts

| Issue | Team | Contact Method |
|-------|------|----------------|
| Ingestion failures | Content Ingest Team | #content-ingest |
| Transcoding failures | Encoding Team | #encoding-platform |
| Publishing failures | Content Distribution | #content-distribution |
| DRM/Security issues | Content Security | #content-security |
| Storage issues | Platform Engineering | #platform-engineering |

---

## Related Runbooks

* Content Ingestion Pipeline Health
* Encoding Pipeline Troubleshooting
* DRM Packaging Issues
* ML Highlights Generation
