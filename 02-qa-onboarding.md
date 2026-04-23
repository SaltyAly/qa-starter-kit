# QA Onboarding Checklist

> Use this the first time you're invited into a project that already has specs, ADRs, and code. Follow the phases in order. Do not write a single test until Phase 2 is complete.

---

## Phase 1 — Read Before You Touch Anything

*Why: You'll write wrong tests if you don't understand the decisions already made.*

- [ ] Read all ADRs — understand the WHY behind decisions, not just what was built
- [ ] Read all service specs — what each service should do, and what's explicitly out of scope
- [ ] Map the auth model — how services authenticate with each other, and how users authenticate with services
- [ ] Map sensitive data — what data flows through the platform and what's sensitive (PII, financial, compliance-regulated)
- [ ] Map service dependencies — what calls what; draw it if it helps
- [ ] Check for existing OSPS Baseline assessment results — look for a `security-insights.yml` file or PVTR output in each repo

---

## Phase 2 — Assess Risk Before Writing a Single Check

*Why: Without a risk ranking, you'll spend time on the wrong things first.*

Answer these questions for each service:

- [ ] Is this service external-facing? (Higher risk — more attack surface)
- [ ] Does this service touch financial data or compliance rules? (Highest risk in FINOS context)
- [ ] Does this service have zero tests? (Coverage gap — flag immediately)
- [ ] Does this service have no Security Insights file or OSPS Baseline coverage?
- [ ] What is the blast radius if this service fails? (What breaks downstream?)

**Output of Phase 2:** A ranked list of services, ordered by risk. This is the order you test them in.

| Rank | Service | Risk drivers | Test first? |
|---|---|---|---|
| 1 | [service name] | [e.g., external-facing, financial data, no tests] | Yes |
| 2 | [service name] | [e.g., internal only, has tests, no financial data] | After #1 |
| ... | | | |

---

## Phase 3 — Set Up Your Environment

*Why: You can't test anything reliably without knowing where to look when things go wrong.*

- [ ] Identify dev and staging environments — which one do you use for testing?
- [ ] Find test credentials and sandbox configs — where do these live?
- [ ] Find the logging stack — where do errors surface? (Loki, CloudWatch, Datadog, etc.)
- [ ] Know what can be tested in isolation vs. what requires full integration running

---

## Phase 4 — First Checks

*Why: Starting with everything at once leads to unfocused, unfinished testing.*

- [ ] Start with the top 2–3 highest-risk services from your Phase 2 ranking only
- [ ] Write every check against the spec or ADR — not your assumptions about what should happen
- [ ] Document expected vs. actual behavior explicitly, every time — even if the test passes
- [ ] Use the finding format from `01-qa-agent-spec.md` for anything that fails

---

## What Not to Do

- **Do not start with the services you're most familiar with.** Start with the highest risk.
- **Do not assume behavior.** Every check traces back to a spec or ADR.
- **Do not test everything at once.** Two services tested well beats ten tested badly.
- **Do not skip Phase 2.** A risk assessment is not optional — it is the whole point.
