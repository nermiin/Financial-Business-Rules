# User Stories & Acceptance Criteria

## Epic: Loan Eligibility Rules Engine — Phase 1

---

### US-001 — Evaluate eligibility on application submit

**As a** loan origination system  
**I want** to call the rules engine with applicant data  
**So that** the customer receives an immediate eligibility outcome

**Priority:** Must | **Story points:** 5

#### Acceptance Criteria

```gherkin
Feature: Synchronous eligibility evaluation

  Scenario: Applicant meets all hard rules
    Given applicant age is 28
    And residency is "DE"
    And verified net income is 3200 EUR
    And DTI is 0.28
    And bureau score is 710
    And no delinquency in last 12 months
    When the LOS submits an eligibility request
    Then the outcome is "APPROVE"
    And reasonCode is null
    And ruleVersion is returned

  Scenario: Applicant fails minimum age rule
    Given applicant age is 17
    When the LOS submits an eligibility request
    Then the outcome is "DECLINE"
    And reasonCode is "AGE_MIN"
    And ruleId is "BR-001"
```

---

### US-002 — Versioned rule deployment

**As a** risk analyst  
**I want** to schedule rule changes with effective dates  
**So that** policy updates align with regulatory announcements

**Priority:** Must | **Story points:** 8

#### Acceptance Criteria

```gherkin
  Scenario: Application uses rule version at submission time
    Given rule BR-003 version 2 raises minimum income to 1600 EUR effective 2026-07-01
    And rule BR-003 version 1 requires 1500 EUR
    When an application is submitted on 2026-06-30
    Then evaluation uses version 1
    When an application is submitted on 2026-07-01
    Then evaluation uses version 2
```

---

### US-003 — Compliance approval gate

**As a** compliance officer  
**I want** to approve rule versions before production  
**So that** regulatory requirements are met before customers are impacted

**Priority:** Must | **Story points:** 5

#### Acceptance Criteria

- Draft rule versions cannot be promoted without Compliance approval record.
- Approval captures user ID, timestamp, and optional comment.
- Rejected versions remain in draft; promoter receives notification.

---

### US-004 — Evaluation audit log

**As an** internal auditor  
**I want** to retrieve the rule evaluation for any application ID  
**So that** I can verify the decline was policy-compliant

**Priority:** Must | **Story points:** 3

#### Acceptance Criteria

- Audit API returns masked inputs, outcome, rule chain, and timestamps.
- Logs are immutable (append-only store).
- Retention ≥ 7 years per records management policy.

---

### US-005 — Sandbox testing for Risk

**As a** risk analyst  
**I want** to test rule changes against historical applications in sandbox  
**So that** I can estimate portfolio impact before go-live

**Priority:** Should | **Story points:** 8

#### Acceptance Criteria

- Sandbox supports batch replay of up to 10,000 anonymized applications.
- Output includes delta report: approvals → declines and vice versa.
- Sandbox data never written to production stores.

---

### US-006 — Borderline DTI referral

**As a** loan officer  
**I want** borderline DTI applications routed to my queue  
**So that** I can apply judgment with supporting documentation

**Priority:** Must | **Story points:** 3

#### Acceptance Criteria

```gherkin
  Scenario: DTI in referral band
    Given DTI is 0.38
    When eligibility is evaluated
    Then outcome is "REFER"
    And reasonCode is "DTI_BORDERLINE"
    And application appears in manual review queue within 30 seconds
```

### US-007 — Read-only rule catalog

**As a** CRM or LOS user  
**I want** to view the active rules and reason codes  
**So that** I can explain an outcome without asking Risk

**Priority:** Should | **Story points:** 3

#### Acceptance Criteria

- Returns rule ID, version, effective date, and description.
- Draft and scheduled versions are not visible to read-only consumers.

---

## Definition of Done

- [ ] Code reviewed and merged
- [ ] Unit and integration tests cover all Gherkin scenarios
- [ ] BA sign-off on UAT evidence
- [ ] Compliance sign-off for rule catalog changes
- [ ] Runbook updated
