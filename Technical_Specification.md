# Technical Specification Document

**Team:** Hinna Parwez, Aradana Nair, Maria Malik, Priyanshi Kulshrestha, Mbathio Lo.

**Advisors:** Dr. David Reeping and Dr. Cedrick Kwuimy.

**Project:** ReFrame - CEAS Co-op Reflection System. Assignment 9.

> Review draft - not yet submission-ready. Team approval and actual advisor-review evidence are pending. Original D0/D1/D2 files, source stories and Week 3 essay were not supplied for direct verification. Statements attributed to those sources are inherited from the uploaded draft or current planning document. Proposed refinements are not approved design decisions. Version 0.1; traces to Requirements_Document.md version 0.3 (2026-10-30). Use the glossary in that document.

## 1. Version and change log

| Version | Date | Change | Author |
| --- | --- | --- | --- |
| 0.1 | 2026-10-30 | Created specification structure, boundary contracts, stack evidence, constraint consequences and 30-row traceability matrix. Contracts and unresolved mappings require reconciliation with D1/D2. | All team members; AI assistance was used |

No advisor approval is implied. Proposed refinements since the cited D2 design are tracked in the requirements 0.3 change log and open questions.

## 2. Architecture summary

Design diagrams are maintained in the project repository (github.com/hinnaparwezuc/Senior-Design) and are referenced, not duplicated, in this document.

| Reference | Contents | Repository path |
| --- | --- | --- |
| D0 High-Level Design | Components, interfaces I1 to I13, data flows, architecture decisions | Design_Diagrams/D0_High_Level_Design.pdf |
| D1 Detailed Design | C7 data model, C2 access algorithm, API contract | Design_Diagrams/D1_Detailed_Design.pdf |
| D2 Detailed Design | C1 and C3 to C6, edge cases, traceability, design rationale | Design_Diagrams/D2_Detailed_Design.pdf |

The current Assignment 5-6 plan names seven components. C1 Web Front End provides student notice/form/review/submission, advisor roster and reflection views, and supervisor evaluation screens. E1 University Sign-In identifies the caller; token-checking glue authenticates protected requests. C2 Access Control Service makes reflection-read decisions and exposes advisor roster and student reflection-list operations. C3 Reflection Service obtains eligibility and placement data from E2 University Records, loads rotation-specific prompts from C4 Prompt Configuration Store, checks responses and saves through C7 Reflection Database. C5 Summary Generator extracts accomplishment, challenge and goal text for authorized advisor reads. C6 Supervisor Evaluation Service checks supervisor assignments through E2 and inserts evaluations through restricted C7 permissions; it has no reflection-read permission. The student flow passes from C1 through authenticated C3 operations to C4/E2, then saves the reflection and responses atomically in C7. The advisor flow passes through C2 authorization before C7 returns content; C5 failure must not suppress the full authorized reflection. The supervisor flow passes through C6 and E2 and stores its separate evaluation. Configuration and secrets support all services; content-free logging records allowed operational fields. The deployment model is a browser client and managed server/database hosting deployed through Git, with daily backups and health checks, as described in plan T15. C2-C6 are logical services; the evidence does not establish separate deployable microservices. UC hosting approval, provider, framework versions and service topology are unresolved. Use synthetic data until approval. The plan identifies D0 Flows A/B/C and D1/D2 interfaces, but the actual diagram files must be checked before these references are accepted as verified traceability.

## 3. Interfaces and data formats

Contracts follow the D0 interface table (I1 to I13), as revised in D2 (I3, I4, I5), and the D1 API contract.

| Boundary | Inputs / outputs and format | Protocol | Failure behaviour |
| --- | --- | --- | --- |
| C1 / E1 sign-in glue | Sign-in request and callback; validated identity {subject, roles}; provider token format pending. | HTTPS; E1 penID Connect authorization code flow, token in a secure HTTP-only cookie. | Invalid state/token: no session or protected content; supervisor mechanism unresolved. |
| C1 / C2 (read API) | Identity, reflection/student ID, include=summary or pagination; JSON roster, reflection and optional summary. | HTTPS JSON | 401 invalid token; uniform 404 for forbidden/missing reflection; 503 stale assignment data. |
| C1 / C3 (I3) | Placement/rotation, notice version and answers; JSON form/check/save receipt. | HTTPS JSON | 422 invalid fields; 403 eligibility; 409 duplicate; 503 dependency outage; no partial records. |
| C1 / C6 (I5 family; verify) | Placement/rotation and evaluation fields; JSON receipt. | HTTPS JSON | 401/403 invalid caller; 422 invalid fields; 409 duplicate; 503 E2 failure. |
| C3 / C4 (I6) | Supported rotation; versioned prompt set with categories and required flags. | Internal call; JSON-equivalent object | Unsupported/incomplete set blocks form and submit; no guessed prompts. |
| C3 / E2 (I7) | Caller and placement IDs; enrollment, ownership and recorded rotation. | External protocol unresolved; JSON assumed | 2-second proposed timeout; refuse start/save on unavailable or invalid evidence. |
| C2 / E2 (I8) | Student/advisor IDs; dated assignments and refresh timestamp. | External protocol unresolved | 15-minute maximum copy age proposed; failed refresh of expired copy denies advisor reads. |
| C3 / C7 (I9) | Validated reflection plus responses and prompt version; stored ID. | Database transaction / driver pending | Rollback all rows on error; uniqueness conflict returns existing owned ID only. |
| C2 / C7 (read boundary) | Authorized reflection/student IDs; stored content and evaluations for assigned advisor. | Database query / driver pending | No query result exposed before authorization; redact storage errors. |
| C2 / C5 (I11) | Authorized reflection text; three bounded summary strings. | Internal function | Return unavailable marker on failure; preserve full authorized reflection. |
| C6 / E2 (I12/I13; verify split) | Supervisor, placement and rotation IDs; assignment eligibility. | External protocol unresolved | Reject unassigned; fail closed on timeout. |
| C6 / C7 (I12/I13; verify split) | Validated evaluation; receipt ID. | Restricted database connection | Atomic insert; reject duplicates; no reflection SELECT permission. |
| Admin loader / C4 | Approved prompt-set configuration; new immutable version. | Admin script; source-file format pending | Reject missing categories or invalid rotation; retain prior versions. |
| Services / configuration, logs, hosting | Secret references and settings; allowlisted decision metadata; backup/health status. | Environment/secret store; logging and host mechanisms pending | Missing secrets prevent readiness; no response text in logs; failed backup alerts operator (mechanism pending). |

### 3.1 API endpoint contract

Only GET /v1/me and GET /v1/advisors/me/students are explicitly named in the current plan. Other paths/methods below are provisional names for documented operations. All routes require valid authentication, except the identity-provider sign-in/callback. Authorization is rechecked server-side on every content operation.

| Method / path | Input | Success output | Errors / requirement |
| --- | --- | --- | --- |
| GET /v1/me | E1 token | 200 identity subject/roles | 401; NFR-07 |
| POST /v1/reflections/start | placement_id; optional supplied rotation | 200 eligibility, notice and supported rotation | 403, 422, 503; REQ-01/04/05 |
| POST /v1/notices/acknowledgments | notice_version | 200 accepted version | 409 stale notice, 422; REQ-05 |
| GET /v1/reflection-form | placement_id, rotation | 200 prompt_set_version and prompts | 403, 422, 503; REQ-01/04/12 |
| POST /v1/reflections/checks | ReflectionInput | 200 valid or field errors; no persistence | 422, 503; REQ-02/05 |
| POST /v1/reflections | ReflectionInput | 201 {reflection_id} | 409 {code, reflection_id} for authorized duplicate; 422/503; REQ-03 |
| GET /v1/reflections/{id} | optional include=summary | 200 reflection; summary for assigned advisor | 404 uniform denied/missing; 503; REQ-06/08/09 |
| GET /v1/students/{id}/reflections | authorized student ID; cursor/limit | 200 items and next_cursor | 404 denied/missing; REQ-08/09 |
| GET /v1/advisors/me/students | cursor; limit default 20, max 50 | 200 current assigned students and next_cursor | 422 invalid limit; 503 stale copy; REQ-11 |
| POST /v1/evaluations | EvaluationInput | 201 {evaluation_id} | 403/409/422/503; REQ-07/10 |
| GET /v1/reflections/{id}/evaluation | reflection ID; assigned advisor only | 200 evaluation or null | 404 uniform denied/missing; REQ-07. Proposed route; confirm D1. |

Proposed common errors use JSON {code: string, message: string, field_errors?: array}. No submitted text, token, stack trace or database detail appears in errors. Authenticated forbidden and nonexistent reflection requests both return HTTP 404 with identical {code: "ACCESS_DENIED", message: "Resource unavailable"}. Unauthenticated requests return 401. Validation field errors contain only prompt IDs and codes. A duplicate reflection ID is returned only after ownership/eligibility checks. Successful responses use application/json; timestamps are UTC ISO 8601. Submission revalidates eligibility, notice version, prompt version and required fields; a successful earlier check is not sufficient. Retry after a lost success response returns the original owned reflection ID rather than creating another row. Summary and evaluation access inherit the parent reflection authorization gate.

### 3.2 Payload and persistence formats

These logical schemas make the draft reviewable. Actual D1 names, types and eight-table decomposition must be supplied and reconciled; this document does not claim to reproduce unseen SQL. IDs below are nonempty opaque strings; rotation is a positive integer; unknown fields are rejected in the proposed request contract.

| Schema / message | Required fields and validation |
| --- | --- |
| ReflectionInput | placement_id: string; rotation: integer >=1; prompt_set_version: string; notice_version: string; answers: array of {prompt_id: string, text: string}. Exactly one answer per applicable prompt; no foreign/duplicate prompt IDs. Required text must contain a non-whitespace character. Caller identity comes from token, never a trusted student_id field. |
| PromptSet | version: string; rotation: integer >=1; prompts: array of {prompt_id: string, category: accomplishment/challenge/goal, required: boolean, text: string}. At least one prompt per category; exact approved wording pending. |
| EvaluationInput | placement_id: string; rotation: integer >=1; accomplishments: string and improvement_areas: string, each 1-5,000 characters and non-whitespace. Supervisor identity comes from validated authentication. |
| ReflectionOutput | reflection_id, student_id, placement_id, rotation, rotation_source (record/student), prompt_set_version, submitted_at, answers. Advisor-only optional summary has accomplishment, challenge and goal strings, each <=300 characters. |
| Assignment payload | student_id, advisor_id, start_date, nullable end_date, fetched_at. E2 supervisor association and course/enrollment field names remain unresolved. |
| Access-decision event | event time, request ID, actor ID, resource ID and allow/deny code only; never answers, evaluations, tokens or unrestricted exception objects. |
| Pagination | items: array; next_cursor: opaque string or null; limit integer 1-50, default 20. Stable order/key must be confirmed in D1. |
| Logical stored record | Keys / integrity / behavior |
| Reflection and responses | Unique (student_id, placement_id, rotation); responses reference reflection and original prompt IDs/version; atomic parent and response commit. Exact D1 key must be verified against plan’s “unique pair” wording. |
| Prompt sets and prompts | Immutable version reference per reflection; configured positive rotation and required categories. Updating prompts creates a new version. |
| Supervisor evaluation | Unique placement/rotation under verified placement ownership; supervisor identity and submit time stored; C6 restricted from reading reflection content. |
| Advisor assignment copy | Student/advisor/date range plus freshness timestamp; supports multiple current advisors; proposed <=15-minute freshness gate. |
| Notice acknowledgment | Student, notice version and timestamp; current version required before starting and rechecked at submit. |
| Other D1 tables | Plan T6 specifies eight tables but does not enumerate their complete schemas. Obtain canonical D1 DDL, foreign keys, indexes, nullability and migration revisions before approval. |

C1 retains unsaved answers in sessionStorage as described in T12. Proposed isolation ties the draft to student and placement/rotation, restores only to the same authenticated user, and clears on submit, explicit sign-out or identity change. No server draft table is introduced.

## 4. Technology stack

Do not substitute a new framework for the team’s approved design. Choices supported by available evidence are listed below; unknown choices remain explicit blockers.

| Layer | Choice / evidence status | Justification tied to ID |
| --- | --- | --- |
| Client | Browser-based Web Front End C1; framework/version not provided. sessionStorage supported by plan T12. | NFR-02, NFR-06; REQ-14 |
| Service/API | Logical C2/C3/C5/C6 services; language, framework and server versions pending D1 verification. Proposed HTTPS JSON contracts. | REQ-01/03/07/08; NFR-07 |
| Authentication | E1 University Sign-In; provider protocol/token configuration unresolved. Supervisor route pending CEAS. | NFR-07; CON-06 |
| Persistence | C7 transactional database; engine/version unresolved. Alembic migrations named in T6. | REQ-03/12; NFR-03 |
| Prompt configuration | C4 versioned tables plus administrator load script (T7). | REQ-01/12 |
| Summarization | C5 deterministic extractive function, 300-character cut (T5). | REQ-06; CON-01/02/04/05 |
| Hosting/operations | Managed hosting, Git deployment, daily backups and health checks (T15); provider/location pending UC approval. | NFR-05; CON-06/08 |
| Configuration/logging | Environment variables, secret store (T14), allowlisted log_event (T18); vendors pending. | REQ-13; NFR-04/07 |
| Verification | Seeded integration/end-to-end tests and volunteer usability sessions (T9/T10/T19/T20); test tools pending. | NFR-01/02/03/06; REQ-09/10 |

## 5. Known constraints and consequences

Each constraint retains its requirements-document ID.

| ID | What it rules out or forces |
| --- | --- |
| CON-01 | Use consistent prompt validation and deterministic summaries; no automated grading. Inspect handling of equivalent answers. |
| CON-02 | Sentiment analysis is excluded; no sentiment-to-grade integration. Future inclusion requires a versioned scope change. |
| CON-03 | Show and store acknowledgment of the approved notice version before reflection; wording must accurately state no automated scoring. |
| CON-04 | No automated evaluation may affect standing; current release has no automated scoring. Any future feature requires an advisor-review gate. |
| CON-05 | Do not infer competence or grade from English style; validate content presence, preserve text, and test diverse writing samples. |
| CON-06 | Require approved hosting, authenticated access, HTTPS and content-free logs/errors. Synthetic data until institutional approval. |
| CON-07 | Restrict reads to author and assigned advisors; C2 gates summaries/evaluations. Confirm owner exception and bounded freshness interpretation. |
| CON-08 | Do not finalize provider/location or production deployment before UC approval; confirm backup/restore capabilities. |
| CON-09 | No research endpoint, export or secondary-use access in this release; future consent must not bypass institutional authorization. |

## 6. Traceability matrix

All 30 IDs are covered exactly once. Status Planned means verification and source reconciliation are pending, not implementation complete. TC-A9-001 through TC-A9-030 are proposed Week 10 test reservations, not existing executed cases. REQ/NFR cases use the verification method in Requirements 0.3; CON cases inspect/test the consequence in section 5. Every row is marked ORPHANED because exact D0/D1/D2 source evidence has not been verified. Candidate components below come from the supplied draft and plan, not invented evidence of completed traceability. Aradana and component owners must replace these markers with exact approved design references before submission. This unresolved state does not meet the rubric’s “no unresolved orphans” expectation.

| Req ID | Description | Design Component (D0/D1/D2) | Status | Test Case |
| --- | --- | --- | --- | --- |
| REQ-01 | Rotation prompts | ORPHANED - candidate C3/C4; D1 I6, D2 DR-1/2 | Planned | TC-A9-001 |
| REQ-02 | Required-response validation | ORPHANED - candidate C1/C3; D1 I3, D2 validation | Planned | TC-A9-002 |
| REQ-03 | Save once | ORPHANED - candidate C3/C7; D1 I9 / Algorithm 2 | Planned | TC-A9-003 |
| REQ-04 | Fallback rotation | ORPHANED - candidate C3/E2; D1 I7, D2 DR-3 | Planned | TC-A9-004 |
| REQ-05 | Eligibility and notice | ORPHANED - candidate C3/E2; D1 I3/I7, D2 DR-6 | Planned | TC-A9-005 |
| REQ-06 | Extractive summary | ORPHANED - candidate C5/C2; D1 I11, D2 summary fallback | Planned | TC-A9-006 |
| REQ-07 | Supervisor evaluation | ORPHANED - candidate C6/C7/E2; D1 I12/I13, D2 DR-5 | Planned | TC-A9-007 |
| REQ-08 | Authorized reflection read | ORPHANED - candidate C2/C7; D1 Algorithm 1 | Planned | TC-A9-008 |
| REQ-09 | Uniform access denial | ORPHANED - candidate C2; D2 DR-4 / denial cases | Planned | TC-A9-009 |
| REQ-10 | Supervisor isolation | ORPHANED - candidate C6/C7; D2 DR-5 | Planned | TC-A9-010 |
| REQ-11 | Advisor roster | ORPHANED - candidate C1/C2; D1 roster endpoint | Planned | TC-A9-011 |
| REQ-12 | Prompt version retention | ORPHANED - candidate C4/C7; D1 I6 / prompt versions | Planned | TC-A9-012 |
| REQ-13 | Access log | ORPHANED - candidate Logging glue; D2 allowlist reference pending | Planned | TC-A9-013 |
| REQ-14 | Browser draft recovery | ORPHANED - candidate C1; D2 failed-save recovery | Planned | TC-A9-014 |
| NFR-01 | Performance | ORPHANED - candidate C1-C7/E2; D2 NFR-1 | Planned | TC-A9-015 |
| NFR-02 | Browser compatibility | ORPHANED - candidate C1; D2 NFR-2 | Planned | TC-A9-016 |
| NFR-03 | Atomic persistence | ORPHANED - candidate C7; D1 Algorithm 2 / D2 NFR-3 | Planned | TC-A9-017 |
| NFR-04 | No content in logs/errors | ORPHANED - candidate C2/C3/C6/logging; D2 NFR-4 | Planned | TC-A9-018 |
| NFR-05 | Backup and recovery | ORPHANED - candidate Hosting/C7; D2 NFR-5 | Planned | TC-A9-019 |
| NFR-06 | Timed usability | ORPHANED - candidate C1; D0 Flows A/B, D2 mapping pending | Planned | TC-A9-020 |
| NFR-07 | Token and transport security | ORPHANED - candidate E1/token glue; D0 sign-in, D2 mapping pending | Planned | TC-A9-021 |
| CON-01 | Consistent assessment | ORPHANED - candidate C3/C5; D2 rule-based scope | Planned | TC-A9-022 |
| CON-02 | No sentiment grading | ORPHANED - candidate C5; D2 exclusion policy | Planned | TC-A9-023 |
| CON-03 | Data-use disclosure | ORPHANED - candidate C1/C3; D2 DR-6 | Planned | TC-A9-024 |
| CON-04 | Human oversight of evaluation | ORPHANED - candidate C5; D2 no-scoring boundary | Planned | TC-A9-025 |
| CON-05 | Writing-style fairness | ORPHANED - candidate C3/C5; D2 validation/summary scope | Planned | TC-A9-026 |
| CON-06 | Protected student data | ORPHANED - candidate C2/C7/E1; D2 privacy rules | Planned | TC-A9-027 |
| CON-07 | Restricted read roles | ORPHANED - candidate C2; D1 Algorithm 1 / D2 DR-4 | Planned | TC-A9-028 |
| CON-08 | Approved storage | ORPHANED - candidate Hosting/C7; D0 deployment decision pending | Planned | TC-A9-029 |
| CON-09 | No research use | ORPHANED - candidate C2; D2 scope exclusion mapping pending | Planned | TC-A9-030 |

## 7. Advisor review record

Review status: pending; discussion occured on 10/28/2026, attendance or resulting approval was supplied. This section records preparation without inventing a meeting. Complete the actual record after Dr. Reeping and/or Dr. Kwuimy review both documents and the matrix.

| Record field | Current entry |
| --- | --- |
| Documents sent | Assignment 8 planning document was sent to advisor for review. |
| Date sent / deadline | Date sent 10/28/2026. 2026-10-30 as Assignment 9 deadline. |
| Review date / participants | October 28,2026; all team members and advisor. |
| What was discussed | Review of assignment 8 and 9 and discussion of IT credential rules relevant to authentication and access requirements for Assignment 9. |
| Decisions and resulting changes | Dr. Reeping said verbally that UC IT can issue guest credentials to external supervisors. The team can contact IT when the build begins. |
| Evidence | Verbal from Dr. Reeping during team meeting. |
| Follow-up owner / date | Hinna Parwez contacts UC IT by 2026-11-12, before sign-in work starts (plan T1). |

Review agenda and edit tracking

| Topic | Affected items | Required record |
| --- | --- | --- |
| Scope and prioritization | 21 priorities; AI/dashboard exclusion; CON-01/02/04/05 | Approve/revise Must set and explicit Won’t list. |
| Security and role conflicts | REQ-08/09/10/14, NFR-07, CON-07 | Resolve supervisor authentication, owner access, assignment revocation and draft isolation. |
| External inputs and data policy | REQ-01/04/05/07; CON-03/06/08/09 | Confirm prompts, E2 contract, notice, retention/corrections and hosting. |
| Acceptance thresholds | NFR-01 to NFR-07; summary/evaluation/pagination limits | Approve numeric criteria and workload; record reasons for changes. |
| Design reconciliation | All 30 matrix rows; interfaces/schema/stack | Provide D0/D1/D2 evidence, remove orphans, agree Week 10 test IDs. |

After review, append a factual entry containing date, attendees, item discussed, decision, affected IDs, exact edits, author and new version. Update both change logs and the specification’s traced requirements version. Keep unresolved items with owners/dates. The suggested three-day lead time would have been October 27 for the plan’s October 30 deadline. Do not backdate a send or meeting; if that lead time was missed, record the actual dates and clarify expectations with the instructor. Repository and submission completion Place Requirements_Document.md and Technical_Specification.md at repository root beside User_Stories.md and Design_Diagrams/. Add both links to existing README section 3 without replacing its other entries. Resolve exact diagram links, commit sources and README, export final PDFs after approval, and have each member submit both complete PDFs with the team names. Repository access was not supplied, so no commit or README edit is claimed.
