# API Service Check Template

> Use this template once per service. Fill in the header, then work through both layers in order. Layer 1 (OSPS Baseline) always comes before Layer 2 (API behavior).

---

## Service Header

Fill this in before starting checks.

```
Service name: 
Repo URL: 
Spec / ADR location: 
External-facing? Yes / No
Touches financial data? Yes / No
Touches PII? Yes / No
Has existing tests? Yes / No / Partial
OSPS Baseline assessment exists? Yes / No — [link if yes]
Tester: 
Date: 
```

---

## Layer 1 — OSPS Baseline Checks

*Why this comes first: Compliance gaps are as important as behavioral bugs. A service that works correctly but has no branch protection or no vulnerability disclosure process is still a risk.*

OSPS Baseline is the Open Source Project Security Baseline from OpenSSF — 39 controls across 7 categories. Check each category for this service's repo.

### AC — Access Control

| Check | Pass / Fail / N/A | Notes |
|---|---|---|
| Branch protection enabled on default branch | | |
| Pull request reviews required before merge | | |
| Workflow permissions appropriately scoped (not `write-all`) | | |
| Auth and authorization controls documented | | |
| Auth controls enforced in code, not just documented | | |

**Control IDs:** AC-01 (branch protection), AC-02 (workflow permissions)

---

### BR — Build & Release

| Check | Pass / Fail / N/A | Notes |
|---|---|---|
| CI/CD pipeline inputs sanitized | | |
| Secret scanning active on this repo | | |
| No secrets committed in history | | |
| Release artifacts signed or attested (e.g., SLSA provenance) | | |
| HTTPS enforced on all release and download endpoints | | |

**Control IDs:** BR-01 (secret scanning), BR-02 (artifact signing), BR-03 (HTTPS)

---

### DO — Documentation

| Check | Pass / Fail / N/A | Notes |
|---|---|---|
| Vulnerability disclosure process documented | | |
| Dependency management policy documented | | |
| User or integration guide exists | | |

**Control IDs:** DO-01 (vulnerability disclosure docs), DO-02 (dependency policy)

---

### GV — Governance

| Check | Pass / Fail / N/A | Notes |
|---|---|---|
| Contribution guidelines present (`CONTRIBUTING.md` or equivalent) | | |
| Code review requirements documented | | |
| Code review requirements enforced (not just documented) | | |

**Control IDs:** GV-01 (contribution guidelines), GV-02 (code review policy)

---

### LE — Legal

| Check | Pass / Fail / N/A | Notes |
|---|---|---|
| License file present (`LICENSE` or `LICENSE.md`) | | |
| License is OSI-approved or appropriate for FINOS context | | |
| License consistent across all releases and packages | | |

**Control IDs:** LE-01 (license present), LE-02 (license consistency)

---

### QA — Quality Assurance

| Check | Pass / Fail / N/A | Notes |
|---|---|---|
| Dependency management automated (Dependabot, Renovate, etc.) or regularly reviewed | | |
| Code review required before merge (policy enforced, not just stated) | | |
| Binary files excluded from repo | | |

**Control IDs:** QA-01 (dependency management), QA-02 (code review enforcement), QA-03 (binary files)

---

### SA/VM — Security Assessment & Vulnerability Management

| Check | Pass / Fail / N/A | Notes |
|---|---|---|
| Threat model documented | | |
| Vulnerability disclosure policy present (`SECURITY.md` or equivalent) | | |
| Security contact listed (in `SECURITY.md` or repo settings) | | |
| Private vulnerability reporting enabled (GitHub: Security → Private reporting) | | |

**Control IDs:** SA-01 (threat model), VM-01 (disclosure policy), VM-02 (private reporting)

---

## Layer 2 — API / Integration Checks

*Why this comes second: Behavior checks against a service with no compliance baseline give you false confidence. Know the compliance posture before you verify behavior.*

### Auth & Access

| Check | Expected | Actual | Pass / Fail |
|---|---|---|---|
| Service requires auth | Documented in spec: yes / no | | |
| Invalid or expired token | 401 response | | |
| Missing required scope | 403 response (not silent success, not wrong data returned) | | |
| Tokens passed in headers | Not in URLs, not in query strings | | |
| Rate limit exceeded | Correct error response (typically 429) | | |

---

### Contract / Spec Compliance

| Check | Expected | Actual | Pass / Fail |
|---|---|---|---|
| Endpoint exists at documented path | Per spec | | |
| Request shape matches spec | Per spec | | |
| Response shape matches spec | Per spec | | |
| Status codes match spec | Per spec | | |
| Required fields enforced | 400 if missing | | |
| Optional fields handled correctly when absent | No crash, no wrong data | | |
| Behavior matches its ADR | Per ADR | | |

---

### Error Handling

| Check | Expected | Actual | Pass / Fail |
|---|---|---|---|
| Malformed input | 400, not 500 | | |
| Dependency unavailable | Graceful failure, not silent hang | | |
| Error messages don't leak internals | No stack traces, no infra names in response | | |
| Errors are logged | Findable in logging stack | | |

---

### FINOS / Compliance Signals

*Skip this section if FINOS is not in scope for this project.*

| Check | Expected | Actual | Pass / Fail |
|---|---|---|---|
| PII not in logs | No PII visible in log output | | |
| PII not in error responses | No PII in any error response body | | |
| Financial data not in logs or error responses | No financial data leaking | | |
| Sensitive operations have audit trail | Logged with user, timestamp, action | | |
| Compliance rules from ADRs enforced | Per ADR | | |
| Financial operations have limits/validations | Boundary conditions handled | | |

---

### Integration Points

| Check | Expected | Actual | Pass / Fail |
|---|---|---|---|
| Service calls its dependencies correctly | Per spec | | |
| Unexpected downstream response handled gracefully | Error surfaced, not swallowed | | |
| Webhooks/callbacks validated before acted on | Signature or source verified | | |
| Cross-service data matches on both sides of the integration | Same values on sender and receiver | | |

---

## Findings Log

Use this section to record anything that fails. Copy the format from `01-qa-agent-spec.md`.

```
Service: 
Risk: High / Medium / Low
OSPS Control: [ID if applicable]
Finding: 
Expected: 
Actual: 
Suggested action: fix / investigate / document as known gap
```

---

*Add more finding blocks as needed.*
