# Business Requirements Document — Loan Eligibility Rules Engine

| Field | Value |
|-------|-------|
| **Project** | NovaPay Lending Rules Engine (Phase 1) |
| **Author** | Business Analysis — Portfolio Sample |
| **Version** | 1.0 |
| **Status** | Approved for development |
| **Stakeholders** | Risk, Compliance, Retail Lending, Engineering |

---

## 1. Background

NovaPay Bank requires a centralized rules engine to evaluate personal loan eligibility at application submission. Phase 1 covers **hard decline rules** only; soft flags and pricing tiers are Phase 2.

## 2. Business Objectives

| ID | Objective | Metric |
|----|-----------|--------|
| BO-01 | Reduce policy-to-production cycle time | ≤ 5 business days |
| BO-02 | Improve auditability of decline decisions | 100% rule attribution |
| BO-03 | Enable business-owned rule maintenance | Risk authors 80% of rules without code |

## 3. Scope

### In Scope
- Age, residency, income, employment, DTI, bureau score, existing delinquency rules.
- Rule versioning, effective dating, and evaluation audit log.
- Integration with loan origination system (LOS) via synchronous API.

### Out of Scope
- Pricing / APR calculation rules.
- Manual override workflow (separate initiative).
- Batch re-evaluation of existing book.

## 4. Business Rules Catalog (Phase 1)

### BR-001 — Minimum Age

| Condition | Outcome |
|-----------|---------|
| Applicant age < 18 | **DECLINE** — reason `AGE_MIN` |
| Applicant age ≥ 18 | Continue evaluation |

### BR-002 — Residency

| Condition | Outcome |
|-----------|---------|
| Country of residence ∉ {DE, AT, NL} | **DECLINE** — reason `RESIDENCY` |
| Otherwise | Continue |

### BR-003 — Minimum Net Monthly Income

| Condition | Outcome |
|-----------|---------|
| Verified net income < €1,500 | **DECLINE** — reason `INCOME_MIN` |
| €1,500 ≤ income < €2,500 | **REFER** — manual review queue |
| income ≥ €2,500 | Continue |

*Verification source: open banking feed OR payslip OCR with confidence ≥ 0.92.*

### BR-004 — Debt-to-Income Ratio

```
DTI = (monthly_debt_obligations + proposed_installment) / net_monthly_income
```

| DTI | Outcome |
|-----|---------|
| > 0.45 | **DECLINE** — reason `DTI_MAX` |
| 0.35 – 0.45 | **REFER** — reason `DTI_BORDERLINE` |
| ≤ 0.35 | Continue |

### BR-005 — Credit Bureau Score

| Score (0–1000) | Outcome |
|----------------|---------|
| < 520 | **DECLINE** — reason `BUREAU_SCORE` |
| 520 – 599 | **REFER** |
| ≥ 600 | Continue |

### BR-006 — Active Delinquency

| Condition | Outcome |
|-----------|---------|
| Any tradeline ≥ 30 DPD in last 12 months | **DECLINE** — reason `DELINQUENCY` |
| Otherwise | **APPROVE** (eligibility path complete) |

## 5. Functional Requirements

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-01 | System SHALL evaluate rules BR-001 through BR-006 in defined order; first terminal outcome stops evaluation | Must |
| FR-02 | System SHALL return `outcome`, `reasonCode`, `ruleId`, `ruleVersion`, `evaluatedAt` | Must |
| FR-03 | System SHALL support effective-dated rule versions; applications use version active at `submittedAt` | Must |
| FR-04 | System SHALL log full input snapshot (PII-masked) with evaluation result | Must |
| FR-05 | Risk users SHALL create draft rules in sandbox without production impact | Must |
| FR-06 | Compliance SHALL approve rule version before promotion to production | Must |
| FR-07 | System SHALL complete evaluation within 500ms p95 | Should |
| FR-08 | System SHALL expose read-only rule catalog API for LOS and CRM | Should |

## 6. Non-Functional Requirements

| ID | Category | Requirement |
|----|----------|-------------|
| NFR-01 | Availability | 99.9% monthly uptime |
| NFR-02 | Security | RBAC — only Risk/Compliance edit rules |
| NFR-03 | Audit | Immutable audit log, 7-year retention |
| NFR-04 | Compliance | GDPR — data minimization on logs |

## 7. Assumptions & Dependencies

- Credit bureau API available with < 200ms latency.
- Income verification service operational for DE/AT/NL.
- LOS can persist `ruleVersion` on application entity.

## 8. Acceptance Criteria (Release)

1. 200 synthetic applications pass/fail per BRD decision table in UAT.
2. Audit sample of 50 production applications shows rule attribution.
3. Risk user promotes rule change in sandbox without engineering deploy.

## 9. Risks

| Risk | Mitigation |
|------|------------|
| Rule order disputes between Risk and Legal | Sign-off workshop + documented evaluation sequence |
| Performance regression under load | Load test 500 TPS before go-live |
| Legacy declines not reproducible | Parallel run shadow mode for 2 weeks |
