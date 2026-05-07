---
layout: default
title: security
permalink: /security/
---

# `$ cat security.policy`

## Scope

This policy covers vulnerabilities in code, configurations, and research artifacts published under the `notfoundsoft` GitHub account. Reports about third-party software should go to the relevant vendor.

## Reporting a vulnerability

Send reports to <notfoundsoft@gmail.com>, encrypted with the PGP key on the [pgp page](/pgp/).

Include:

- Affected repository, commit, or release
- Reproduction steps or proof of concept
- Impact assessment
- Suggested mitigation, if known

## Coordinated disclosure

- Acknowledgement within 72 hours of receipt
- Initial assessment within 7 days
- Fix timeline depends on severity and complexity. Communicated case by case.
- Public disclosure coordinated with the reporter, typically after a fix is available or a 90-day window has elapsed

## Out of scope

- Issues in unmaintained or archived repositories
- Vulnerabilities requiring privileged local access already granted by the user
- Theoretical issues without demonstrated impact
- Findings produced solely by automated scanners with no validation

## Safe harbor

Good-faith research conducted under this policy will not be referred for legal action. Avoid privacy violations, data destruction, service disruption, and any access to systems or data beyond what is necessary to demonstrate the issue.
