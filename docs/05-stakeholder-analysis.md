# Stakeholder Analysis

## 1. Stakeholder Register

| Stakeholder | Role | Interest | Influence | Engagement |
|-------------|------|----------|-----------|------------|
| Head of Retail Lending | Sponsor | Faster time-to-market for products | High | Manage closely |
| Chief Risk Officer | Policy owner | Accurate, controllable rules | High | Manage closely |
| Compliance Director | Regulator interface | Audit trail, approval controls | High | Manage closely |
| Engineering Lead | Delivery | Maintainable integration | Medium | Keep informed |
| Loan Operations Manager | Operations | Manual queue workload | Medium | Keep informed |
| Internal Audit | Assurance | Reproducibility of decisions | Medium | Consult |
| Customer (indirect) | End user | Fair, consistent outcomes | Low | Monitor |

## 2. RACI Matrix

| Activity | Risk | Compliance | BA/PO | Engineering | Loan Ops | Audit |
|----------|------|------------|-------|-------------|----------|-------|
| Define business rules | **R/A** | C | R | I | C | I |
| Author BRD | C | C | **R/A** | C | I | I |
| Approve rule versions for prod | C | **R/A** | I | I | I | I |
| Build rules engine integration | I | I | C | **R/A** | I | I |
| UAT execution | C | C | **R** | C | **A** | I |
| Audit sampling | I | C | I | I | C | **R/A** |

*R = Responsible, A = Accountable, C = Consulted, I = Informed*

## 3. Communication Plan

| Audience | Channel | Frequency | Content |
|----------|---------|-----------|---------|
| Steering committee | Status deck | Bi-weekly | Milestones, risks, decisions |
| Risk & Compliance | Working session | Weekly | Rule catalog changes, UAT |
| Engineering | Refinement + Slack | Daily during sprint | Story clarity, API contracts |
| Loan Operations | Demo | Per release | Queue behavior, reason codes |
| Audit | Pre-go-live briefing | Once | Logging and retrieval demo |

## 4. Key Decisions Log

| Date | Decision | Rationale | Owner |
|------|----------|-----------|-------|
| 2026-03-10 | Phase 1 = hard declines only | Reduce scope; pricing rules need separate model sign-off | Sponsor |
| 2026-03-18 | DMN for rule notation | Business-readable; tooling support | BA |
| 2026-04-02 | Synchronous API only | LOS requires immediate outcome; batch deferred | Engineering |
