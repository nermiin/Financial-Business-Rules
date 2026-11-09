# Requirements Traceability Matrix

Links business rules and objectives to functional requirements and user stories.

| Business Objective | Business Rule | Functional Req | User Story | Test Case Ref |
|--------------------|---------------|----------------|------------|---------------|
| BO-02 Auditability | BR-001 – BR-006 | FR-02, FR-04 | US-001, US-004 | TC-ELIG-001 – TC-ELIG-012 |
| BO-01 Cycle time | All | FR-05, FR-06 | US-002, US-003 | TC-VER-001 – TC-VER-005 |
| BO-03 Business ownership | BR-003 (example) | FR-05, FR-06 | US-002, US-005 | TC-SBX-001 – TC-SBX-010 |
| BO-02 Auditability | BR-004 | FR-01, FR-02 | US-001, US-006 | TC-DTI-001 – TC-DTI-004 |
| BO-01 Cycle time | — | FR-07 | US-001 | TC-PERF-001 |
| — | BR-005 | FR-01 | US-001 | TC-BUR-001 – TC-BUR-003 |
| — | BR-006 | FR-01 | US-001 | TC-DEL-001 |

## Sample Test Case — TC-DTI-001

| Field | Value |
|-------|-------|
| **Covers** | BR-004, US-006 |
| **Preconditions** | All rules except DTI pass |
| **Input** | DTI = 0.46 |
| **Expected** | DECLINE, reasonCode = DTI_MAX |

## Coverage Summary

| Artifact type | Count | Traced % |
|---------------|-------|----------|
| Business rules (BR) | 6 | 100% |
| Functional requirements (FR) | 8 | 100% |
| User stories (US) | 6 | 100% |

## Coverage Review

Catalog review found FR-03 and FR-08 untraced. They are closed below, together with the approved change requests.

| Objective | Rule or CR | FR | User Story | Test |
|-----------|------------|-----|------------|------|
| BO-02 Auditability | Effective dating (CR-06) | FR-03 | US-002 | TC-VER-001 |
| BO-03 Business ownership | none | FR-08 | US-007 | Catalog API test |
| BO-02 Correct outcomes | CR-01 | FR-01 | US-001 | TC-CMB-001 to TC-CMB-005 |
| BO-02 Unambiguous DTI | BR-004 amended (CR-02) | FR-01 | US-006 | TC-DTI-001 to TC-DTI-004 |
| BO-03 Safe rule changes | BR-006 amended, BR-007 (CR-03) | FR-01 | US-001 | TC-DEL-001, TC-DEL-002 |
| BO-02 Missing data | BR-008 (CR-04) | FR-01 | US-006 | TC-DAT-001 |
| BO-02 Age reference | BR-001 amended (CR-05) | FR-01 | US-001 | TC-AGE-01, TC-AGE-02 |
