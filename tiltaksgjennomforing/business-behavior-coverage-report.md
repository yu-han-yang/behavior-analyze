# Business Behavior Coverage Report

## Executive Summary

- Project: tiltaksgjennomforing
- Generated suite: tests/EM_tiltaksgjennomforing_True_25_false_false_SPECIFIED_false_0_Test.java (195 tests, 203 service-call occurrences)
- Coverage inputs: reports/report.xml and reports/report.csv
- Source: src/main/java and src/main/resources
- Documented behaviors: 44
- Covered: 0
- Partially covered: 0
- Not covered: 44
- Unclear: 0
- Business behavior coverage: 0.0%
- JaCoCo signal: 820/6,541 lines (12.5%), 127/8,084 branches (1.6%), 409/3,691 methods (11.1%)
- Function/API invocation coverage: 77/83 (92.8%); two shared-route families leave five exact functions ambiguous
- Required-step attempt coverage: 76/91 (83.5%)
- Required-step application-reach coverage: 0/91 (0.0%)
- Required-step context-valid success coverage: 0/91 (0.0%)
- Primary workflow success coverage: 0/44 (0.0%)
- Core required-step failure coverage: 0/392 (0.0%)
- Alternative path success coverage: 0/13 (0.0%)
- Alternative-step failure coverage: 0/190 (0.0%)
- Optional/supporting execution coverage: 0/21 (0.0%)
- Optional-step failure coverage: 0/73 (0.0%)
- All workflow-context business-failure coverage: 0/655 (0.0%)
- Unique source business-branch coverage: 0/422 (0.0%)
- Core workflow-outcome checklist coverage: 0/436

The suite broadly contacts routes but never proves a documented business outcome. Every test resets the database and SUT. No test then creates valid state and carries a Location, UUID, freshness value, child id, or notification id into a successor call. Of 203 calls, 100 return 400, 75 return 401, five return 403, ten return 500, and thirteen return 200. All thirteen successful calls target support APIs (code lists, features, health, rights URLs, or OpenAPI), not the 44 workflows.

The funnel is internally consistent: 0 context-valid successes <= 0 application reaches <= 76 attempts. The few covered controller entry/log lines stop at malformed input or generic access checks, so they are not promoted to application reach. The generated 500 tests identify CorrelationIdFilter.doFilterInternal and are infrastructure faults, not documented business failures.

## Coverage Matrix

| ID | Business behavior | Attempts | App reached | Context-valid | Primary | Core failures | Status |
|---|---|---:|---:|---:|---|---:|---|
| B1 | Obtain a Role-Scoped Agreement Worklist | 1/1 | 0/1 | 0/1 | No | 0/0 | Not Covered |
| B2 | Save and Reuse a Filtered Agreement Search | 2/2 | 0/2 | 0/2 | No | 0/0 | Not Covered |
| B3 | Enter a Work-Training Agreement | 5/8 | 0/8 | 0/8 | No | 0/44 | Not Covered |
| B4 | Enter a Mentor Agreement | 7/10 | 0/10 | 0/10 | No | 0/49 | Not Covered |
| B5 | Enter a Subsidy-Backed Agreement After Decision-Maker Approval | 6/9 | 0/9 | 0/9 | No | 0/62 | Not Covered |
| B6 | Reject a Pending Subsidy Period | 6/9 | 0/9 | 0/9 | No | 0/56 | Not Covered |
| B7 | Return a Rejected Subsidy Period for Re-decision | 1/1 | 0/1 | 0/1 | No | 0/3 | Not Covered |
| B8 | Assign an Employer-Created Agreement to an Advisor | 2/2 | 0/2 | 0/2 | No | 0/12 | Not Covered |
| B9 | Reopen and Revise a Partially Approved Draft | 6/7 | 0/7 | 0/7 | No | 0/30 | Not Covered |
| B10 | Authorize After-Registration | 0/1 | 0/1 | 0/1 | No | 0/4 | Not Covered |
| B11 | Remove After-Registration Authorization | 0/1 | 0/1 | 0/1 | No | 0/4 | Not Covered |
| B12 | Shorten an Entered Agreement | 1/1 | 0/1 | 0/1 | No | 0/8 | Not Covered |
| B13 | Extend an Entered Agreement | 1/1 | 0/1 | 0/1 | No | 0/9 | Not Covered |
| B14 | Amend Subsidy Calculation | 1/1 | 0/1 | 0/1 | No | 0/7 | Not Covered |
| B15 | Amend Agreement Contact Information | 1/1 | 0/1 | 0/1 | No | 0/5 | Not Covered |
| B16 | Amend Job Description | 1/1 | 0/1 | 0/1 | No | 0/5 | Not Covered |
| B17 | Amend Follow-Up and Adaptation Text | 1/1 | 0/1 | 0/1 | No | 0/5 | Not Covered |
| B18 | Replace Work-Training Goals | 1/1 | 0/1 | 0/1 | No | 0/7 | Not Covered |
| B19 | Replace Inclusion-Subsidy Expenses | 2/2 | 0/2 | 0/2 | No | 0/11 | Not Covered |
| B20 | Amend Mentor Details | 1/1 | 0/1 | 0/1 | No | 0/6 | Not Covered |
| B21 | Change Subsidy Cost Center Before Entry | 1/1 | 0/1 | 0/1 | No | 0/5 | Not Covered |
| B22 | Prepare an Arena-Cleanup Agreement at the Correct Migration Boundary | 4/4 | 0/4 | 0/4 | No | 0/16 | Not Covered |
| B23 | Refresh Participant Follow-Up and NAV Units | 1/1 | 0/1 | 0/1 | No | 0/4 | Not Covered |
| B24 | Annul an Agreement | 2/2 | 0/2 | 0/2 | No | 0/7 | Not Covered |
| B25 | Soft-Delete an Agreement | 1/1 | 0/1 | 0/1 | No | 0/2 | Not Covered |
| B26 | Obtain the Employer Portal Agreement Portfolio | 1/1 | 0/1 | 0/1 | No | 0/0 | Not Covered |
| B27 | Obtain the Decision-Maker Work Queue | 1/1 | 0/1 | 0/1 | No | 0/2 | Not Covered |
| B28 | Resolve a Valid Employer Organization | 1/1 | 0/1 | 0/1 | No | 0/3 | Not Covered |
| B29 | Obtain an Approved Agreement PDF | 1/1 | 0/1 | 0/1 | No | 0/3 | Not Covered |
| B30 | Review and Clear Agreement Notifications | 2/2 | 0/2 | 0/2 | No | 0/1 | Not Covered |
| B31 | Journal Approved Agreement Versions | 2/2 | 0/2 | 0/2 | No | 0/0 | Not Covered |
| B32 | Repair Missing Wage-Subsidy Totals on Selected Agreements | 1/1 | 0/1 | 0/1 | No | 0/4 | Not Covered |
| B33 | Repair Missing Permanent-Subsidy Reduction Data | 1/1 | 0/1 | 0/1 | No | 0/0 | Not Covered |
| B34 | Backfill Subsidy Periods for One Agreement | 1/1 | 0/1 | 0/1 | No | 0/2 | Not Covered |
| B35 | Recalculate Unhandled Subsidy Periods | 1/1 | 0/1 | 0/1 | No | 0/4 | Not Covered |
| B36 | Diagnose Subsidy-Period Date Ordering | 1/1 | 0/1 | 0/1 | No | 0/1 | Not Covered |
| B37 | Annul One Subsidy Period | 1/1 | 0/1 | 0/1 | No | 0/1 | Not Covered |
| B38 | Replace a Period and Resend It as Approved | 1/1 | 0/1 | 0/1 | No | 0/3 | Not Covered |
| B39 | Replace a Period with an Unhandled Copy | 1/1 | 0/1 | 0/1 | No | 0/3 | Not Covered |
| B40 | Reclassify Historical Periods as Arena-Treated | 1/1 | 0/1 | 0/1 | No | 0/3 | Not Covered |
| B41 | Synchronize Selected Agreements to the Data Warehouse | 1/1 | 0/1 | 0/1 | No | 0/0 | Not Covered |
| B42 | Synchronize All Eligible Agreements to the Data Warehouse Asynchronously | 1/1 | 0/1 | 0/1 | No | 0/0 | Not Covered |
| B43 | Backfill One Agreement Event Message | 1/1 | 0/1 | 0/1 | No | 0/1 | Not Covered |
| B44 | Backfill All Agreement Event Messages Asynchronously | 1/1 | 0/1 | 0/1 | No | 0/0 | Not Covered |

Confidence is high for every outcome verdict: test resets, requests, assertions, and coverage agree. B4, B31, B41, B42, and B44 have medium confidence only on the exact boundary at which a controller entry/access-check line stopped; that ambiguity does not affect the Not Covered verdict.

## Behavior Evidence and Failure Ledger

For every B1–B44 row above:

- Happy path: Not Covered. No continuous ordered scenario uses documented actors, pre-state, values, response handoffs, and terminal assertions.
- Required-step application reach and context-valid success: zero. A matching route returning 400/401/403/500 is retained only in the attempt layer.
- Required failures: every one of the 392 workflow occurrences is Not Covered. No test establishes all earlier prerequisites, violates only the documented condition, and asserts its source discriminator.
- Alternative paths: 0/13 complete. Alternative failures: 0/190.
- Optional steps: 0/21 executed with valid context. Optional failures: 0/73.
- Unique branch contribution: none. The 655 workflow occurrences deduplicate to 422 keys of exact function, normalized discriminator, and normalized condition.
- Verification: no persisted-state, version, lifecycle, child-row, ownership, journal, notification, DVH/event, or forbidden-side-effect assertion exists.

The exact per-behavior required-failure denominators are in the matrix. Alternative and optional counts by behavior were retained separately when computing the totals; they do not alter core status.

Notable method-level evidence:

- AvtaleController.opprettMentorAvtale covers only line 407; business lines 408–424 are missed.
- InternalAvtaleController.hentIkkeJournalfoerteAvtaler touches validation/catch logic, while repository/mapping lines 36–43 are missed.
- InternalDvhMeldingProdusentController.patcheAvtale and patchAlleAvtaler cover their first log line, then stop before the access check/action.
- AvtaleHendelseController bulk methods cover their first log line, then stop before access and service execution.
- AvtaleController is otherwise absent from covered mapped business methods. Aggregate class coverage is not used as application-reach evidence.

## Cross-Behavior Gaps

- Database/SUT reset boundaries make cross-test stitching invalid.
- Shared /godkjenn probes omit innlogget-part, so participant, employer, and advisor functions remain ambiguous.
- No mentor creation request uses avtalerolle=ARBEIDSGIVER.
- The after-registration endpoint is a source-level toggle, but tests provide no known pre-state to distinguish enable from disable.
- Direct status assertions replace business assertions.
- Malformed UUIDs, missing required fields, authentication failures, and absent state are mixed; no negative test isolates a domain rule.
- Code coverage is mostly framework/support execution and does not corroborate a business transition.

## Suggested Additional Tests

### T1: Enter a work-training agreement

- Priority: P0.
- Target: B3 primary path, Steps 1–8.
- Reset once; fix the clock before 2026-07-01. Stub participant 01039513753 as adult/unprotected, advisor Q987654 with write access, Ereg virksomhet 111222333, and matching employer Altinn rights.
- Call sequence: POST /avtaler with ARBEIDSTRENING; capture Location id; GET as VEILEDER and capture sistEndret; PUT the complete documented work-training body with freshness; POST /godkjenn as DELTAKER; refresh; POST as ARBEIDSGIVER; refresh; POST as VEILEDER; final GET.
- Assert 201/200 responses, every freshness binding, exact actor identity, all three approval timestamps, entered status, avtaleInngått, ikrafttredelsestidspunkt, one current version, and expected events without duplicates.
- Passing this adds eight context-valid steps and one primary success. B3 remains Partially Covered until all 44 core failures are covered.

### T2: Journal one approved agreement version

- Priority: P0.
- Target: B31 Steps 1–2.
- Fixture an advisor-approved AvtaleInnhold UUID 22222222-2222-4222-8222-222222222222 with journalpostId null; use the configured system identity.
- GET /internal/avtaler; assert the exact version UUID and payload. Stub the external journal id 453997128. PUT /internal/avtaler with body {"22222222-2222-4222-8222-222222222222":"453997128"}. GET again.
- Assert only that version is marked, other business fields are unchanged, and the version disappears from the export list.
- Passing this adds two context-valid steps and one primary success, making B31 Covered.

### T3: Patch one selected agreement to DVH

- Priority: P1.
- Target: B41 Step 1.
- Fixture agreement 33333333-3333-4333-8333-333333333333 and authenticate an AAD user in the configured DVH group.
- POST /utvikler-admin/dvh-melding/patch with body {"avtaleIder":["33333333-3333-4333-8333-333333333333"]}.
- Assert 200, exactly one DvhMeldingEntitet with PATCHING type, matching agreement/status/actor and valid Avro payload; assert no agreement mutation or row for an unrequested id.
- Passing this adds one context-valid step and one primary success, making B41 Covered.

After these, add one focused test per core failure. Each must keep all unrelated fields and state valid, violate only the target rule, assert the documented discriminator, assert unchanged persistence, and assert forbidden side effects did not occur.

## Notes and Assumptions

- report.xml is minified but valid JaCoCo XML and was parsed directly; CSV was only a cross-check.
- All generated tests are treated as executed because the supplied JaCoCo session accompanies the suite.
- The workflow document's own Coverage result: Complete fields were ignored.
- Function invocation credit is withheld for employer mentor creation, three /godkjenn actor variants, and two after-registration meanings.
- Source discrepancy: after-registration enable and disable are documented as two functions, while source implements one state-dependent toggle.
- No expected artifact was missing.
