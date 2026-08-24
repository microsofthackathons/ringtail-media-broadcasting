---
title: Ringtail Media Broadcasting
description: Operational hackathon repository for a fictional sports media broadcasting company
---

## Overview

Ringtail Media Broadcasting is a fictional media company that provides live
broadcasting services for sports leagues. This repository is a shared workspace
for operational hackathons focused on diagnosing incidents, restoring services,
and improving the reliability of live sports broadcasts.

The scenarios and documentation in this repository are intended for learning,
experimentation, and collaborative problem-solving. They do not describe a real
company or production environment.

## Repository contents

* [knowledge](knowledge/) contains architecture notes, service references,
	terminology, and other background material
* [runbooks](runbooks/) contains repeatable procedures for diagnosing and
	resolving common operational failures
* [rcas](rcas/) contains root cause analyses that capture incident impact,
	timelines, contributing factors, and corrective actions

## Operational hackathons

Hackathon participants can use this repository to practice realistic broadcast
operations, including:

* Investigating degraded or interrupted live streams
* Responding to latency, encoding, ingest, distribution, and playback failures
* Coordinating incident response across operational roles
* Turning investigation findings into reusable runbooks
* Documenting lessons learned through blameless root cause analysis

A typical exercise starts with shared context from `knowledge/`, uses or tests a
procedure in `runbooks/`, and concludes with an RCA in `rcas/`. Findings should
feed back into the knowledge base and runbooks so that each exercise improves the
next response.

## Contributing content

Keep contributions concise, actionable, and understandable without prior
knowledge of the exercise. When adding operational content:

* State the scenario, assumptions, and expected outcome
* Use fictional systems, people, leagues, and data
* Include verification and rollback steps in runbooks where applicable
* Separate observed facts from hypotheses in incident investigations
* Record corrective actions with clear owners or roles
* Avoid credentials, secrets, personal information, and production data

Use descriptive, lowercase file names with hyphens, such as
`encoder-failover.md` or `regional-playback-outage.md`.

## Disclaimer

Ringtail Media Broadcasting is fictional. Any resemblance to real companies,
systems, incidents, leagues, or people is coincidental.
