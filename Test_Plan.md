# Test Plan: ReFrame CEAS Co-op Reflection System

**Team:** Hinna Parwez, Aradana Nair, Maria Malik, Priyanshi Kulshrestha, Mbathio Lo   
**Advisors:** Dr. David Reeping and Dr. Cedrick Kwuimy   
**Version:** 0.2 (draft, 2026-11-04). Traces to Requirements\_Document.md v0.3 and Technical\_Specification.md v0.1.   
**Owner:**. Each component owner writes and maintains the cases for their component.

Status Note: This document outlines the proposed testing procedures and success criteria. Testing has not yet been conducted, and all test cases are currently marked as "Not Run."

## 1\. Scope

### 1.1 In scope this cycle

| Area | Requirement IDs | Component | Why it is in scope |
| :---- | :---- | :---- | :---- |
| Reflection form, validation, save | REQ-01, 02, 03, 05 | C1, C3, C4, C7, E2 | Must items, core student flow, data integrity |
| Access control and privacy | REQ-08, 09, 10; CON-06, 07 | C2, C6, C7 | Must items, highest risk |
| Security and reliability | NFR-03, 04, 07 | C7, logging, E1 glue | Must items |
| Should items | REQ-04, 06, 07, 11, 12, 14; NFR-01, 02, 05, 06 | C1 to C7 | Planned scope, tested after the Must items |
| Could item | REQ-13 | Logging glue | One case, lowest priority |
| Constraints | CON-01 to 05, 08 | C1, C3, C5, hosting | Checked by inspection or test, one case each |

### 1.2 Out of scope

| Item | Reason |
| :---- | :---- |
| Won't items: automated scoring, sentiment analysis, Power BI/dashboard (CON-02, CON-04, OQ-10) | Excluded from this release |
| Research or export access (CON-09) | Only checked that no such endpoint exists |
| Student read path for supervisor evaluations (OQ-6) | Not decided, no requirement exists |
| Correction of submitted records (OQ-3) | Procedure unresolved |
| Retention and deletion rules (OQ-5) | Needs university guidance |
| Retesting E1 (University Sign-In) and E2 (University Records) internals | Third party. We only test how our code reacts to them (stubs and mocks) |
| Hosting provider internals and managed database engine | Third party. We test backup and restore results only |
| Real student data | Not allowed until UC IT approval (CON-06, CON-08). Synthetic data only |
| Load beyond 25 concurrent users | Pilot is about 25 students. D1 sizing of 500 is OQ-11 and stays untested until decided |

Known limitations: supervisor sign-in is unresolved (OQ-1), so REQ-07 supervisor login tests use a stub identity until it is decided. Rotations 1 to 3 use placeholder prompts (OQ-4).

---

## 2\. Verification and Validation Strategy

**Verification** (did we build it right, against the spec) is done by the team. Unit, integration and system tests check REQ, NFR and CON items against the "Verification method" column in Requirements v0.3. Seeded data and fault injection are used. **Validation** (did we build the right thing) is done by stakeholders. The advisors (Dr. Reeping, Dr. Kwuimy) review the advisor flow and the summary. Student and advisor volunteers run the timed usability sessions for NFR-06 (plan task T20). Supervisor flow is validated by advisor walkthrough only, since no real supervisors are available.

This is not a hardware or machine learning project. The summary is deterministic and extractive, so no accuracy or model metrics are needed. Accuracy is measured as exactness (for example 100% of blank required responses found).

| Layer | Approx. count | Coverage focus |
| :---- | :---- | :---- |
| Unit | 25 | Validation rules, summary cutting (300 characters), access decision logic, token checks |
| Integration | 20 | C1 to C7 API contract, E1/E2 stubs, database transactions, fault injection |
| System / end-to-end | 12 | Student, advisor and supervisor flows in the browser, seeded data |
| Acceptance / usability | 4 sessions | NFR-06 volunteer sessions, advisor walkthrough |
| Non-functional | 6 | Load (NFR-01), browsers (NFR-02), backup and restore (NFR-05), log scan (NFR-04) |

Risk order: access control (REQ-08, 09, 10), data integrity (REQ-03, NFR-03) and token security (NFR-07) are tested first.

---

## 3\. Measurable Success Criteria

| Metric | Target | How measured | Req ID |
| :---- | :---- | :---- | :---- |
| Blank required responses found | 100%, 0 stored reflections | Submit with 1, 2, 3 blank fields, count rows | REQ-02 |
| Reflections per student, placement, rotation | Exactly 1 | Two simultaneous submits, count rows | REQ-03 |
| Denied requests with no content, identical body | 100% of tested cases | One case per actor type plus nonexistent ID, compare bodies | REQ-09 |
| Supervisor reads of reflection content | 0 allowed | Request 3 reflections after submit, check DB grants | REQ-10 |
| Summary field length | At most 300 characters, every field | Short, exactly 300, long, multi-sentence, unbroken text | REQ-06 |
| Page-load and submit p95 latency | Under 2 s at 25 users, 10 min steady state, 1 min warm-up, E2 at 250 ms | Browser elapsed times, p95 | NFR-01 |
| E2 timeout error time | Content-free error within 3 s | Inject E2 timeout | NFR-01 |
| Core-flow steps completed | 100%, no blocked controls or horizontal scroll | 4 browsers (latest 2 versions), 1366x768 and 360x800 | NFR-02 |
| Partial records after forced failure | 0 | Fault injection at each save step, row counts | NFR-03 |
| Content in logs and error responses | 0 lines, 0 responses | Search for seeded marker text across full suite | NFR-04 |
| Backup interval, restart time, restore | At most 24 h, at most 5 min, 100% of reflections | Restore trial into clean instance, restart demo | NFR-05 |
| Usability: student | At least 4 of 5 finish in under 15 min, no help | Volunteer session | NFR-06 |
| Usability: advisor | At least 4 of 5 open a named reflection in under 2 min, no help | Volunteer session | NFR-06 |
| Invalid-token requests rejected with 401 | 100% (missing, altered, wrong issuer, wrong audience, expired by more than 60 s) | One test per case | NFR-07 |
| Advisor roster page size | Default 20, max 50, no duplicates | 51 seeded assignments | REQ-11 |

---

## 4\. Test Cases

Case IDs extend the Week 9 matrix IDs. The matrix row TC-A9-001 now covers cases TC-A9-001-A, \-B and \-C. The matrix and the specification version log must be updated with this change before submission.

Seed data used in all cases: students S1, S2; advisor A1 (assigned to S1), advisor A2 (not assigned); supervisor SUP1 (assigned), SUP2 (not assigned); UC IT user IT1. Each answer contains the marker text `ZZMARK-<case id>`.

### 4.1 Must requirements (3 or more cases each, each group has an edge or failure case)

| ID | Description | Steps | Expected Result | Status |
| :---- | :---- | :---- | :---- | :---- |
| TC-A9-001-A | REQ-01: supported rotation shows its prompt set | 1\. Sign in as S1 with recorded rotation 1\. 2\. Call GET /v1/reflection-form. 3\. Repeat for rotations 2 and 3\. | 200\. Each response returns the set mapped to that rotation. Required prompts are marked. | Not Run |
| TC-A9-001-B | REQ-01 (edge): unsupported rotation | 1\. Seed S2 with rotation 0\. 2\. Call GET /v1/reflection-form. 3\. Repeat with rotation 99\. | 422 error both times. No prompts returned. | Not Run |
| TC-A9-001-C | REQ-01 (failure): incomplete prompt set | 1\. Load a test set for rotation 4 with no goal prompt. 2\. Call GET /v1/reflection-form for rotation 4\. | Form is blocked. No prompts guessed or returned. | Not Run |
| TC-A9-002-A | REQ-02: one blank required response | 1\. As S1, fill all prompts except one required prompt (empty). 2\. Press Review. | The blank prompt ID is reported. Other text is kept. Submission blocked. Reflection count stays 0\. | Not Run |
| TC-A9-002-B | REQ-02: two and three blank responses | 1\. Repeat with 2 blank, then 3 blank required prompts. | All blank prompts reported each time (100%). Reflection count stays 0\. | Not Run |
| TC-A9-002-C | REQ-02 (edge): whitespace-only | 1\. Enter only spaces and a tab in one required prompt. 2\. Press Review. | Treated as blank and reported. Count stays 0\. Text in other fields kept. | Not Run |
| TC-A9-003-A | REQ-03: first save | 1\. As S1 (eligible, notice accepted), submit a valid reflection. 2\. Count rows for S1, placement P1, rotation 1\. | 201 with reflection\_id. Exactly 1 reflection row with all responses. | Not Run |
| TC-A9-003-B | REQ-03 (failure): duplicate | 1\. After A, submit the same placement and rotation again. | 409 with the ID of the first reflection. Row count stays 1\. | Not Run |
| TC-A9-003-C | REQ-03 (edge): simultaneous submit | 1\. Script two POST /v1/reflections calls at the same time for S2. 2\. Count rows. | Exactly 1 stored reflection. The other call gets 409 with the same ID. | Not Run |
| TC-A9-005-A | REQ-05: eligible start | 1\. Stub E2 to confirm S1 enrollment and placement. 2\. S1 accepts the current notice. 3\. Call POST /v1/reflections/start. | 200\. Notice, rotation and eligibility returned. | Not Run |
| TC-A9-005-B | REQ-05 (failure): not eligible or not owner | 1\. Stub E2 as not enrolled for S2. Call start. 2\. As S1, start with S2's placement ID. | 403 both times. No reflection created. | Not Run |
| TC-A9-005-C | REQ-05 (failure): E2 unreachable | 1\. Stub E2 to time out. 2\. As S1, call start. | 503 within 3 s. No reflection created. | Not Run |
| TC-A9-005-D | REQ-05 (edge): notice not acknowledged or stale | 1\. As S1 with no acknowledgment, call start. 2\. Acknowledge an old version, call start. | Refused both times. 409 for the stale version. No reflection created. | Not Run |
| TC-A9-008-A | REQ-08: owner reads | 1\. Seed one reflection for S1. 2\. As S1, GET /v1/reflections/{id}. | 200 with all answers. | Not Run |
| TC-A9-008-B | REQ-08: assigned advisor reads | 1\. As A1 (current assignment to S1), GET the same reflection. | 200 with all answers. | Not Run |
| TC-A9-008-C | REQ-08 (edge): stored record unchanged | 1\. Hash the stored rows before. 2\. Run A and B. 3\. Hash again. | Hashes are identical. | Not Run |
| TC-A9-009-A | REQ-09: denied actor types | 1\. Request S1's reflection as S2, A2, SUP1 and IT1. | 404 with body `{code: "ACCESS_DENIED", message: "Resource unavailable"}` for each. No content. | Not Run |
| TC-A9-009-B | REQ-09 (edge): nonexistent ID | 1\. As A2, request a random unused ID. 2\. Compare body to case A. | Body is byte-identical to case A. | Not Run |
| TC-A9-009-C | REQ-09 (failure): ended assignment | 1\. Set A1's assignment end date to yesterday. 2\. As A1, request S1's reflection. | 404 ACCESS\_DENIED. | Not Run |
| TC-A9-009-D | REQ-09 (edge): stale assignment copy | 1\. Make the assignment copy older than 15 min. 2\. Stub E2 refresh to fail. 3\. As A1, request S1's reflection. | Access denied (503 stale copy per spec for roster, ACCESS\_DENIED for content). No content returned. | Not Run |
| TC-A9-010-A | REQ-10: supervisor after evaluation | 1\. As SUP1, POST /v1/evaluations (valid). 2\. As SUP1, request 3 different reflections by ID. | All 3 return the access-denied response. | Not Run |
| TC-A9-010-B | REQ-10 (inspection): database rights | 1\. List the grants for the C6 database user. 2\. Try a SELECT on reflection tables with that user. | No read right on reflection data. The SELECT fails. | Not Run |
| TC-A9-010-C | REQ-10 (edge): indirect paths | 1\. As SUP1, call the summary include and student reflection list endpoints. | Access denied on both. No content. | Not Run |
| TC-A9-017-A | NFR-03: fault before parent insert | 1\. Inject an error before the reflection row is written. 2\. Submit as S1. 3\. Count reflection and response rows. | 0 reflection rows, 0 response rows. No partial data. | Not Run |
| TC-A9-017-B | NFR-03: fault between parent and responses | 1\. Inject an error after the parent row, before responses. 2\. Submit. 3\. Count rows. | Everything rolled back. 0 rows. | Not Run |
| TC-A9-017-C | NFR-03 (edge): fault at commit and evaluation save | 1\. Inject an error at commit. 2\. Repeat for POST /v1/evaluations. | 0 partial reflections and 0 partial evaluations. | Not Run |
| TC-A9-018-A | NFR-04: log scan | 1\. Run the full automated suite. 2\. Search all log output for `ZZMARK-`. | 0 matches. | Not Run |
| TC-A9-018-B | NFR-04: error bodies | 1\. Trigger 401, 403, 404, 422, 503 with marker text in the input. 2\. Search the responses for the marker. | 0 matches. Field errors contain only prompt IDs and codes. | Not Run |
| TC-A9-018-C | NFR-04 (failure): unhandled exception | 1\. Force an unhandled exception during a save with marker text. 2\. Inspect response and logs. | Generic error. No stack trace, marker or database detail. | Not Run |
| TC-A9-021-A | NFR-07: missing and altered token | 1\. Call GET /v1/me with no token. 2\. Call with one character of the signature changed. | 401 both times. No protected content. | Not Run |
| TC-A9-021-B | NFR-07: wrong issuer and audience | 1\. Call with a validly signed token for the wrong issuer. 2\. Repeat with the wrong audience. | 401 both times. | Not Run |
| TC-A9-021-C | NFR-07 (edge): expiry boundary | 1\. Call with a token expired 30 s ago. 2\. Call with one expired 90 s ago. | 30 s: accepted (within 60 s allowance). 90 s: 401\. | Not Run |
| TC-A9-021-D | NFR-07: transport and exemptions | 1\. Request the site over plain HTTP. 2\. Call every route without a token. | HTTP refused or redirected. Only sign-in, callback and static assets work without a token. | Not Run |

### 4.2 Should, Could and constraint cases (one case each, plus one edge where noted)

| ID | Description | Steps | Expected Result | Status |
| :---- | :---- | :---- | :---- | :---- |
| TC-A9-004 | REQ-04: student-supplied rotation | 1\. Seed a placement with no recorded rotation. 2\. Submit with supported rotation 1\. 3\. Repeat with unsupported rotation 9\. | First stored and marked "student". Second rejected. | Not Run |
| TC-A9-006 | REQ-06: summary and fallback | 1\. Read reflections with short, exactly 300-character, long, multi-sentence and unbroken text as A1 with include=summary. 2\. Force C5 to fail and read again. | Every field is at most 300 characters and only student text. On failure the full reflection is still shown with an unavailable marker. | Not Run |
| TC-A9-007 | REQ-07: evaluation rules | 1\. As SUP1 submit a valid evaluation. 2\. Submit again. 3\. As SUP2 submit. 4\. Stub E2 down and submit. 5\. Test whitespace-only, 1, 5,000 and 5,001 characters. | Only the first valid assigned submission persists. Duplicate 409, unassigned 403, E2 down 503, bad fields 422\. 5,001 rejected. | Not Run |
| TC-A9-011 | REQ-11: roster | 1\. Seed 3 current and 1 ended assignment for A1. 2\. List students. 3\. Seed 51 current and page through. 4\. Try limit 0 and 51\. | First list returns 3\. Paging gives default 20, max 50, no duplicates. Invalid limits return 422\. | Not Run |
| TC-A9-012 | REQ-12: prompt version retention | 1\. Save a reflection under version 1\. 2\. Load version 2 with changed wording. 3\. Read the old reflection. | Old reflection still shows version 1 prompts. Version 1 still exists. | Not Run |
| TC-A9-013 | REQ-13: access log | 1\. Run the denial tests. 2\. Count log lines and decisions. 3\. Sample 10 lines. | One line per decision. Lines hold identifiers and the decision only. | Not Run |
| TC-A9-014 | REQ-14: draft recovery | 1\. Type answers, force a failed save, reload in the same tab. 2\. Expire the token, sign in as the same user. 3\. Sign in as a different user. 4\. Sign out. 5\. Submit successfully. Check the database for draft rows. | Answers restored in steps 1 and 2\. Cleared in steps 3, 4, 5\. 0 draft rows. | Not Run |
| TC-A9-015 | NFR-01: performance | 1\. On staging, run 25 users for 10 min after a 1 min warm-up. 2\. Record p95. 3\. Separately inject an E2 timeout. | p95 under 2 s. Timeout error within 3 s. Record workload, seed size, failures. | Not Run |
| TC-A9-016 | NFR-02: browsers | 1\. Run all three flows in the latest 2 versions of Chrome, Edge, Firefox, Safari at 1366x768 and 360x800. 2\. Demo on 1 phone. | 100% of steps done. No blocked controls or horizontal scroll. Versions recorded. | Not Run |
| TC-A9-019 | NFR-05: backup and restore | 1\. Take a backup with seeded reflections. 2\. Restore into a clean instance. 3\. Kill a service and time its restart. | 100% of reflections recovered. Restart within 5 min. Backup at least every 24 h. | Not Run |
| TC-A9-020 | NFR-06: usability | 1\. Give 5 students a seeded reflection task and 5 advisors a find-and-open task. 2\. Time each, no help. | At least 4 of 5 students under 15 min. At least 4 of 5 advisors under 2 min. | Not Run |
| TC-A9-022 | CON-01: consistent validation | 1\. Submit two reflections with equivalent content from different students. 2\. Compare validation and summary results. | Identical results. | Not Run |
| TC-A9-023 | CON-02: no sentiment analysis | 1\. Inspect code and dependencies for sentiment or scoring features. 2\. Inspect API for score fields. | None found. | Not Run |
| TC-A9-024 | CON-03: data-use notice | 1\. Open the notice as S1. 2\. Check the wording for "no automated scoring". 3\. Check the stored acknowledgment. | Notice shown before the form. Wording is accurate. Version and time stored. | Not Run |
| TC-A9-025 | CON-04: human oversight | 1\. Inspect that no automated result is stored or shown as a grade. | No grade, score or standing field exists. | Not Run |
| TC-A9-026 | CON-05: writing-style fairness | 1\. Submit samples with non-native grammar, very short and very long answers. | All accepted when content is present. Text stored unchanged. | Not Run |
| TC-A9-027 | CON-06: protected data | 1\. Check HTTPS, authenticated access and content-free logs. 2\. Check that only synthetic data is in all environments. | All pass. No real student data present. | Not Run |
| TC-A9-028 | CON-07: restricted read roles | 1\. Run TC-A9-008 and TC-A9-009 results against the role table (author and assigned advisor only). | Only those two roles can read. | Not Run |
| TC-A9-029 | CON-08: approved storage | 1\. Inspect the hosting config and approval record. | Provider and location match the UC-approved decision. Synthetic data until approval. | Not Run |
| TC-A9-030 | CON-09: no research use | 1\. List all routes. 2\. Try to find any export or research endpoint. | None exist. | Not Run |

## 5\. Test Environment

| Item | Development | Staging | Pilot |
| :---- | :---- | :---- | :---- |
| Purpose | Unit and integration | System, load, fault injection, restore | Usability sessions, advisor walkthrough |
| Hosting | Local machines, local database | Managed server and database, same as target (provider pending UC approval) | Same as staging after UC IT approval |
| OS and browsers | Windows 11 and macOS (latest), Chrome | Latest 2 stable versions of Chrome, Edge, Firefox, Safari. Record exact versions at test time | Volunteers' own browsers, versions recorded |
| Devices | Laptops | Laptops at 1366x768, one phone at 360x800 | Volunteer laptops |
| E1 and E2 | Stubs and mocks | Stubs. E2 mock set to 250 ms normal and a timeout mode | Real E1 only if approved, otherwise stub |
| Data | Small seed set (users listed in section 4\) | Seed set plus 51 advisor assignments and load data. Synthetic only | Synthetic only until UC IT approval |
| Network | Local | Normal broadband. E2 delay injected | Normal broadband |
| Tools | Test runner, load tool and database client (to be chosen, see Technical Specification section 4\) | Same plus log search script | None |

Server language, framework and database versions are still unresolved (Technical Specification section 4). They must be recorded here once chosen. The seed script and database schema are committed to the repository so the environment can be rebuilt.

---

## 6\. Pass and Fail Criteria

**Test level.** A test passes only when every expected result in its row is observed exactly. It fails if any result differs, if any protected content appears in a response or log, or if a stored record is partial or changed. A test is Blocked when a dependency (E1 or E2 stub, hosting approval) is missing. Blocked tests are reported and are not counted as passed.

**Metric level.** Each metric in section 3 passes only when it meets its threshold. For example, p95 of 2.0 s or more fails NFR-01. A single content leak in logs or responses fails NFR-04. One partial record fails NFR-03. Fewer than 4 of 5 volunteers finishing in time fails NFR-06.

**Plan level.** The plan succeeds when:

- 100% of Must cases (sections 4.1) pass, with 0 open Critical or High defects.  
- At least 90% of Should and Could cases pass, and every failure has a logged defect and owner.  
- All metric thresholds in section 3 are met or have an approved exception from the advisors.  
- No test result is missing: no case is left Not Run at the final review.

Defect levels: Critical means content exposure, access bypass or data loss. High means a Must case fails. Medium and Low are other failures.

---

## 7\. Schedule and Evidence

Milestone names below must be matched to the Week 8 plan before submission.

| Milestone | Test activity | Owner | Evidence |
| :---- | :---- | :---- | :---- |
| Sprint 1: sign-in and access control | Unit and integration for REQ-08, 09, 10, NFR-07 (first, highest risk) | Aradana Nair (C2), sign-in owner TBD | Test run logs, 401 and 404 result table |
| Sprint 2: reflection form and save | Unit and integration for REQ-01, 02, 03, 05, NFR-03 fault injection | C3, C4, C7 owners | Row count reports, fault injection log |
| Sprint 3: Should features | Tests for REQ-04, 06, 07, 11, 12, 14 | Component owners | Case results in this plan |
| Before design review | System tests on staging, NFR-04 log scan, NFR-01 load run, NFR-02 browsers | QA lead | Latency report, browser version list, log scan output |
| Before demo | Backup and restore (NFR-05), CON inspections | QA lead, Hinna Parwez (hosting) | Restore report, inspection checklist |
| Pilot / acceptance | Usability sessions (NFR-06), advisor walkthrough | QA lead, advisors | Timing sheets, advisor sign-off notes |
| Final Design Report (Assignment \#10) | Summary of results and defects | All members | Updated Status column, defect list |

Two test cases will be swapped with another team before Sprint 2 to check that the steps can be run without questions.

---

## 8\. Definition of Done

**Feature level.** A feature is done when:

- [ ] Its code is merged after review by one other teammate.  
- [ ] Its unit and integration tests are written and pass.  
- [ ] Its test cases in this plan have a Status of Pass with evidence.  
- [ ] It meets the related metric in section 3\.  
- [ ] No Critical or High defect is open against it.  
- [ ] No reflection or evaluation text appears in its logs or errors.  
- [ ] Its documentation is updated (API contract, README, traceability matrix row).  
- [ ] The component owner signs off.

**Project level.** The project is done when:

- [ ] All Must cases pass and the plan-level criteria in section 6 are met.  
- [ ] All traceability rows are free of ORPHANED markers and match D0/D1/D2.  
- [ ] Open questions that affect tests (OQ-1, OQ-4, OQ-7, OQ-8, OQ-9, OQ-11) are resolved or recorded as exceptions.  
- [ ] Usability and advisor walkthrough results are recorded.  
- [ ] The advisors have reviewed the results and the QA lead and team have signed off.  
- [ ] Test\_Plan.md is in the repository root, linked in README section 3 and the appendix, and each member has submitted the PDF with all team names.