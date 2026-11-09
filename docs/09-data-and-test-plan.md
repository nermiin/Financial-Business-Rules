# Data Requirements and Test Plan: Business Rules

## 1. Evaluation Request

| Field | Type | Mandatory | Rule |
|-------|------|-----------|------|
| `applicationId` | String | Yes | LOS identifier |
| `submittedAt` | Timestamp, UTC | Yes | Selects rule version |
| `dateOfBirth` | Date | Yes | Age derived, not sent |
| `residenceCountry` | ISO 3166 | Yes | BR-002 |
| `verifiedNetIncome` | Decimal | Yes | EUR, monthly |
| `incomeSource`, `incomeConfidence` | Enum, number | Yes | OCR requires at least 0.92 |
| `monthlyDebtObligations` | Decimal | Yes | Bureau plus declared |
| `proposedInstallment` | Decimal | Yes | From LOS |
| `bureauScore` | Integer | Conditional | Absent on a no-hit |
| `maxDpd12m` | Integer | Conditional | Worst tradeline in 12 months |

## 2. Evaluation Response (FR-02)

| Field | Rule |
|-------|------|
| `outcome` | APPROVE, REFER, DECLINE |
| `reasonCodes` | Ordered list, empty on APPROVE |
| `ruleChain` | Each `ruleId`, `ruleVersion`, result |
| `evaluatedAt` | Server time |
| `evaluationId` | Links to audit log |

FR-02 originally returned a single `reasonCode`. Because all rules now run (CR-01), several reasons can apply, so the response carries a list.

## 3. Reason Code Governance

| Code | Rule | Customer wording | Owner |
|------|------|------------------|-------|
| `AGE_MIN` | BR-001 | You must be 18 or over to apply | Risk |
| `RESIDENCY` | BR-002 | This product is not available in your country | Risk |
| `INCOME_MIN` | BR-003 | Your income does not meet our minimum for this product | Risk |
| `DTI_MAX` | BR-004 | The repayments would be too high against your income and commitments | Risk |
| `BUREAU_SCORE` | BR-005 | Information from a credit reference agency affected this decision | Compliance |
| `DELINQUENCY` | BR-006 | Information from a credit reference agency affected this decision | Compliance |

Referral codes are internal only. The customer is told that the application is being reviewed.

## 4. Change Governance

| Change type | Example | Approval | Replay required |
|-------------|---------|----------|-----------------|
| Parameter, tighter | Income minimum rises | Compliance | Yes |
| Parameter, looser | DTI limit rises | Compliance and CRO | Yes, plus 30-day monitoring |
| New rule | New exclusion | Compliance and CRO | Yes |
| Wording | Customer text change | Compliance | No |
| Emergency | Regulatory stop | Compliance on call, reviewed within 2 days | After the event |

## 5. Boundary Test Matrix

Every band edge is tested at the edge and one step either side.

| TC | Rule | Value | Expected |
|----|------|-------|----------|
| TC-AGE-01 | BR-001 | One day under 18 | DECLINE |
| TC-AGE-02 | BR-001 | 18 on submission date | Continue |
| TC-INC-01 | BR-003 | 1,499.99 | DECLINE |
| TC-INC-02 | BR-003 | 1,500.00 | REFER |
| TC-INC-03 | BR-003 | 2,499.99 | REFER |
| TC-INC-04 | BR-003 | 2,500.00 | Continue |
| TC-DTI-001 | BR-004 | 0.4600 | DECLINE |
| TC-DTI-002 | BR-004 | 0.4500 | REFER |
| TC-DTI-003 | BR-004 | 0.3501 | REFER |
| TC-DTI-004 | BR-004 | 0.3500 | Continue |
| TC-BUR-001 | BR-005 | 519 | DECLINE |
| TC-BUR-002 | BR-005 | 520 | REFER |
| TC-BUR-003 | BR-005 | 600 | Continue |
| TC-DEL-001 | BR-006 | 30 DPD within 12 months | DECLINE |
| TC-DEL-002 | BR-006 | 29 DPD within 12 months | Continue |
| TC-DAT-001 | BR-008 | Bureau no-hit | REFER |

## 6. Combination Tests

| TC | Scenario | Expected |
|----|----------|----------|
| TC-CMB-001 | REFER on income and DECLINE on DTI | DECLINE |
| TC-CMB-002 | REFER on income and REFER on DTI | REFER, two reasons |
| TC-CMB-003 | DECLINE on two rules | DECLINE, two reasons |
| TC-CMB-004 | All pass | APPROVE, no reasons |
| TC-CMB-005 | Missing score and DECLINE on age | DECLINE |

## 7. Migration and Shadow Run

| Step | Check | Pass mark |
|------|-------|-----------|
| 1 | Replay 12 months of decisions through the engine | Differences explained by CR-01 to CR-04 |
| 2 | Shadow production for 2 weeks | 99.5% agreement after known differences |
| 3 | Sample 50 applications with Internal Audit | 100% rule attribution |
| 4 | Load test at 500 TPS | p95 at or below 500ms |
| 5 | Switch channels one at a time | Web, then app, then branch |
