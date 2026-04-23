# QA Starter Kit

A practical QA toolkit for developers working on multi-service open source platforms — especially projects in or adjacent to the FINOS ecosystem and OpenSSF OSPS Baseline.

Built for developers who are being invited into an existing project after the fact, have specs and ADRs but no QA process, and need methodology — not just more tooling.

---

## What's in this kit

| File | What it does |
|---|---|
| [`01-qa-agent-spec.md`](01-qa-agent-spec.md) | Replaces an AI-generated system prompt with a methodology-grounded QA agent spec. Fill in the project context block and use it. |
| [`02-qa-onboarding.md`](02-qa-onboarding.md) | A four-phase checklist for the first time you're invited into an existing project. Read this before writing a single test. |
| [`03-api-service-checks.md`](03-api-service-checks.md) | A per-service check template. Two layers: OSPS Baseline compliance first, then API behavior. Copy once per service. |

---

## How to use this kit

**If you have a QA agent and want to make it better:** Start with `01-qa-agent-spec.md`. Fill in the project context block at the top. Replace your current system prompt with this file.

**If you're starting QA on an existing project for the first time:** Start with `02-qa-onboarding.md`. Follow the four phases in order before writing any checks.

**When you're ready to check a specific service:** Use `03-api-service-checks.md`. Fill in the service header, work through Layer 1 (OSPS Baseline), then Layer 2 (API behavior). Log findings using the format in `01-qa-agent-spec.md`.

---

## The methodology behind this kit

This kit is grounded in the [OpenSSF OSPS Baseline](https://github.com/ossf/security-baseline) — a set of 39 security and quality controls across 7 categories that are already used in FINOS ecosystem projects.

**Why compliance checks come first:** A service that works correctly but has no branch protection, no vulnerability disclosure policy, and no secret scanning is still a risk. OSPS Baseline checks catch the things behavioral testing misses.

**Why risk ranking comes before test writing:** In a multi-service platform, you can't test everything. The onboarding checklist helps you figure out which services matter most before you spend time on the wrong ones.

**Why every finding has a format:** Unlabeled QA output creates noise. Every finding in this kit includes a risk tier, an OSPS control ID (where applicable), and a clear expected vs. actual statement — so findings are actionable and traceable.

---

## Compliance context

The OSPS Baseline categories used in this kit:

| Category | What it covers |
|---|---|
| AC — Access Control | Branch protection, workflow permissions, auth controls |
| BR — Build & Release | CI/CD security, secret scanning, artifact signing |
| DO — Documentation | Vulnerability disclosure, dependency policy, integration guides |
| GV — Governance | Contribution guidelines, code review requirements |
| LE — Legal | License presence and consistency |
| QA — Quality Assurance | Dependency management, code review enforcement |
| SA/VM — Security Assessment & Vulnerability Management | Threat models, disclosure policies, private reporting |

---

## Who built this

Built by a QA engineer working in identity and account services. Shared because the question "do you have a starter kit for QA first-time setup?" deserves a real answer.
