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
