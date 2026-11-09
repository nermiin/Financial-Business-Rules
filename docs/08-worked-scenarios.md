# Worked Scenarios: Business Rules

Fictional applicants evaluated against the [decision table](07-to-be-process-design.md). `DTI = (monthly debt + proposed installment) / net monthly income`.

## S-01 Why REFER Cannot Stop Evaluation

| Input | Value |
|-------|-------|
| Age, residence | 34, NL |
| Net income | EUR 2,000 |
| Debt plus installment | EUR 1,200 |
| DTI | 0.6000 |
| Bureau score | 480 |

| Evaluation | BR-003 | BR-004 | BR-005 | Final |
|------------|--------|--------|--------|-------|
| Baseline FR-01, stop at first terminal | REFER | Not run | Not run | **REFER** |
| Amended, CR-01 | REFER | DECLINE `DTI_MAX` | DECLINE `BUREAU_SCORE` | **DECLINE** |

Under the baseline, a loan officer spends time on an application that policy already rejects twice over.

## S-02 Clean Approval

| Input | Value | Rule result |
|-------|-------|-------------|
| Age 28, DE | | Pass |
| Net income | EUR 3,200 | Pass |
| Debt EUR 420, installment EUR 500 | DTI 0.2875 | Pass |
| Bureau score | 710 | Pass |
| Delinquency | None | Pass |
| **Final** | | **APPROVE** via BR-007 |

## S-03 DTI Boundaries

Net income EUR 3,200 in each case.

| Debt plus installment | Raw DTI | Rounded | Outcome |
|-----------------------|---------|---------|---------|
| 1,120.00 | 0.35 | 0.3500 | Continue |
| 1,120.20 | 0.350063 | 0.3501 | REFER |
| 1,440.00 | 0.45 | 0.4500 | REFER |
| 1,440.20 | 0.450063 | 0.4501 | DECLINE |

The first row is the overlap found in F-02: the baseline allows both outcomes.

## S-04 Effective-Dated Version

BR-003 version 2 raises minimum income to EUR 1,600, effective 1 July 2026 in Europe/Berlin (CEST, UTC+2).

| `submittedAt` (UTC) | Berlin local | Version | Income 1,550 result |
|---------------------|--------------|---------|---------------------|
| 2026-06-30T21:59:59Z | 30 June, 23:59:59 | 1 | REFER |
| 2026-06-30T22:00:00Z | 1 July, 00:00:00 | 2 | DECLINE |

An application saved on 30 June and submitted on 1 July uses version 2, because `submittedAt` selects the version.

## S-05 Sandbox Impact Analysis

Replay of 10,000 anonymized applications for the same change.

| Outcome | Before | After | Change |
|---------|--------|-------|--------|
| APPROVE | 6,200 | 6,200 | 0 |
| REFER | 2,100 | 1,860 | (240) |
| DECLINE | 1,700 | 1,940 | +240 |

**Reading the delta:** 240 applicants with income from EUR 1,500 up to EUR 1,599 move from REFER to DECLINE. No approvals change. The manual queue falls by 11.4%, and the Risk team must decide whether the approvals those referrals used to produce are worth losing.

## S-06 Audit Record

What an auditor retrieves for one application (US-004). Personal data is masked.

```json
{
  "applicationId": "APP-20260702-0091",
  "submittedAt": "2026-07-02T09:14:03Z",
  "finalOutcome": "REFER",
  "reasonCodes": ["DTI_BORDERLINE"],
  "ruleChain": [
    { "ruleId": "BR-001", "version": 1, "result": "CONTINUE" },
    { "ruleId": "BR-002", "version": 1, "result": "CONTINUE" },
    { "ruleId": "BR-003", "version": 2, "result": "CONTINUE" },
    { "ruleId": "BR-004", "version": 2, "result": "REFER" },
    { "ruleId": "BR-005", "version": 1, "result": "CONTINUE" },
    { "ruleId": "BR-006", "version": 2, "result": "CONTINUE" }
  ],
  "inputSnapshot": {
    "ageBand": "25-34",
    "residence": "DE",
    "netIncomeBand": "3000-3499",
    "dti": 0.3812,
    "bureauScoreBand": "650-699"
  }
}
```

## Edge Case Register

| ID | Situation | Expected Behavior | Rule |
|----|-----------|-------------------|------|
| E-01 | 18th birthday on the submission date | Passes | BR-001 |
| E-02 | Income exactly EUR 2,500 | Continue | BR-003 |
| E-03 | Income exactly EUR 1,500 | REFER | BR-003 |
| E-04 | Payslip OCR confidence 0.91, no open banking | REFER `DATA_UNAVAILABLE` | BR-008 |
| E-05 | Bureau no-hit | REFER `DATA_UNAVAILABLE` | BR-008 |
| E-06 | Score exactly 520 or 600 | REFER, then continue | BR-005 |
| E-07 | Tradeline at 30 DPD exactly 12 months ago | DECLINE, window is inclusive | BR-006 |
| E-08 | Two versions claim the same effective date | Second promotion rejected | FR-03 |
