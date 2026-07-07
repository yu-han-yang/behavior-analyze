# Business Behavior Coverage Report

## Executive Summary

- Project under analysis: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing`
- Business specification: `business-behavior.md`
- Test suites analyzed: `tests/EM_tiltaksgjennomforing_True_25_false_false_SPECIFIED_false_0_Test.java` — 195 generated test methods
- Application calls analyzed: 203 calls across 80 distinct normalized routes (78 owned business route signatures plus two unowned probes: OpenAPI docs and an alternate approval route)
- Coverage reports analyzed: `reports/report.xml` (primary), `reports/report.csv` (counter cross-check)
- Source roots analyzed: `src/main/java`, with configuration/stub context from `src/main/resources` and `src/test/resources`
- Total documented behaviors: `81`
- Total documented failure entries: `461`
- Covered / Partially Covered / Not Covered / Unclear: `7 / 0 / 74 / 0`
- Business behavior coverage: `7/81 (8.6%)`
- Function/API invocation coverage: `77/83 (92.8%)`, plus 3 ambiguous shared-route families affecting 6 exact functions
- Required-step attempt coverage: `79/85 (92.9%)`
- Required-step application-reach coverage: `7/85 (8.2%)`
- Required-step context-valid success coverage: `7/85 (8.2%)`
- Happy-path behavior coverage: `7/81 (8.6%)`
- Documented business-failure coverage: `0/461 (0.0%)`
- Unique source business-branch coverage: `0/461 (0.0%)`
- Behavior outcome checklist coverage: `7/542 (1.3%)`
- Optional verification execution coverage: `0/37 (0.0%)`
- Combined JaCoCo signal: lines `820/6541 (12.5%)`, branches `127/8084 (1.6%)`, methods `409/3691 (11.1%)`, classes `129/394 (32.7%)`

The suite has broad route-probe breadth but almost no domain-state depth. Every owned route family is called, yet most calls stop at malformed UUID/date/enum/body binding, a missing `innlogget-part` cookie, authentication, or an administrative admission gate. JaCoCo corroborates complete execution only for the seven stateless lookup/health behaviors B54–B60. It does not convert the remaining controller/filter signal into business outcomes. The funnel is internally consistent: context-valid success 7 ≤ application reached 7 ≤ attempted 79.

All tests call `controller.resetDatabase(Arrays.asList())` and `controller.resetStateOfSUT()` in `@BeforeEach`. Therefore calls from different test methods cannot be stitched into one workflow. No generated test captures a business response value such as `Location`, `sokId`, or `sistEndret` and reuses it in a later business call. Eight test methods contain two application calls, but those pairs repeat failing probes rather than establish and consume business state.

## Inventory Validation

- Parsed behavior count: `81`; expected 81 — reconciled.
- Parsed failure-entry count: `461`; expected 461 — reconciled.
- Behaviors with `Failure and exceptional cases: None.`: `20` — B1, B7, B8, B50, B52, B54, B55, B56, B57, B58, B59, B60, B61, B62, B66, B69, B77, B78, B80, B81.
- Malformed or unparsed behavior/failure entries: `0`.
- Exact-function-name mapping failures through `full-behavior.md`: `0`; all 83 owned exact functions occur in the function inventory.
- Required workflow steps: `85`; optional verification steps: `37`.
- Denominator reconciliation: behavior outcomes = `81 happy paths + 461 documented failures = 542`; unique source-branch denominator = `461` after the required key normalization.
- Inventory completeness: complete; no silent omissions or substitutions were needed.

## Coverage Matrix

| ID | Business Behavior | Required Steps Attempted | Application Reached | Context-Valid Steps | Happy Path | Failure Coverage | Optional Verification | Status | Confidence |
|---|---|---:|---:|---:|---|---:|---:|---|---|
| B1 | Role-scoped agreement listing | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | N/A | Not Covered | High |
| B2 | Advisor-created agreement | 1/1 | 0/1 | 0/1 | Not Covered | 0/13 | N/A | Not Covered | High |
| B3 | Arena cleanup agreement creation | 1/1 | 0/1 | 0/1 | Not Covered | 0/13 | N/A | Not Covered | High |
| B4 | Employer-created agreement | 1/1 | 0/1 | 0/1 | Not Covered | 0/5 | N/A | Not Covered | High |
| B5 | Advisor-created mentor agreement | 1/1 | 0/1 | 0/1 | Not Covered | 0/14 | N/A | Not Covered | High |
| B6 | Employer-created mentor agreement | 0/1 | 0/1 | 0/1 | Not Covered | 0/9 | N/A | Not Covered | High |
| B7 | Check participant overlap | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | N/A | Not Covered | High |
| B8 | Saved agreement search registration and replay | 2/2 | 0/2 | 0/2 | Not Covered | 0/0 | N/A | Not Covered | High |
| B9 | Agreement detail retrieval by id | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | N/A | Not Covered | High |
| B10 | Agreement detail retrieval by agreement number | 1/1 | 0/1 | 0/1 | Not Covered | 0/2 | N/A | Not Covered | High |
| B11 | Agreement version history retrieval | 1/1 | 0/1 | 0/1 | Not Covered | 0/2 | N/A | Not Covered | High |
| B12 | Draft agreement update | 1/1 | 0/1 | 0/1 | Not Covered | 0/25 | 0/1 | Not Covered | High |
| B13 | Draft agreement update dry-run | 1/1 | 0/1 | 0/1 | Not Covered | 0/25 | N/A | Not Covered | High |
| B14 | Agreement sharing with a party | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | 0/1 | Not Covered | High |
| B15 | Participant agreement approval | 0/1 | 0/1 | 0/1 | Not Covered | 0/8 | 0/1 | Not Covered | High |
| B16 | Employer agreement approval | 0/1 | 0/1 | 0/1 | Not Covered | 0/8 | 0/1 | Not Covered | High |
| B17 | Mentor confidentiality signing | 1/1 | 0/1 | 0/1 | Not Covered | 0/4 | 0/1 | Not Covered | High |
| B18 | Advisor final approval | 0/1 | 0/1 | 0/1 | Not Covered | 0/17 | 0/1 | Not Covered | High |
| B19 | Advisor approval on behalf of participant | 1/1 | 0/1 | 0/1 | Not Covered | 0/19 | 0/1 | Not Covered | High |
| B20 | Advisor approval on behalf of employer | 1/1 | 0/1 | 0/1 | Not Covered | 0/17 | 0/1 | Not Covered | High |
| B21 | Advisor approval on behalf of participant and employer | 1/1 | 0/1 | 0/1 | Not Covered | 0/18 | 0/1 | Not Covered | High |
| B22 | Approval revocation | 1/1 | 0/1 | 0/1 | Not Covered | 0/4 | 0/1 | Not Covered | High |
| B23 | After-registration eligibility marking | 0/1 | 0/1 | 0/1 | Not Covered | 0/4 | 0/1 | Not Covered | High |
| B24 | After-registration eligibility removal | 0/1 | 0/1 | 0/1 | Not Covered | 0/4 | 0/1 | Not Covered | High |
| B25 | Subsidy period approval | 1/1 | 0/1 | 0/1 | Not Covered | 0/9 | 0/1 | Not Covered | High |
| B26 | Subsidy period rejection | 1/1 | 0/1 | 0/1 | Not Covered | 0/8 | 0/1 | Not Covered | High |
| B27 | Rejected subsidy period return | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | 0/1 | Not Covered | High |
| B28 | Agreement shortening | 1/1 | 0/1 | 0/1 | Not Covered | 0/18 | 0/1 | Not Covered | High |
| B29 | Dry-run agreement shortening | 1/1 | 0/1 | 0/1 | Not Covered | 0/17 | N/A | Not Covered | High |
| B30 | Agreement extension | 1/1 | 0/1 | 0/1 | Not Covered | 0/20 | 0/1 | Not Covered | High |
| B31 | Dry-run agreement extension | 1/1 | 0/1 | 0/1 | Not Covered | 0/20 | N/A | Not Covered | High |
| B32 | Subsidy calculation change | 1/1 | 0/1 | 0/1 | Not Covered | 0/9 | 0/1 | Not Covered | High |
| B33 | Dry-run subsidy calculation change | 1/1 | 0/1 | 0/1 | Not Covered | 0/9 | N/A | Not Covered | High |
| B34 | Contact information change | 1/1 | 0/1 | 0/1 | Not Covered | 0/13 | 0/1 | Not Covered | High |
| B35 | Job description change | 1/1 | 0/1 | 0/1 | Not Covered | 0/10 | 0/1 | Not Covered | High |
| B36 | Follow-up and adaptation text change | 1/1 | 0/1 | 0/1 | Not Covered | 0/6 | 0/1 | Not Covered | High |
| B37 | Work-training goal replacement | 1/1 | 0/1 | 0/1 | Not Covered | 0/8 | 0/1 | Not Covered | High |
| B38 | Inclusion subsidy expense replacement | 1/1 | 0/1 | 0/1 | Not Covered | 0/10 | 0/1 | Not Covered | High |
| B39 | Mentor details change | 1/1 | 0/1 | 0/1 | Not Covered | 0/11 | 0/1 | Not Covered | High |
| B40 | Cost center change | 1/1 | 0/1 | 0/1 | Not Covered | 0/5 | 0/1 | Not Covered | High |
| B41 | Arena migration date adjustment | 1/1 | 0/1 | 0/1 | Not Covered | 0/4 | 0/1 | Not Covered | High |
| B42 | Dry-run Arena migration date adjustment | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | N/A | Not Covered | High |
| B43 | Employer account number lookup | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | N/A | Not Covered | High |
| B44 | Agreement PDF download | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | N/A | Not Covered | High |
| B45 | Salesforce dialog visibility check | 1/1 | 0/1 | 0/1 | Not Covered | 0/2 | N/A | Not Covered | High |
| B46 | Follow-up unit refresh | 1/1 | 0/1 | 0/1 | Not Covered | 0/7 | 0/1 | Not Covered | High |
| B47 | Advisor takeover of agreement | 1/1 | 0/1 | 0/1 | Not Covered | 0/8 | 0/1 | Not Covered | High |
| B48 | Agreement annulment | 1/1 | 0/1 | 0/1 | Not Covered | 0/5 | 0/1 | Not Covered | High |
| B49 | Agreement soft deletion | 1/1 | 0/1 | 0/1 | Not Covered | 0/2 | 0/1 | Not Covered | High |
| B50 | Employer Min Side agreement listing | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | N/A | Not Covered | High |
| B51 | Decision-maker work queue listing | 1/1 | 0/1 | 0/1 | Not Covered | 0/1 | N/A | Not Covered | High |
| B52 | Logged-in user context lookup | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | N/A | Not Covered | High |
| B53 | Employer organization lookup | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | N/A | Not Covered | High |
| B54 | Altinn rights request URL lookup | 1/1 | 1/1 | 1/1 | Covered | 0/0 | N/A | Covered | High |
| B55 | Combined code-list lookup | 1/1 | 1/1 | 1/1 | Covered | 0/0 | N/A | Covered | High |
| B56 | Agreement status code-list lookup | 1/1 | 1/1 | 1/1 | Covered | 0/0 | N/A | Covered | High |
| B57 | Measure type code-list lookup | 1/1 | 1/1 | 1/1 | Covered | 0/0 | N/A | Covered | High |
| B58 | Feature toggle evaluation | 1/1 | 1/1 | 1/1 | Covered | 0/0 | N/A | Covered | High |
| B59 | Feature variant lookup | 1/1 | 1/1 | 1/1 | Covered | 0/0 | N/A | Covered | High |
| B60 | Internal health probe | 1/1 | 1/1 | 1/1 | Covered | 0/0 | N/A | Covered | High |
| B61 | Overview notification listing | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | N/A | Not Covered | High |
| B62 | Agreement modal notification listing | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | N/A | Not Covered | High |
| B63 | Agreement notification log listing | 1/1 | 0/1 | 0/1 | Not Covered | 0/2 | N/A | Not Covered | High |
| B64 | Single notification read marking | 2/2 | 0/2 | 0/2 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B65 | Bulk notification read marking | 2/2 | 0/2 | 0/2 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B66 | Journal export and completion marking | 2/2 | 0/2 | 0/2 | Not Covered | 0/0 | 0/1 | Not Covered | High |
| B67 | Selected agreement wage-subsidy recalculation | 1/1 | 0/1 | 0/1 | Not Covered | 0/4 | N/A | Not Covered | High |
| B68 | Missing reduced-percent date repair | 1/1 | 0/1 | 0/1 | Not Covered | 0/1 | N/A | Not Covered | High |
| B69 | Dry-run missing reduced-percent date fix | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | N/A | Not Covered | High |
| B70 | Admin subsidy-period generation for one agreement | 1/1 | 0/1 | 0/1 | Not Covered | 0/2 | 0/1 | Not Covered | High |
| B71 | Unhandled subsidy-period recalculation | 1/1 | 0/1 | 0/1 | Not Covered | 0/4 | 0/1 | Not Covered | High |
| B72 | Subsidy-period date-order diagnostic | 1/1 | 0/1 | 0/1 | Not Covered | 0/1 | N/A | Not Covered | High |
| B73 | Subsidy-period annulment | 1/1 | 0/1 | 0/1 | Not Covered | 0/1 | N/A | Not Covered | High |
| B74 | Annul and resend approved subsidy period | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | N/A | Not Covered | High |
| B75 | Annul and generate unhandled subsidy period | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | N/A | Not Covered | High |
| B76 | Annul and generate Arena-treated periods | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | 0/1 | Not Covered | High |
| B77 | Selected data warehouse patching | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | N/A | Not Covered | High |
| B78 | All-agreement data warehouse patching | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | N/A | Not Covered | High |
| B79 | Single agreement event publication | 1/1 | 0/1 | 0/1 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B80 | All-agreement event publication | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | N/A | Not Covered | High |
| B81 | All-agreement event publication dry-run | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | N/A | Not Covered | High |

## Function/API Invocation Checklist

| Exact Function Name | Method/Route | Attempted? | Distinguishable? | Representative Tests | Result Classes |
|---|---|---|---|---|---|
| list accessible agreements | `GET /avtaler` | Yes | Yes | `test_5_getOnAvtalerReturns400`, `test_6_getOnAvtalerReturns400`, `test_7_getOnAvtalerReturns400` | binding/generic 400, authentication |
| create advisor agreement | `POST /avtaler` | Yes | Yes | `test_0_postOnAvtalerCauses500_internalServerError`, `test_11_getOnAvtalerReturns400`, `test_12_getOnAvtalerReturns400` | binding/generic 400, authentication, admission, infrastructure/unexpected 5xx |
| create Arena cleanup agreement | `POST /avtaler` | Yes | Yes | `test_0_postOnAvtalerCauses500_internalServerError`, `test_11_getOnAvtalerReturns400`, `test_12_getOnAvtalerReturns400` | binding/generic 400, authentication, admission, infrastructure/unexpected 5xx |
| create employer agreement | `POST /avtaler/opprett-som-arbeidsgiver` | Yes | Yes | `test_54_postOnOpprett_som_arbeidsgiverReturns400`, `test_55_postOnOpprett_som_arbeidsgiverReturns400`, `test_77_postOnOpprett_som_arbeidsgiverReturns401` | binding/generic 400, authentication, admission |
| create mentor agreement as advisor | `POST /avtaler/opprett-mentor-avtale` | Yes | Yes | `test_22_postOnOpprett_mentor_avtaleCauses500_internalServerError`, `test_48_postOnOpprett_mentor_avtaleReturns400`, `test_78_postOnOpprett_mentor_avtaleReturns401` | binding/generic 400, authentication, infrastructure/unexpected 5xx |
| create mentor agreement as employer | `POST /avtaler/opprett-mentor-avtale` | No — ambiguous shared-route attempt | No | `test_22_postOnOpprett_mentor_avtaleCauses500_internalServerError`, `test_48_postOnOpprett_mentor_avtaleReturns400`, `test_78_postOnOpprett_mentor_avtaleReturns401` | binding/generic 400, authentication, infrastructure/unexpected 5xx, ambiguous shared-route outcome |
| check participant overlap | `GET /avtaler/deltaker-allerede-paa-tiltak` | Yes | Yes | `test_33_getOnDeltaker_allerede_paa_tiltakReturns400`, `test_34_getOnDeltaker_allerede_paa_tiltakReturns400`, `test_45_getOnDeltaker_allerede_paa_tiltakReturns400` | binding/generic 400, authentication |
| search agreements and save search | `POST /avtaler/sok` | Yes | Yes | `test_47_postOnSokReturns400`, `test_49_postOnSokReturns400`, `test_50_postOnSokReturns400` | binding/generic 400, authentication |
| replay saved agreement search | `GET /avtaler/sok` | Yes | Yes | `test_38_getOnSokReturns400`, `test_44_getOnSokReturns400`, `test_63_getOnSokReturns401` | binding/generic 400, authentication |
| retrieve agreement by id | `GET /avtaler/{avtaleId}` | Yes | Yes | `test_39_getOnAvtalReturns400`, `test_65_getOnAvtalReturns401` | binding/generic 400, authentication |
| retrieve agreement by agreement number | `GET /avtaler/avtaleNr/{avtaleNr}` | Yes | Yes | `test_95_getOnAvtaleNrReturns400`, `test_144_getOnAvtaleNrReturns401` | binding/generic 400, authentication |
| list agreement versions | `GET /avtaler/{avtaleId}/versjoner` | Yes | Yes | `test_92_getOnVersjonerReturns400`, `test_141_getOnVersjonerReturns401` | binding/generic 400, authentication |
| update agreement | `PUT /avtaler/{avtaleId}` | Yes | Yes | `test_56_putOnAvtalReturns400`, `test_57_putOnAvtalReturns400`, `test_58_putOnAvtalReturns400` | binding/generic 400, authentication |
| dry-run agreement update | `PUT /avtaler/{avtaleId}/dry-run` | Yes | Yes | `test_139_putOnDry_runReturns400`, `test_186_putOnDry_runReturns401` | binding/generic 400, authentication |
| share agreement with party | `POST /avtaler/{avtaleId}/del-med-avtalepart` | Yes | Yes | `test_116_postOnDel_med_avtalepartReturns400`, `test_182_postOnDel_med_avtalepartReturns401` | binding/generic 400, authentication |
| approve agreement as participant | `POST /avtaler/{avtaleId}/godkjenn` | No — ambiguous shared-route attempt | No | `test_104_postOnGodkjennReturns400`, `test_105_postOnGodkjennReturns400`, `test_163_postOnGodkjennReturns401` | binding/generic 400, authentication, ambiguous shared-route outcome |
| approve agreement as employer | `POST /avtaler/{avtaleId}/godkjenn` | No — ambiguous shared-route attempt | No | `test_104_postOnGodkjennReturns400`, `test_105_postOnGodkjennReturns400`, `test_163_postOnGodkjennReturns401` | binding/generic 400, authentication, ambiguous shared-route outcome |
| sign mentor confidentiality declaration | `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring` | Yes | Yes | `test_103_postOnMentorGodkjennTaushetserkl_ringReturns400`, `test_134_postOnMentorGodkjennTaushetserkl_ringReturns400`, `test_161_postOnMentorGodkjennTaushetserkl_ringReturns401` | binding/generic 400, authentication |
| approve agreement as advisor | `POST /avtaler/{avtaleId}/godkjenn` | No — ambiguous shared-route attempt | No | `test_104_postOnGodkjennReturns400`, `test_105_postOnGodkjennReturns400`, `test_163_postOnGodkjennReturns401` | binding/generic 400, authentication, ambiguous shared-route outcome |
| approve on behalf of participant | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av` | Yes | Yes | `test_126_postOnGodkjenn_paa_vegne_avReturns400`, `test_167_postOnGodkjenn_paa_vegne_avReturns401` | binding/generic 400, authentication |
| approve on behalf of employer | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver` | Yes | Yes | `test_127_postOnGodkjenn_paa_vegne_av_arbeidsgiverReturns400`, `test_168_postOnGodkjenn_paa_vegne_av_arbeidsgiverReturns401` | binding/generic 400, authentication |
| approve on behalf of participant and employer | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` | Yes | Yes | `test_124_postOnGodkjenn_paa_vegne_av_deltaker_og_arbeidsgiverReturns400`, `test_165_postOnGodkjenn_paa_vegne_av_deltaker_og_arbeidsgiverReturns401` | binding/generic 400, authentication |
| revoke approvals | `POST /avtaler/{avtaleId}/opphev-godkjenninger` | Yes | Yes | `test_101_postOnOpphev_godkjenningerReturns400`, `test_159_postOnOpphev_godkjenningerReturns401` | binding/generic 400, authentication |
| mark agreement eligible for after-registration | `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` | No — ambiguous shared-route attempt | No | `test_99_postOnSet_om_avtalen_kan_etterregistreresReturns400`, `test_157_postOnSet_om_avtalen_kan_etterregistreresReturns401` | binding/generic 400, authentication, ambiguous shared-route outcome |
| remove after-registration eligibility | `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` | No — ambiguous shared-route attempt | No | `test_99_postOnSet_om_avtalen_kan_etterregistreresReturns400`, `test_157_postOnSet_om_avtalen_kan_etterregistreresReturns401` | binding/generic 400, authentication, ambiguous shared-route outcome |
| approve subsidy period | `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode` | Yes | Yes | `test_106_postOnGodkjenn_tilskuddsperiodeReturns400`, `test_164_postOnGodkjenn_tilskuddsperiodeReturns401` | binding/generic 400, authentication |
| reject subsidy period | `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode` | Yes | Yes | `test_117_postOnAvslag_tilskuddsperiodeReturns400`, `test_183_postOnAvslag_tilskuddsperiodeReturns401` | binding/generic 400, authentication |
| send rejected subsidy period back | `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter` | Yes | Yes | `test_100_postOnSend_tilbake_til_beslutterReturns400`, `test_158_postOnSend_tilbake_til_beslutterReturns401` | binding/generic 400, authentication |
| shorten agreement | `POST /avtaler/{avtaleId}/forkort` | Yes | Yes | `test_108_postOnForkortReturns400`, `test_137_postOnForkortReturns400`, `test_171_postOnForkortReturns401` | binding/generic 400, authentication |
| dry-run agreement shortening | `POST /avtaler/{avtaleId}/forkort-dry-run` | Yes | Yes | `test_109_postOnForkort_dry_runReturns400`, `test_135_postOnForkort_dry_runReturns400`, `test_172_postOnForkort_dry_runReturns401` | binding/generic 400, authentication |
| extend agreement | `POST /avtaler/{avtaleId}/forleng` | Yes | Yes | `test_128_postOnForlengReturns400`, `test_136_postOnForlengReturns400`, `test_169_postOnForlengReturns401` | binding/generic 400, authentication |
| dry-run agreement extension | `POST /avtaler/{avtaleId}/forleng-dry-run` | Yes | Yes | `test_107_postOnForleng_dry_runReturns400`, `test_170_postOnForleng_dry_runReturns401` | binding/generic 400, authentication |
| change subsidy calculation | `POST /avtaler/{avtaleId}/endre-tilskuddsberegning` | Yes | Yes | `test_110_postOnEndre_tilskuddsberegningReturns400`, `test_173_postOnEndre_tilskuddsberegningReturns401` | binding/generic 400, authentication |
| dry-run subsidy calculation change | `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run` | Yes | Yes | `test_129_postOnEndre_tilskuddsberegning_dry_runReturns400`, `test_174_postOnEndre_tilskuddsberegning_dry_runReturns401` | binding/generic 400, authentication |
| change contact information | `POST /avtaler/{avtaleId}/endre-kontaktinfo` | Yes | Yes | `test_114_postOnEndre_kontaktinfoReturns400`, `test_180_postOnEndre_kontaktinfoReturns401` | binding/generic 400, authentication |
| change job description | `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse` | Yes | Yes | `test_111_postOnEndre_stillingbeskrivelseReturns400`, `test_175_postOnEndre_stillingbeskrivelseReturns401` | binding/generic 400, authentication |
| change follow-up and adaptation text | `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging` | Yes | Yes | `test_112_postOnEndre_oppfolging_og_tilretteleggingReturns400`, `test_176_postOnEndre_oppfolging_og_tilretteleggingReturns401` | binding/generic 400, authentication |
| change work-training goals | `POST /avtaler/{avtaleId}/endre-maal` | Yes | Yes | `test_130_postOnEndre_maalReturns400`, `test_178_postOnEndre_maalReturns401` | binding/generic 400, authentication |
| change inclusion subsidy expenses | `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd` | Yes | Yes | `test_115_postOnEndre_inkluderingstilskuddReturns400`, `test_181_postOnEndre_inkluderingstilskuddReturns401` | binding/generic 400, authentication |
| change mentor details | `POST /avtaler/{avtaleId}/endre-om-mentor` | Yes | Yes | `test_113_postOnEndre_om_mentorReturns400`, `test_177_postOnEndre_om_mentorReturns401` | binding/generic 400, authentication |
| change cost center | `POST /avtaler/{avtaleId}/endre-kostnadssted` | Yes | Yes | `test_131_postOnEndre_kostnadsstedReturns400`, `test_179_postOnEndre_kostnadsstedReturns401` | binding/generic 400, authentication |
| adjust Arena migration date | `POST /avtaler/{avtaleId}/juster-arena-migreringsdato` | Yes | Yes | `test_123_postOnJuster_arena_migreringsdatoReturns400`, `test_162_postOnJuster_arena_migreringsdatoReturns401` | binding/generic 400, authentication |
| dry-run Arena migration date adjustment | `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run` | Yes | Yes | `test_187_postOnDry_runReturns400`, `test_194_postOnDry_runReturns401` | binding/generic 400, authentication |
| get employer account number | `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver` | Yes | Yes | `test_94_getOnKontonummer_arbeidsgiverReturns400`, `test_96_getOnKontonummer_arbeidsgiverReturns400`, `test_143_getOnKontonummer_arbeidsgiverReturns401` | binding/generic 400, authentication |
| download agreement PDF | `GET /avtaler/{avtaleId}/pdf` | Yes | Yes | `test_93_getOnPdfReturns400`, `test_142_getOnPdfReturns401` | binding/generic 400, authentication |
| check Salesforce dialog visibility | `GET /avtaler/{avtaleId}/vis-salesforce-dialog` | Yes | Yes | `test_91_getOnVis_salesforce_dialogReturns400`, `test_140_getOnVis_salesforce_dialogReturns401` | binding/generic 400, authentication |
| refresh follow-up unit | `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet` | Yes | Yes | `test_102_postOnOppdaterOppf_lgingsEnhetReturns400`, `test_160_postOnOppdaterOppf_lgingsEnhetReturns401` | binding/generic 400, authentication |
| take over agreement as advisor | `PUT /avtaler/{avtaleId}/overta` | Yes | Yes | `test_138_putOnOvertaReturns400`, `test_185_putOnOvertaReturns401` | binding/generic 400, authentication |
| annul agreement | `POST /avtaler/{avtaleId}/annuller` | Yes | Yes | `test_118_postOnAnnullerReturns400`, `test_133_postOnAnnullerReturns400`, `test_184_postOnAnnullerReturns401` | binding/generic 400, authentication |
| soft-delete agreement | `POST /avtaler/{avtaleId}/slettemerk` | Yes | Yes | `test_98_postOnSlettemerkReturns400`, `test_156_postOnSlettemerkReturns401` | binding/generic 400, authentication |
| list employer agreements | `GET /avtaler/min-side-arbeidsgiver` | Yes | Yes | `test_32_getOnMin_side_arbeidsgiverReturns400`, `test_43_getOnMin_side_arbeidsgiverReturns400`, `test_69_getOnMin_side_arbeidsgiverReturns401` | binding/generic 400, authentication, admission |
| list decision-maker agreements | `GET /avtaler/beslutter-liste` | Yes | Yes | `test_35_getOnBeslutter_listeReturns400`, `test_36_getOnBeslutter_listeReturns400`, `test_37_getOnBeslutter_listeReturns400` | binding/generic 400, authentication, admission |
| get logged-in user | `GET /innlogget-bruker` | Yes | Yes | `test_10_getOnInnlogget_brukerReturns400`, `test_17_getOnInnlogget_brukerReturns401` | binding/generic 400, authentication |
| look up organization | `GET /organisasjoner` | Yes | Yes | `test_9_getOnOrganisasjonerReturns400`, `test_13_getOnOrganisasjonerReturns400`, `test_16_getOnOrganisasjonerReturns401` | binding/generic 400, authentication |
| get Altinn rights request URLs | `GET /be-om-altinn-rettighet-urler` | Yes | Yes | `test_3_getOnBe_om_altinn_rettighet_urlerReturnsObject` | successful |
| get all code lists | `GET /kodeverk` | Yes | Yes | `test_1_getOnKodeverkReturnsObject` | successful |
| get status code list | `GET /kodeverk/statuser` | Yes | Yes | `test_26_getOnStatuserReturns7Elements` | successful |
| get measure type code list | `GET /kodeverk/tiltakstyper` | Yes | Yes | `test_23_getOnTiltakstyperReturns6Elements`, `test_25_getOnTiltakstyperReturns6Elements`, `test_29_getOnTiltakstyperReturns6Elements` | successful |
| evaluate feature toggles | `GET /feature` | Yes | Yes | `test_2_getOnFeatureReturnsObject`, `test_4_getOnFeatureReturnsObject` | successful |
| get feature variants | `GET /feature/variant` | Yes | Yes | `test_27_getOnFeatureVariantWithQueryParamsReturnsObject`, `test_28_getOnFeatureVariantWithQueryParamReturnsObject` | successful |
| run health check | `GET /internal/healthcheck` | Yes | Yes | `test_24_getOnHealthcheckReturnsContent` | successful |
| list overview notifications | `GET /varsler/oversikt` | Yes | Yes | `test_40_getOnOversiktReturns400`, `test_66_getOnOversiktReturns401` | binding/generic 400, authentication |
| list agreement modal notifications | `GET /varsler/avtale-modal` | Yes | Yes | `test_41_getOnAvtale_modalReturns400`, `test_67_getOnAvtale_modalReturns401` | binding/generic 400, authentication |
| list agreement notification log | `GET /varsler/avtale-logg` | Yes | Yes | `test_42_getOnAvtale_loggReturns400`, `test_68_getOnAvtale_loggReturns401` | binding/generic 400, authentication |
| mark notification as read | `POST /varsler/{varselId}/sett-til-lest` | Yes | Yes | `test_119_postOnSett_til_lestReturns400`, `test_145_postOnSett_til_lestReturns401` | binding/generic 400, authentication |
| mark multiple notifications as read | `POST /varsler/sett-alle-til-lest` | Yes | Yes | `test_52_postOnSett_alle_til_lestReturns400`, `test_73_postOnSett_alle_til_lestReturns401` | binding/generic 400, authentication |
| list unjournaled agreements | `GET /internal/avtaler` | Yes | Yes | `test_64_getOnInternalAvtalerWithQueryParamReturns401`, `test_72_getOnInternalAvtalerReturns401`, `test_83_getOnAvtalerReturns403` | authentication, admission |
| mark agreement versions as journaled | `PUT /internal/avtaler` | Yes | Yes | `test_62_putOnAvtalerReturns400`, `test_80_putOnAvtalerReturns401` | binding/generic 400, authentication |
| recalculate wage subsidy | `POST /utvikler-admin/reberegn` | Yes | Yes | `test_20_postOnReberegnCauses500_internalServerError`, `test_53_postOnReberegnReturns400`, `test_74_postOnReberegnReturns401` | binding/generic 400, authentication, infrastructure/unexpected 5xx |
| fix missing reduced-percent date | `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}` | Yes | Yes | `test_85_postOnReberegn_mangler_dato_for_redusert_prosCauses500_internalServerError`, `test_147_postOnReberegn_mangler_dato_for_redusert_prosReturns401` | authentication, infrastructure/unexpected 5xx |
| dry-run missing reduced-percent date fix | `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}` | Yes | Yes | `test_86_postOnReberegn_mangler_dato_for_redusert_prosent_dry_runCauses500_internalServerError`, `test_148_postOnReberegn_mangler_dato_for_redusert_prosent_dry_runReturns401` | authentication, infrastructure/unexpected 5xx |
| generate subsidy periods for agreement | `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}` | Yes | Yes | `test_188_postOnLag_tilskuddsperioder_for_en_avtalReturns400`, `test_191_postOnLag_tilskuddsperioder_for_en_avtalReturns401` | binding/generic 400, authentication |
| recalculate unhandled subsidy periods | `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}` | Yes | Yes | `test_120_postOnReberegn_ubehandlede_tilskuddsperiodReturns400`, `test_146_postOnReberegn_ubehandlede_tilskuddsperiodReturns401` | binding/generic 400, authentication |
| find subsidy period date-order problems | `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer` | Yes | Yes | `test_21_postOnFinn_avtaler_med_tilskuddsperioder_feil_datoerCauses500_internalServerError`, `test_75_postOnFinn_avtaler_med_tilskuddsperioder_feil_datoerReturns401` | authentication, infrastructure/unexpected 5xx |
| annul subsidy period | `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}` | Yes | Yes | `test_121_postOnAnnuller_tilskuddsperiodReturns400`, `test_153_postOnAnnuller_tilskuddsperiodReturns401` | binding/generic 400, authentication |
| annul and resend approved subsidy period | `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}` | Yes | Yes | `test_97_postOnAnnuller_og_resend_tilskuddsperiodReturns400`, `test_154_postOnAnnuller_og_resend_tilskuddsperiodReturns401` | binding/generic 400, authentication |
| annul and generate unhandled subsidy period | `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}` | Yes | Yes | `test_122_postOnAnnuller_og_generer_tilskuddsperiodReturns400`, `test_155_postOnAnnuller_og_generer_tilskuddsperiodReturns401` | binding/generic 400, authentication |
| annul and generate Arena-treated periods | `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}` | Yes | Yes | `test_190_postOnAnnuller_og_generer_behandlet_i_arena_periodReturns400`, `test_193_postOnAnnuller_og_generer_behandlet_i_arena_periodReturns401` | binding/generic 400, authentication |
| patch selected data warehouse messages | `POST /utvikler-admin/dvh-melding/patch` | Yes | Yes | `test_88_postOnPatchCauses500_internalServerError`, `test_132_postOnPatchReturns400`, `test_150_postOnPatchReturns401` | binding/generic 400, authentication, infrastructure/unexpected 5xx |
| patch all data warehouse messages | `POST /utvikler-admin/dvh-melding/patchalleavtaler` | Yes | Yes | `test_87_postOnPatchalleavtalerCauses500_internalServerError`, `test_149_postOnPatchalleavtalerReturns401` | authentication, infrastructure/unexpected 5xx |
| send event message for one agreement | `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}` | Yes | Yes | `test_189_postOnSend_melding_en_avtalReturns400`, `test_192_postOnSend_melding_en_avtalReturns401` | binding/generic 400, authentication |
| send event messages for all agreements | `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler` | Yes | Yes | `test_89_postOnSend_melding_alle_avtalerCauses500_internalServerError`, `test_151_postOnSend_melding_alle_avtalerReturns401` | authentication, infrastructure/unexpected 5xx |
| dry-run event messages for all agreements | `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler` | Yes | Yes | `test_90_postOnDry_send_melding_alle_avtalerCauses500_internalServerError`, `test_152_postOnDry_send_melding_alle_avtalerReturns401` | authentication, infrastructure/unexpected 5xx |

The three ambiguous families are: `POST /avtaler/opprett-mentor-avtale`, where `avtalerolle=VEILEDER` distinguishes the advisor variant but no call selects `ARBEIDSGIVER`; `POST /avtaler/{avtaleId}/godkjenn` without `innlogget-part`; and the after-registration toggle against no valid aggregate/pre-state. Both advisor-creation variants on `POST /avtaler` are distinguishable: generated tests explicitly send `ryddeavtale=false` and `ryddeavtale=true`.

## Behavior Details

### `B1`: `Role-scoped agreement listing`

- Business goal: Returns a paginated list of agreements visible to the logged-in party, filtered by the query fields.
- Starting point: Existing service state
- Expected business result: Returns a paginated list of agreements visible to the logged-in party, filtered by the query fields.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | list accessible agreements | `GET /avtaler` with query/path values documented by the endpoint; cookie `innlogget-part` when required to return the requested view without changing persisted state | Yes | No | No | `test_5_getOnAvtalerReturns400`, `test_6_getOnAvtalerReturns400`, `test_7_getOnAvtalerReturns400` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof.
- Recommended test IDs that close the gap: `B1-HP`.

### `B2`: `Advisor-created agreement`

- Business goal: Creates a new agreement for a participant and employer as a NAV advisor and returns a `Location` pointing to `/avtaler/{avtaleId}`.
- Starting point: No prior service state
- Expected business result: Creates a new agreement for a participant and employer as a NAV advisor and returns a `Location` pointing to `/avtaler/{avtaleId}`.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | create advisor agreement | `POST /avtaler` with body `OpprettAvtale` with `deltakerFnr`, `bedriftNr`, `tiltakstype`; query `ryddeavtale=false`; authenticated advisor caller to create the domain object and capture generated ids from the response | Yes | No | No | `test_0_postOnAvtalerCauses500_internalServerError`, `test_11_getOnAvtalerReturns400`, `test_12_getOnAvtalerReturns400` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| create advisor agreement | `IKKE_TILGANG_TIL_DELTAKER` | the advisor lacks write access to the concrete participant<br>**Why:** The candidate-scoped access service returns false before aggregate creation.<br>**Violated prerequisite:** The advisor must have write access to this participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkTilgangskontroll`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create advisor agreement | `TiltaksgjennomforingException: Deltakers fnr må være satt.` | the parsed creation body has no participant identity<br>**Why:** The aggregate constructor explicitly requires a participant before it can create domain state.<br>**Violated prerequisite:** `deltakerFnr` must identify the participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create advisor agreement | `TiltaksgjennomforingException: Arbeidsgivers bedriftnr må være satt.` | the parsed creation body has no employer business number<br>**Why:** The aggregate constructor explicitly requires a company before it can create domain state.<br>**Violated prerequisite:** `bedriftNr` must identify the employer business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create advisor agreement | `SOMMERJOBB_IKKE_GAMMEL_NOK` | the participant is under 16 years old<br>**Why:** The aggregate constructor rejects creation for a participant below the minimum age.<br>**Violated prerequisite:** Participants must be at least 16 years old. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create advisor agreement | `SOMMERJOBB_FOR_GAMMEL` | a summer-job participant is over 30 on 1 January<br>**Why:** The aggregate constructor applies the summer-job age ceiling before creation.<br>**Violated prerequisite:** A summer-job participant must satisfy the measure's age limit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create advisor agreement | `IKKE_TILGANG_TIL_DELTAKER` | PDL reports address protection code 6 for the participant<br>**Why:** The advisor creation path rejects protected participants after retrieving person data.<br>**Violated prerequisite:** This advisor flow may not create an agreement for a code-6-protected participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create advisor agreement | `HENTING_AV_INNSATS_BEHOV_FEILET` | Arena has no complete follow-up status for the participant<br>**Why:** The Arena business result is evaluated against the selected measure and rejects creation.<br>**Violated prerequisite:** Complete follow-up and qualification data must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create advisor agreement | `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET` | Arena returns an invalid qualification group<br>**Why:** The Arena business result is evaluated against the selected measure and rejects creation.<br>**Violated prerequisite:** The participant's qualification group must confer a recognized right. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create advisor agreement | `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNTILSKUDD_OG_SOMMERJOBB_FEIL` | the participant's qualification group is ineligible for temporary wage subsidy, summer job, or mentor<br>**Why:** The Arena business result is evaluated against the selected measure and rejects creation.<br>**Violated prerequisite:** The qualification group must be eligible for the selected measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create advisor agreement | `KVALIFISERINGSGRUPPE_VARIG_LONNTILSKUDD_FEIL` | the participant's qualification group is ineligible for permanent wage subsidy<br>**Why:** The Arena business result is evaluated against the selected measure and rejects creation.<br>**Violated prerequisite:** The qualification group must be eligible for permanent wage subsidy. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create advisor agreement | `EnhetErJuridiskException` | Ereg identifies `bedriftNr` as a legal entity rather than a business unit<br>**Why:** The organization result is rejected before the created agreement is saved.<br>**Violated prerequisite:** Agreements must target an operational business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create advisor agreement | `EnhetErOrganisasjonsleddException` | Ereg identifies `bedriftNr` as an organizational link<br>**Why:** The organization result is rejected before the created agreement is saved.<br>**Violated prerequisite:** Agreements must target a valid business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create advisor agreement | `EnhetFinnesIkkeException` | Ereg cannot find the requested business unit<br>**Why:** The organization result is rejected before the created agreement is saved.<br>**Violated prerequisite:** The employer business unit must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/13`
- Behavior outcome checklist summary: `0/14`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 13 concrete failure branches.
- Recommended test IDs that close the gap: `B2-HP`, `B2-F01`, `B2-F02`, `B2-F03`, `B2-F04`, `B2-F05`, `B2-F06`, `B2-F07`, `B2-F08`, `B2-F09`, `B2-F10`, `B2-F11`, `B2-F12`, `B2-F13`.

### `B3`: `Arena cleanup agreement creation`

- Business goal: Creates a new advisor agreement and also marks it as an Arena cleanup agreement.
- Starting point: No prior service state
- Expected business result: Creates a new advisor agreement and also marks it as an Arena cleanup agreement.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | create Arena cleanup agreement | `POST /avtaler` with body `OpprettAvtale` with `deltakerFnr`, `bedriftNr`, `tiltakstype`; query `ryddeavtale=true`; authenticated advisor caller to create the domain object and capture generated ids from the response | Yes | No | No | `test_12_getOnAvtalerReturns400` explicitly sends `ryddeavtale=true`, but its malformed/empty participant identity yields only a route attempt and no business result. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| create Arena cleanup agreement | `IKKE_TILGANG_TIL_DELTAKER` | the advisor lacks write access to the concrete participant<br>**Why:** The candidate-scoped access service returns false before aggregate creation.<br>**Violated prerequisite:** The advisor must have write access to this participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkTilgangskontroll`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create Arena cleanup agreement | `TiltaksgjennomforingException: Deltakers fnr må være satt.` | the parsed creation body has no participant identity<br>**Why:** The aggregate constructor explicitly requires a participant before it can create domain state.<br>**Violated prerequisite:** `deltakerFnr` must identify the participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create Arena cleanup agreement | `TiltaksgjennomforingException: Arbeidsgivers bedriftnr må være satt.` | the parsed creation body has no employer business number<br>**Why:** The aggregate constructor explicitly requires a company before it can create domain state.<br>**Violated prerequisite:** `bedriftNr` must identify the employer business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create Arena cleanup agreement | `SOMMERJOBB_IKKE_GAMMEL_NOK` | the participant is under 16 years old<br>**Why:** The aggregate constructor rejects creation for a participant below the minimum age.<br>**Violated prerequisite:** Participants must be at least 16 years old. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create Arena cleanup agreement | `SOMMERJOBB_FOR_GAMMEL` | a summer-job participant is over 30 on 1 January<br>**Why:** The aggregate constructor applies the summer-job age ceiling before creation.<br>**Violated prerequisite:** A summer-job participant must satisfy the measure's age limit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create Arena cleanup agreement | `IKKE_TILGANG_TIL_DELTAKER` | PDL reports address protection code 6 for the participant<br>**Why:** The advisor creation path rejects protected participants after retrieving person data.<br>**Violated prerequisite:** This advisor flow may not create an agreement for a code-6-protected participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create Arena cleanup agreement | `HENTING_AV_INNSATS_BEHOV_FEILET` | Arena has no complete follow-up status for the participant<br>**Why:** The Arena business result is evaluated against the selected measure and rejects creation.<br>**Violated prerequisite:** Complete follow-up and qualification data must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create Arena cleanup agreement | `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET` | Arena returns an invalid qualification group<br>**Why:** The Arena business result is evaluated against the selected measure and rejects creation.<br>**Violated prerequisite:** The participant's qualification group must confer a recognized right. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create Arena cleanup agreement | `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNTILSKUDD_OG_SOMMERJOBB_FEIL` | the participant's qualification group is ineligible for temporary wage subsidy, summer job, or mentor<br>**Why:** The Arena business result is evaluated against the selected measure and rejects creation.<br>**Violated prerequisite:** The qualification group must be eligible for the selected measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create Arena cleanup agreement | `KVALIFISERINGSGRUPPE_VARIG_LONNTILSKUDD_FEIL` | the participant's qualification group is ineligible for permanent wage subsidy<br>**Why:** The Arena business result is evaluated against the selected measure and rejects creation.<br>**Violated prerequisite:** The qualification group must be eligible for permanent wage subsidy. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create Arena cleanup agreement | `EnhetErJuridiskException` | Ereg identifies `bedriftNr` as a legal entity rather than a business unit<br>**Why:** The organization result is rejected before the agreement and any cleanup marker are saved.<br>**Violated prerequisite:** Agreements must target an operational business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create Arena cleanup agreement | `EnhetErOrganisasjonsleddException` | Ereg identifies `bedriftNr` as an organizational link<br>**Why:** The organization result is rejected before the agreement and any cleanup marker are saved.<br>**Violated prerequisite:** Agreements must target a valid business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create Arena cleanup agreement | `EnhetFinnesIkkeException` | Ereg cannot find the requested business unit<br>**Why:** The organization result is rejected before the agreement and any cleanup marker are saved.<br>**Violated prerequisite:** The employer business unit must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/13`
- Behavior outcome checklist summary: `0/14`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 13 concrete failure branches.
- Recommended test IDs that close the gap: `B3-HP`, `B3-F01`, `B3-F02`, `B3-F03`, `B3-F04`, `B3-F05`, `B3-F06`, `B3-F07`, `B3-F08`, `B3-F09`, `B3-F10`, `B3-F11`, `B3-F12`, `B3-F13`.

### `B4`: `Employer-created agreement`

- Business goal: Creates a new agreement as an employer and returns a `Location` for `/avtaler/{avtaleId}`.
- Starting point: No prior service state
- Expected business result: Creates a new agreement as an employer and returns a `Location` for `/avtaler/{avtaleId}`.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | create employer agreement | `POST /avtaler/opprett-som-arbeidsgiver` with body `OpprettAvtale` with `deltakerFnr`, `bedriftNr`, `tiltakstype`; authenticated employer caller with Altinn rights to create the domain object and capture generated ids from the response | Yes | No | No | `test_54_postOnOpprett_som_arbeidsgiverReturns400`, `test_55_postOnOpprett_som_arbeidsgiverReturns400`, `test_77_postOnOpprett_som_arbeidsgiverReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| create employer agreement | `TilgangskontrollException` | the employer lacks an Altinn right for the requested company and measure<br>**Why:** The employer's persisted/external business-right relationship does not cover the selected company and measure.<br>**Violated prerequisite:** The employer must have the measure-specific Altinn right for the company. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.tilgangTilBedriftVedOpprettelseAvAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create employer agreement | `TiltaksgjennomforingException: Deltakers fnr må være satt.` | the parsed creation body has no participant identity<br>**Why:** The aggregate constructor explicitly requires a participant before it can create domain state.<br>**Violated prerequisite:** `deltakerFnr` must identify the participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create employer agreement | `TiltaksgjennomforingException: Arbeidsgivers bedriftnr må være satt.` | the parsed creation body has no employer business number<br>**Why:** The aggregate constructor explicitly requires a company before it can create domain state.<br>**Violated prerequisite:** `bedriftNr` must identify the employer business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create employer agreement | `SOMMERJOBB_IKKE_GAMMEL_NOK` | the participant is under 16 years old<br>**Why:** The aggregate constructor rejects creation for a participant below the minimum age.<br>**Violated prerequisite:** Participants must be at least 16 years old. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create employer agreement | `SOMMERJOBB_FOR_GAMMEL` | a summer-job participant is over 30 on 1 January<br>**Why:** The aggregate constructor applies the summer-job age ceiling before creation.<br>**Violated prerequisite:** A summer-job participant must satisfy the measure's age limit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/5`
- Behavior outcome checklist summary: `0/6`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 5 concrete failure branches.
- Recommended test IDs that close the gap: `B4-HP`, `B4-F01`, `B4-F02`, `B4-F03`, `B4-F04`, `B4-F05`.

### `B5`: `Advisor-created mentor agreement`

- Business goal: Creates a mentor agreement where the advisor is the creator.
- Starting point: No prior service state
- Expected business result: Creates a mentor agreement where the advisor is the creator.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | create mentor agreement as advisor | `POST /avtaler/opprett-mentor-avtale` with body `OpprettMentorAvtale` with `deltakerFnr`, `mentorFnr`, `bedriftNr`, `tiltakstype=MENTOR`, `avtalerolle=VEILEDER`; authenticated advisor caller to create the domain object and capture generated ids from the response | Yes | No | No | `test_22_postOnOpprett_mentor_avtaleCauses500_internalServerError` explicitly sends `avtalerolle=VEILEDER`, but malformed/missing identities prevent a context-valid operation. | No precise JaCoCo evidence proves successful admission and the documented operation. `AvtaleController.opprettMentorAvtale` has only 2/66 instructions, 1/13 lines, and 0/8 branches covered. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| create mentor agreement as advisor | `DELTAGER_OG_MENTOR_KAN_IKKE_HA_SAMME_FØDSELSNUMMER` | the participant and mentor have the same national identity number<br>**Why:** The controller rejects a person serving as both participant and mentor in the agreement.<br>**Violated prerequisite:** Participant and mentor must be different people. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.opprettMentorAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as advisor | `IKKE_TILGANG_TIL_DELTAKER` | the advisor lacks write access to the concrete participant<br>**Why:** The candidate-scoped access service returns false before aggregate creation.<br>**Violated prerequisite:** The advisor must have write access to this participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkTilgangskontroll`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as advisor | `TiltaksgjennomforingException: Deltakers fnr må være satt.` | the parsed creation body has no participant identity<br>**Why:** The aggregate constructor explicitly requires a participant before it can create domain state.<br>**Violated prerequisite:** `deltakerFnr` must identify the participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as advisor | `TiltaksgjennomforingException: Arbeidsgivers bedriftnr må være satt.` | the parsed creation body has no employer business number<br>**Why:** The aggregate constructor explicitly requires a company before it can create domain state.<br>**Violated prerequisite:** `bedriftNr` must identify the employer business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as advisor | `SOMMERJOBB_IKKE_GAMMEL_NOK` | the participant is under 16 years old<br>**Why:** The aggregate constructor rejects creation for a participant below the minimum age.<br>**Violated prerequisite:** Participants must be at least 16 years old. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as advisor | `SOMMERJOBB_FOR_GAMMEL` | a summer-job participant is over 30 on 1 January<br>**Why:** The aggregate constructor applies the summer-job age ceiling before creation.<br>**Violated prerequisite:** A summer-job participant must satisfy the measure's age limit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as advisor | `IKKE_TILGANG_TIL_DELTAKER` | PDL reports address protection code 6 for the participant<br>**Why:** The advisor creation path rejects protected participants after retrieving person data.<br>**Violated prerequisite:** This advisor flow may not create an agreement for a code-6-protected participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as advisor | `HENTING_AV_INNSATS_BEHOV_FEILET` | Arena has no complete follow-up status for the participant<br>**Why:** The Arena business result is evaluated against the selected measure and rejects creation.<br>**Violated prerequisite:** Complete follow-up and qualification data must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as advisor | `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET` | Arena returns an invalid qualification group<br>**Why:** The Arena business result is evaluated against the selected measure and rejects creation.<br>**Violated prerequisite:** The participant's qualification group must confer a recognized right. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as advisor | `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNTILSKUDD_OG_SOMMERJOBB_FEIL` | the participant's qualification group is ineligible for temporary wage subsidy, summer job, or mentor<br>**Why:** The Arena business result is evaluated against the selected measure and rejects creation.<br>**Violated prerequisite:** The qualification group must be eligible for the selected measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as advisor | `KVALIFISERINGSGRUPPE_VARIG_LONNTILSKUDD_FEIL` | the participant's qualification group is ineligible for permanent wage subsidy<br>**Why:** The Arena business result is evaluated against the selected measure and rejects creation.<br>**Violated prerequisite:** The qualification group must be eligible for permanent wage subsidy. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as advisor | `EnhetErJuridiskException` | Ereg identifies `bedriftNr` as a legal entity rather than a business unit<br>**Why:** The organization result is rejected before the agreement and any cleanup marker are saved.<br>**Violated prerequisite:** Agreements must target an operational business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as advisor | `EnhetErOrganisasjonsleddException` | Ereg identifies `bedriftNr` as an organizational link<br>**Why:** The organization result is rejected before the agreement and any cleanup marker are saved.<br>**Violated prerequisite:** Agreements must target a valid business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as advisor | `EnhetFinnesIkkeException` | Ereg cannot find the requested business unit<br>**Why:** The organization result is rejected before the agreement and any cleanup marker are saved.<br>**Violated prerequisite:** The employer business unit must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/14`
- Behavior outcome checklist summary: `0/15`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 14 concrete failure branches.
- Recommended test IDs that close the gap: `B5-HP`, `B5-F01`, `B5-F02`, `B5-F03`, `B5-F04`, `B5-F05`, `B5-F06`, `B5-F07`, `B5-F08`, `B5-F09`, `B5-F10`, `B5-F11`, `B5-F12`, `B5-F13`, `B5-F14`.

### `B6`: `Employer-created mentor agreement`

- Business goal: Creates a mentor agreement where the employer is the creator.
- Starting point: No prior service state
- Expected business result: Creates a mentor agreement where the employer is the creator.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | create mentor agreement as employer | `POST /avtaler/opprett-mentor-avtale` with body `OpprettMentorAvtale` with `deltakerFnr`, `mentorFnr`, `bedriftNr`, `tiltakstype=MENTOR`, `avtalerolle=ARBEIDSGIVER`; authenticated employer caller with Altinn rights to create the domain object and capture generated ids from the response | No — ambiguous | No | No | `test_22_postOnOpprett_mentor_avtaleCauses500_internalServerError`, `test_48_postOnOpprett_mentor_avtaleReturns400`, `test_78_postOnOpprett_mentor_avtaleReturns401` calls the shared route but lacks the documented discriminator/actor/pre-state; exact-function credit is withheld. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| create mentor agreement as employer | `DELTAGER_OG_MENTOR_KAN_IKKE_HA_SAMME_FØDSELSNUMMER` | the participant and mentor have the same national identity number<br>**Why:** The controller rejects a person serving as both participant and mentor in the agreement.<br>**Violated prerequisite:** Participant and mentor must be different people. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.opprettMentorAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as employer | `TilgangskontrollException` | the employer lacks an Altinn right for the requested company and measure<br>**Why:** The employer's persisted/external business-right relationship does not cover the selected company and measure.<br>**Violated prerequisite:** The employer must have the measure-specific Altinn right for the company. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.tilgangTilBedriftVedOpprettelseAvAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as employer | `TiltaksgjennomforingException: Deltakers fnr må være satt.` | the parsed creation body has no participant identity<br>**Why:** The aggregate constructor explicitly requires a participant before it can create domain state.<br>**Violated prerequisite:** `deltakerFnr` must identify the participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as employer | `TiltaksgjennomforingException: Arbeidsgivers bedriftnr må være satt.` | the parsed creation body has no employer business number<br>**Why:** The aggregate constructor explicitly requires a company before it can create domain state.<br>**Violated prerequisite:** `bedriftNr` must identify the employer business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as employer | `SOMMERJOBB_IKKE_GAMMEL_NOK` | the participant is under 16 years old<br>**Why:** The aggregate constructor rejects creation for a participant below the minimum age.<br>**Violated prerequisite:** Participants must be at least 16 years old. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as employer | `SOMMERJOBB_FOR_GAMMEL` | a summer-job participant is over 30 on 1 January<br>**Why:** The aggregate constructor applies the summer-job age ceiling before creation.<br>**Violated prerequisite:** A summer-job participant must satisfy the measure's age limit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale(OpprettAvtale/OpprettMentorAvtale)`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as employer | `EnhetErJuridiskException` | Ereg identifies `bedriftNr` as a legal entity rather than a business unit<br>**Why:** The organization result is rejected before the agreement and any cleanup marker are saved.<br>**Violated prerequisite:** Agreements must target an operational business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as employer | `EnhetErOrganisasjonsleddException` | Ereg identifies `bedriftNr` as an organizational link<br>**Why:** The organization result is rejected before the agreement and any cleanup marker are saved.<br>**Violated prerequisite:** Agreements must target a valid business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| create mentor agreement as employer | `EnhetFinnesIkkeException` | Ereg cannot find the requested business unit<br>**Why:** The organization result is rejected before the agreement and any cleanup marker are saved.<br>**Violated prerequisite:** The employer business unit must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `0/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/9`
- Behavior outcome checklist summary: `0/10`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: exact shared-route discrimination; context-valid required workflow and terminal-state proof; all 9 concrete failure branches.
- Recommended test IDs that close the gap: `B6-HP`, `B6-F01`, `B6-F02`, `B6-F03`, `B6-F04`, `B6-F05`, `B6-F06`, `B6-F07`, `B6-F08`, `B6-F09`.

### `B7`: `Check participant overlap`

- Business goal: Returns existing agreements that overlap the participant, measure type, and optional period.
- Starting point: Existing service state
- Expected business result: Returns existing agreements that overlap the participant, measure type, and optional period.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | check participant overlap | `GET /avtaler/deltaker-allerede-paa-tiltak` with query `deltakerFnr`, `tiltakstype`, optional `startDato`, `sluttDato`, `avtaleId`; advisor caller to return the requested view without changing persisted state | Yes | No | No | `test_33_getOnDeltaker_allerede_paa_tiltakReturns400`, `test_34_getOnDeltaker_allerede_paa_tiltakReturns400`, `test_45_getOnDeltaker_allerede_paa_tiltakReturns400` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof.
- Recommended test IDs that close the gap: `B7-HP`.

### `B8`: `Saved agreement search registration and replay`

- Business goal: Persist a search filter and replay it later through its generated search id.
- Starting point: Existing service state
- Expected business result: Looks up a saved search by `sokId`, reruns it, increments usage, and returns the same response shape as search.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | search agreements and save search | `POST /avtaler/sok` with body `AvtalePredicate`; cookie `innlogget-part`; optional `sorteringskolonne`, `page`, `size` to save the search filter and capture `sokId` | Yes | No | No | `test_47_postOnSokReturns400`, `test_49_postOnSokReturns400`, `test_50_postOnSokReturns400` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |
| 2 | replay saved agreement search | `GET /avtaler/sok` with query `sokId` returned by `search agreements and save search`; same cookie `innlogget-part`; optional paging and sorting to replay the saved filter through the captured `sokId` | Yes | No | No | `test_38_getOnSokReturns400`, `test_44_getOnSokReturns400`, `test_63_getOnSokReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `2/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof.
- Recommended test IDs that close the gap: `B8-HP`.

### `B9`: `Agreement detail retrieval by id`

- Business goal: Retrieves the agreement identified by `{avtaleId}` for the selected logged-in party.
- Starting point: Existing service state
- Expected business result: Retrieves the agreement identified by `{avtaleId}` for the selected logged-in party.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to return the requested view without changing persisted state | Yes | No | No | `test_39_getOnAvtalReturns400`, `test_65_getOnAvtalReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| retrieve agreement by id | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.hentAvtale/hentAvtaleFraAvtaleNr/hentAvtaleVersjoner`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| retrieve agreement by id | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| retrieve agreement by id | `IKKE_TILGANG_TIL_AVTALE` | a mentor owns the agreement but has not signed the confidentiality declaration<br>**Why:** Mentor detail retrieval applies an additional object-state gate after ownership succeeds.<br>**Violated prerequisite:** The mentor must sign the confidentiality declaration before viewing agreement details. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Mentor.java — Mentor.hentAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 3 concrete failure branches.
- Recommended test IDs that close the gap: `B9-HP`, `B9-F01`, `B9-F02`, `B9-F03`.

### `B10`: `Agreement detail retrieval by agreement number`

- Business goal: Retrieves an agreement by its generated agreement number.
- Starting point: Existing service state
- Expected business result: Retrieves an agreement by its generated agreement number.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | retrieve agreement by agreement number | `GET /avtaler/avtaleNr/{avtaleNr}` with query/path values documented by the endpoint; cookie `innlogget-part` when required to return the requested view without changing persisted state | Yes | No | No | `test_95_getOnAvtaleNrReturns400`, `test_144_getOnAvtaleNrReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| retrieve agreement by agreement number | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.hentAvtale/hentAvtaleFraAvtaleNr/hentAvtaleVersjoner`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| retrieve agreement by agreement number | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 2 concrete failure branches.
- Recommended test IDs that close the gap: `B10-HP`, `B10-F01`, `B10-F02`.

### `B11`: `Agreement version history retrieval`

- Business goal: Returns all stored content versions for an agreement.
- Starting point: Existing service state
- Expected business result: Returns all stored content versions for an agreement.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | list agreement versions | `GET /avtaler/{avtaleId}/versjoner` with path `avtaleId`; authenticated caller with required role and access to return the requested view without changing persisted state | Yes | No | No | `test_92_getOnVersjonerReturns400`, `test_141_getOnVersjonerReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| list agreement versions | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.hentAvtale/hentAvtaleFraAvtaleNr/hentAvtaleVersjoner`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| list agreement versions | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 2 concrete failure branches.
- Recommended test IDs that close the gap: `B11-HP`, `B11-F01`, `B11-F02`.

### `B12`: `Draft agreement update`

- Business goal: Updates mutable agreement content and, for subsidy-backed measures, recalculates subsidy periods.
- Starting point: Existing service state
- Expected business result: Updates mutable agreement content and, for subsidy-backed measures, recalculates subsidy periods.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | update agreement | `PUT /avtaler/{avtaleId}` with path `avtaleId`; header `If-Unmodified-Since` from current `sistEndret`; body `EndreAvtale`; cookie `innlogget-part` with edit rights to complete the domain transition | Yes | No | No | `test_56_putOnAvtalReturns400`, `test_57_putOnAvtalReturns400`, `test_58_putOnAvtalReturns400` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B12. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| update agreement | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `KAN_IKKE_ENDRE` | the concrete agreement party role is not permitted to edit agreement content<br>**Why:** The role implementation reports that it cannot perform agreement edits.<br>**Violated prerequisite:** The party must be an editing-capable advisor or employer. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.endreAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `VARIGHET_DATO_TILBAKE_I_TID` | an employer edits an unassigned agreement with a start date before today<br>**Why:** Employer editing of an unassigned agreement rejects a past start date.<br>**Violated prerequisite:** The unassigned employer-created agreement must start today or later. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.avvisDatoerTilbakeITid`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `VARIGHET_DATO_TILBAKE_I_TID` | an employer edits an unassigned agreement with an end date before today<br>**Why:** Employer editing of an unassigned agreement rejects a past end date.<br>**Violated prerequisite:** The unassigned employer-created agreement must end today or later. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.avvisDatoerTilbakeITid`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `TilgangskontrollException` | participant, employer, or advisor approval already exists<br>**Why:** Content is locked until all approvals are revoked.<br>**Violated prerequisite:** No party approval may exist when draft content is edited. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAvtalenKanEndres`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `SAMTIDIGE_ENDRINGER` | the supplied concurrency timestamp is absent or older than `sistEndret`<br>**Why:** The aggregate rejects a stale edit.<br>**Violated prerequisite:** The edit must target the latest version. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `START_ETTER_SLUTT` | start date is after end date<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Start date must not follow end date. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `FORTIDLIG_STARTDATO` | an unentered agreement not approved for after-registration starts more than seven days in the past<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Past starts beyond the grace period require after-registration approval. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `SLUTTDATO_GRENSE_NÅDD` | end date is after 2089-12-31<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** The domain upper end-date bound is 2089-12-31. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `VARIGHET_FOR_LANG_ARBEIDSTRENING` | work training exceeds 18 months<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Work training may last at most 18 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/ArbeidstreningStartOgSluttDatoStrategy.java — ArbeidstreningStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `VARIGHET_FOR_LANG_INKLUDERINGSTILSKUDD` | inclusion subsidy exceeds 12 months<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Inclusion subsidy may not extend beyond its 12-month limit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/InkluderingstilskuddStartOgSluttDatoStrategy.java — InkluderingstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `VARIGHET_FOR_LANG_MENTOR_36_MND` | mentor duration exceeds 36 months for specially or permanently adapted effort<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Eligible adapted-effort mentor agreements are limited to 36 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MentorStartOgSluttDatoStrategy.java — MentorStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `VARIGHET_FOR_LANG_MENTOR_6_MND` | mentor duration exceeds 6 months for other qualification groups<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Other mentor agreements are limited to 6 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MentorStartOgSluttDatoStrategy.java — MentorStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `VARIGHET_FOR_LANG_MIDLERTIDIG_LONNSTILSKUDD_24_MND` | temporary wage subsidy exceeds 24 months for specially or permanently adapted effort<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** The adapted-effort maximum is 24 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MidlertidigLonnstilskuddStartOgSluttDatoStrategy.java — MidlertidigLonnstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `VARIGHET_FOR_LANG_MIDLERTIDIG_LONNSTILSKUDD_12_MND` | temporary wage subsidy exceeds 12 months for situational or missing qualification group<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** The applicable maximum is 12 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MidlertidigLonnstilskuddStartOgSluttDatoStrategy.java — MidlertidigLonnstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `SOMMERJOBB_FOR_TIDLIG` | summer-job start or end is before its permitted summer window<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Summer-job dates must fall within the source-defined summer window. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `SOMMERJOBB_FOR_SENT` | summer-job start is after 31 August or end is after 27 September<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Summer-job dates must remain inside the source-defined latest bounds. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `SOMMERJOBB_FOR_LANG_VARIGHET` | summer job lasts longer than four weeks<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Summer-job duration may not exceed four weeks minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `FEIL_OTP_SATS` | the occupational-pension rate is below 0.0 or above 0.3<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** The wage-subsidy calculation restricts the pension rate to the implemented interval. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/LonnstilskuddStrategy.java — LonnstilskuddStrategy.endre`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `LONNSTILSKUDD_PROSENT_ER_UGYLDIG` | the wage-subsidy percentage is outside the measure-specific allowed values or range<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** The selected wage-subsidy strategy rejects its percentage. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/MidlertidigLonnstilskuddStrategy.java — MidlertidigLonnstilskuddStrategy.endre; src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/VarigLonnstilskuddStrategy.java — VarigLonnstilskuddStrategy.endre; src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/SommerjobbStrategy.java — SommerjobbStrategy.endre`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `INKLUDERINGSTILSKUDD_SUM_FOR_HØY` | the persisted inclusion-subsidy expense total already exceeds 136700 when the update enters the strategy<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** The inclusion strategy enforces the maximum against the current content before copying the proposed list. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/InkluderingstilskuddStrategy.java — InkluderingstilskuddStrategy.sjekkTotalBeløp`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `TiltaksgjennomforingException: Maks lengde for mål er 1000 tegn` | a work-training goal description exceeds 1000 characters<br>**Why:** The work-training content strategy validates each goal description before copying it into the agreement.<br>**Violated prerequisite:** Every work-training goal description must be at most 1000 characters. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Maal.java — Maal.sjekkMaalLengde`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| update agreement | `IKKE_TILGANG_TIL_DELTAKER` | the advisor refresh reaches a participant with address protection code 6<br>**Why:** The advisor update path rejects the protected participant while refreshing person data.<br>**Violated prerequisite:** The advisor may not update this code-6-protected participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.oppdaterePersondataFraPdlVedEndreAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/25`
- Behavior outcome checklist summary: `0/26`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 25 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B12-HP`, `B12-F01`, `B12-F02`, `B12-F03`, `B12-F04`, `B12-F05`, `B12-F06`, `B12-F07`, `B12-F08`, `B12-F09`, `B12-F10`, `B12-F11`, `B12-F12`, `B12-F13`, `B12-F14`, `B12-F15`, `B12-F16`, `B12-F17`, `B12-F18`, `B12-F19`, `B12-F20`, `B12-F21`, `B12-F22`, `B12-F23`, `B12-F24`, `B12-F25`, `B12-V01`.

### `B13`: `Draft agreement update dry-run`

- Business goal: Validates and returns the would-be updated agreement without saving it.
- Starting point: Existing service state
- Expected business result: Validates and returns the would-be updated agreement without saving it.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | dry-run agreement update | `PUT /avtaler/{avtaleId}/dry-run` with path `avtaleId`; header `If-Unmodified-Since`; body `EndreAvtale`; cookie `innlogget-part` with edit rights to validate and preview the transition without saving it | Yes | No | No | `test_139_putOnDry_runReturns400`, `test_186_putOnDry_runReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| dry-run agreement update | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `KAN_IKKE_ENDRE` | the concrete agreement party role is not permitted to edit agreement content<br>**Why:** The role implementation reports that it cannot perform agreement edits.<br>**Violated prerequisite:** The party must be an editing-capable advisor or employer. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.endreAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `VARIGHET_DATO_TILBAKE_I_TID` | an employer edits an unassigned agreement with a start date before today<br>**Why:** Employer editing of an unassigned agreement rejects a past start date.<br>**Violated prerequisite:** The unassigned employer-created agreement must start today or later. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.avvisDatoerTilbakeITid`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `VARIGHET_DATO_TILBAKE_I_TID` | an employer edits an unassigned agreement with an end date before today<br>**Why:** Employer editing of an unassigned agreement rejects a past end date.<br>**Violated prerequisite:** The unassigned employer-created agreement must end today or later. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.avvisDatoerTilbakeITid`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `TilgangskontrollException` | participant, employer, or advisor approval already exists<br>**Why:** Content is locked until all approvals are revoked.<br>**Violated prerequisite:** No party approval may exist when draft content is edited. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAvtalenKanEndres`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `SAMTIDIGE_ENDRINGER` | the supplied concurrency timestamp is absent or older than `sistEndret`<br>**Why:** The aggregate rejects a stale edit.<br>**Violated prerequisite:** The edit must target the latest version. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `START_ETTER_SLUTT` | start date is after end date<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Start date must not follow end date. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `FORTIDLIG_STARTDATO` | an unentered agreement not approved for after-registration starts more than seven days in the past<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Past starts beyond the grace period require after-registration approval. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `SLUTTDATO_GRENSE_NÅDD` | end date is after 2089-12-31<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** The domain upper end-date bound is 2089-12-31. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `VARIGHET_FOR_LANG_ARBEIDSTRENING` | work training exceeds 18 months<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Work training may last at most 18 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/ArbeidstreningStartOgSluttDatoStrategy.java — ArbeidstreningStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `VARIGHET_FOR_LANG_INKLUDERINGSTILSKUDD` | inclusion subsidy exceeds 12 months<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Inclusion subsidy may not extend beyond its 12-month limit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/InkluderingstilskuddStartOgSluttDatoStrategy.java — InkluderingstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `VARIGHET_FOR_LANG_MENTOR_36_MND` | mentor duration exceeds 36 months for specially or permanently adapted effort<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Eligible adapted-effort mentor agreements are limited to 36 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MentorStartOgSluttDatoStrategy.java — MentorStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `VARIGHET_FOR_LANG_MENTOR_6_MND` | mentor duration exceeds 6 months for other qualification groups<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Other mentor agreements are limited to 6 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MentorStartOgSluttDatoStrategy.java — MentorStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `VARIGHET_FOR_LANG_MIDLERTIDIG_LONNSTILSKUDD_24_MND` | temporary wage subsidy exceeds 24 months for specially or permanently adapted effort<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** The adapted-effort maximum is 24 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MidlertidigLonnstilskuddStartOgSluttDatoStrategy.java — MidlertidigLonnstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `VARIGHET_FOR_LANG_MIDLERTIDIG_LONNSTILSKUDD_12_MND` | temporary wage subsidy exceeds 12 months for situational or missing qualification group<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** The applicable maximum is 12 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MidlertidigLonnstilskuddStartOgSluttDatoStrategy.java — MidlertidigLonnstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `SOMMERJOBB_FOR_TIDLIG` | summer-job start or end is before its permitted summer window<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Summer-job dates must fall within the source-defined summer window. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `SOMMERJOBB_FOR_SENT` | summer-job start is after 31 August or end is after 27 September<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Summer-job dates must remain inside the source-defined latest bounds. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `SOMMERJOBB_FOR_LANG_VARIGHET` | summer job lasts longer than four weeks<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** Summer-job duration may not exceed four weeks minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `FEIL_OTP_SATS` | the occupational-pension rate is below 0.0 or above 0.3<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** The wage-subsidy calculation restricts the pension rate to the implemented interval. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/LonnstilskuddStrategy.java — LonnstilskuddStrategy.endre`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `LONNSTILSKUDD_PROSENT_ER_UGYLDIG` | the wage-subsidy percentage is outside the measure-specific allowed values or range<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** The selected wage-subsidy strategy rejects its percentage. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/MidlertidigLonnstilskuddStrategy.java — MidlertidigLonnstilskuddStrategy.endre; src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/VarigLonnstilskuddStrategy.java — VarigLonnstilskuddStrategy.endre; src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/SommerjobbStrategy.java — SommerjobbStrategy.endre`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `INKLUDERINGSTILSKUDD_SUM_FOR_HØY` | the persisted inclusion-subsidy expense total already exceeds 136700 when the update enters the strategy<br>**Why:** The reachable measure-specific content or date strategy rejects the agreement state.<br>**Violated prerequisite:** The inclusion strategy enforces the maximum against the current content before copying the proposed list. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/InkluderingstilskuddStrategy.java — InkluderingstilskuddStrategy.sjekkTotalBeløp`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `TiltaksgjennomforingException: Maks lengde for mål er 1000 tegn` | a work-training goal description exceeds 1000 characters<br>**Why:** The work-training content strategy validates each goal description before copying it into the agreement.<br>**Violated prerequisite:** Every work-training goal description must be at most 1000 characters. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Maal.java — Maal.sjekkMaalLengde`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement update | `IKKE_TILGANG_TIL_DELTAKER` | the advisor refresh reaches a participant with address protection code 6<br>**Why:** The advisor update path rejects the protected participant while refreshing person data.<br>**Violated prerequisite:** The advisor may not update this code-6-protected participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.oppdaterePersondataFraPdlVedEndreAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/25`
- Behavior outcome checklist summary: `0/26`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 25 concrete failure branches.
- Recommended test IDs that close the gap: `B13-HP`, `B13-F01`, `B13-F02`, `B13-F03`, `B13-F04`, `B13-F05`, `B13-F06`, `B13-F07`, `B13-F08`, `B13-F09`, `B13-F10`, `B13-F11`, `B13-F12`, `B13-F13`, `B13-F14`, `B13-F15`, `B13-F16`, `B13-F17`, `B13-F18`, `B13-F19`, `B13-F20`, `B13-F21`, `B13-F22`, `B13-F23`, `B13-F24`, `B13-F25`.

### `B14`: `Agreement sharing with a party`

- Business goal: Registers a share event for the selected agreement party and creates corresponding notifications.
- Starting point: Existing service state
- Expected business result: Registers a share event for the selected agreement party and creates corresponding notifications.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | share agreement with party | `POST /avtaler/{avtaleId}/del-med-avtalepart` with path `avtaleId`; body `Avtalerolle` for the receiver; advisor caller to complete the domain transition | Yes | No | No | `test_116_postOnDel_med_avtalepartReturns400`, `test_182_postOnDel_med_avtalepartReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B14. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| share agreement with party | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| share agreement with party | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| share agreement with party | `UGYLDIG_TLF` | the selected participant, employer, advisor, or mentor has no valid mobile number<br>**Why:** The aggregate validates the selected party's persisted phone number before registering the share event.<br>**Violated prerequisite:** The selected agreement party must have a valid mobile number. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.delMedAvtalepart`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 3 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B14-HP`, `B14-F01`, `B14-F02`, `B14-F03`, `B14-V01`.

### `B15`: `Participant agreement approval`

- Business goal: Records participant approval on a fully filled agreement.
- Starting point: Existing service state
- Expected business result: Records participant approval on a fully filled agreement.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | approve agreement as participant | `POST /avtaler/{avtaleId}/godkjenn` with path `avtaleId`; header `If-Unmodified-Since`; cookie `innlogget-part=DELTAKER` to complete the domain transition | No — ambiguous | No | No | `test_104_postOnGodkjennReturns400`, `test_105_postOnGodkjennReturns400`, `test_163_postOnGodkjennReturns401` calls the shared route but lacks the documented discriminator/actor/pre-state; exact-function credit is withheld. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B15. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| approve agreement as participant | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as participant | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as participant | `SAMTIDIGE_ENDRINGER` | the supplied concurrency timestamp is absent or older than the agreement's current `sistEndret`<br>**Why:** The aggregate refuses approval based on a stale version.<br>**Violated prerequisite:** Approval must target the latest agreement version. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as participant | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as participant | `ALT_MA_VAERE_FYLT_UT` | at least one measure-specific required agreement field is empty<br>**Why:** The selected `AvtaleInnholdStrategy` reports an incomplete required-field set.<br>**Violated prerequisite:** All fields required for the agreement's measure type must be filled. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning; src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleInnhold.java — AvtaleInnhold.felterSomIkkeErFyltUt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as participant | `MANGLER_BEREGNING` | a wage-subsidy agreement lacks total subsidy, subsidy percentage, or generated subsidy periods<br>**Why:** Subsidy-backed agreements require a complete calculation before approval.<br>**Violated prerequisite:** Calculation values and subsidy periods must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as participant | `MANGLER_VEILEDER_PÅ_AVTALE` | the agreement has no assigned advisor<br>**Why:** Approval is blocked for an unassigned agreement.<br>**Violated prerequisite:** An advisor must be assigned before any party approves. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as participant | `KAN_IKKE_GODKJENNE_DELTAKER_HAR_ALLEREDE_GODKJENT` | the participant has already approved<br>**Why:** Duplicate participant approval is rejected.<br>**Violated prerequisite:** Participant approval may be recorded only once per current version. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `0/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/8`
- Behavior outcome checklist summary: `0/9`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: exact shared-route discrimination; context-valid required workflow and terminal-state proof; all 8 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B15-HP`, `B15-F01`, `B15-F02`, `B15-F03`, `B15-F04`, `B15-F05`, `B15-F06`, `B15-F07`, `B15-F08`, `B15-V01`.

### `B16`: `Employer agreement approval`

- Business goal: Records employer approval on a fully filled agreement.
- Starting point: Existing service state
- Expected business result: Records employer approval on a fully filled agreement.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | approve agreement as employer | `POST /avtaler/{avtaleId}/godkjenn` with path `avtaleId`; header `If-Unmodified-Since`; cookie `innlogget-part=ARBEIDSGIVER` to complete the domain transition | No — ambiguous | No | No | `test_104_postOnGodkjennReturns400`, `test_105_postOnGodkjennReturns400`, `test_163_postOnGodkjennReturns401` calls the shared route but lacks the documented discriminator/actor/pre-state; exact-function credit is withheld. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B16. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| approve agreement as employer | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as employer | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as employer | `SAMTIDIGE_ENDRINGER` | the supplied concurrency timestamp is absent or older than the agreement's current `sistEndret`<br>**Why:** The aggregate refuses approval based on a stale version.<br>**Violated prerequisite:** Approval must target the latest agreement version. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as employer | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as employer | `ALT_MA_VAERE_FYLT_UT` | at least one measure-specific required agreement field is empty<br>**Why:** The selected `AvtaleInnholdStrategy` reports an incomplete required-field set.<br>**Violated prerequisite:** All fields required for the agreement's measure type must be filled. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning; src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleInnhold.java — AvtaleInnhold.felterSomIkkeErFyltUt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as employer | `MANGLER_BEREGNING` | a wage-subsidy agreement lacks total subsidy, subsidy percentage, or generated subsidy periods<br>**Why:** Subsidy-backed agreements require a complete calculation before approval.<br>**Violated prerequisite:** Calculation values and subsidy periods must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as employer | `MANGLER_VEILEDER_PÅ_AVTALE` | the agreement has no assigned advisor<br>**Why:** Approval is blocked for an unassigned agreement.<br>**Violated prerequisite:** An advisor must be assigned before any party approves. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as employer | `KAN_IKKE_GODKJENNE_ARBEIDSGIVER_HAR_ALLEREDE_GODKJENT` | the employer has already approved<br>**Why:** Duplicate employer approval is rejected.<br>**Violated prerequisite:** Employer approval may be recorded only once per current version. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `0/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/8`
- Behavior outcome checklist summary: `0/9`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: exact shared-route discrimination; context-valid required workflow and terminal-state proof; all 8 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B16-HP`, `B16-F01`, `B16-F02`, `B16-F03`, `B16-F04`, `B16-F05`, `B16-F06`, `B16-F07`, `B16-F08`, `B16-V01`.

### `B17`: `Mentor confidentiality signing`

- Business goal: Records that the mentor has signed the confidentiality declaration.
- Starting point: Existing service state
- Expected business result: Records that the mentor has signed the confidentiality declaration.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | sign mentor confidentiality declaration | `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring` with path `avtaleId`; header `If-Unmodified-Since`; cookie `innlogget-part=MENTOR` to complete the domain transition | Yes | No | No | `test_103_postOnMentorGodkjennTaushetserkl_ringReturns400`, `test_134_postOnMentorGodkjennTaushetserkl_ringReturns400`, `test_161_postOnMentorGodkjennTaushetserkl_ringReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B17. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| sign mentor confidentiality declaration | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| sign mentor confidentiality declaration | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| sign mentor confidentiality declaration | `SAMTIDIGE_ENDRINGER` | the supplied concurrency timestamp is absent or stale<br>**Why:** The common approval wrapper rejects a stale agreement version.<br>**Violated prerequisite:** The signature must target the latest agreement version. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| sign mentor confidentiality declaration | `KAN_IKKE_GODKJENNE_MENTOR_HAR_ALLEREDE_GODKJENT` | the mentor has already signed the confidentiality declaration<br>**Why:** Duplicate mentor signing is rejected.<br>**Violated prerequisite:** The confidentiality signature may be recorded only once. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForMentor`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/4`
- Behavior outcome checklist summary: `0/5`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 4 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B17-HP`, `B17-F01`, `B17-F02`, `B17-F03`, `B17-F04`, `B17-V01`.

### `B18`: `Advisor final approval`

- Business goal: Records advisor approval after participant and employer have approved. For measures not requiring decision-maker approval, this also makes the agreement entered.
- Starting point: Existing service state
- Expected business result: Records advisor approval after participant and employer have approved. For measures not requiring decision-maker approval, this also makes the agreement entered.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | approve agreement as advisor | `POST /avtaler/{avtaleId}/godkjenn` with path `avtaleId`; header `If-Unmodified-Since`; cookie `innlogget-part=VEILEDER` to complete the domain transition | No — ambiguous | No | No | `test_104_postOnGodkjennReturns400`, `test_105_postOnGodkjennReturns400`, `test_163_postOnGodkjennReturns401` calls the shared route but lacks the documented discriminator/actor/pre-state; exact-function credit is withheld. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B18. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| approve agreement as advisor | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `SAMTIDIGE_ENDRINGER` | the supplied concurrency timestamp is absent or older than the agreement's current `sistEndret`<br>**Why:** The aggregate refuses approval based on a stale version.<br>**Violated prerequisite:** Approval must target the latest agreement version. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `ALT_MA_VAERE_FYLT_UT` | at least one measure-specific required agreement field is empty<br>**Why:** The selected `AvtaleInnholdStrategy` reports an incomplete required-field set.<br>**Violated prerequisite:** All fields required for the agreement's measure type must be filled. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning; src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleInnhold.java — AvtaleInnhold.felterSomIkkeErFyltUt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `MANGLER_BEREGNING` | a wage-subsidy agreement lacks total subsidy, subsidy percentage, or generated subsidy periods<br>**Why:** Subsidy-backed agreements require a complete calculation before approval.<br>**Violated prerequisite:** Calculation values and subsidy periods must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `MANGLER_VEILEDER_PÅ_AVTALE` | the agreement has no assigned advisor<br>**Why:** Approval is blocked for an unassigned agreement.<br>**Violated prerequisite:** An advisor must be assigned before any party approves. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `IKKE_TILGANG_TIL_DELTAKER` | PDL reports address protection code 6<br>**Why:** Advisor approval explicitly blocks code-6 participants.<br>**Violated prerequisite:** The advisor may not approve this protected participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `HENTING_AV_INNSATS_BEHOV_FEILET` | Arena has no complete follow-up status<br>**Why:** Advisor approval requires a complete Arena business result.<br>**Violated prerequisite:** Complete follow-up status must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET` | Arena returns an invalid qualification group<br>**Why:** The participant lacks a recognized qualification right.<br>**Violated prerequisite:** The qualification group must be valid. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNTILSKUDD_OG_SOMMERJOBB_FEIL` | the qualification group is ineligible for the selected temporary wage subsidy, summer-job, or mentor measure<br>**Why:** The measure eligibility check fails.<br>**Violated prerequisite:** The group must qualify for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `KVALIFISERINGSGRUPPE_VARIG_LONNTILSKUDD_FEIL` | the qualification group is ineligible for permanent wage subsidy<br>**Why:** The permanent-subsidy eligibility check fails.<br>**Violated prerequisite:** The group must qualify for permanent wage subsidy. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT` | the advisor has already approved<br>**Why:** Duplicate advisor approval is rejected.<br>**Violated prerequisite:** Advisor approval may be recorded only once. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `MENTOR_MÅ_SIGNERE_TAUSHETSERKLÆRING` | a mentor agreement lacks the mentor confidentiality signature<br>**Why:** Final advisor approval is ordered after the mentor signature.<br>**Violated prerequisite:** The mentor must sign first. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `VEILEDER_SKAL_GODKJENNE_SIST` | participant or employer approval is missing<br>**Why:** The advisor must be the last agreement party to approve.<br>**Violated prerequisite:** Participant and employer approvals must already exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `SOMMERJOBB_FOR_GAMMEL_FRA_OPPSTARTDATO` | the summer-job participant is over 30 at the agreement start<br>**Why:** The approval-time age rule uses the persisted start date.<br>**Violated prerequisite:** The participant must meet the summer-job age ceiling at start. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve agreement as advisor | `DELTAKER_72_AAR` | a non-summer-job participant is over 72 at the agreement end<br>**Why:** The approval-time upper age rule rejects the persisted end date.<br>**Violated prerequisite:** The participant must not exceed 72 at end. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `0/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/17`
- Behavior outcome checklist summary: `0/18`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: exact shared-route discrimination; context-valid required workflow and terminal-state proof; all 17 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B18-HP`, `B18-F01`, `B18-F02`, `B18-F03`, `B18-F04`, `B18-F05`, `B18-F06`, `B18-F07`, `B18-F08`, `B18-F09`, `B18-F10`, `B18-F11`, `B18-F12`, `B18-F13`, `B18-F14`, `B18-F15`, `B18-F16`, `B18-F17`, `B18-V01`.

### `B19`: `Advisor approval on behalf of participant`

- Business goal: Advisor records both advisor approval and participant approval on behalf of the participant.
- Starting point: Existing service state
- Expected business result: Advisor records both advisor approval and participant approval on behalf of the participant.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | approve on behalf of participant | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av` with path `avtaleId`; body `GodkjentPaVegneGrunn` with at least one reason; advisor caller to complete the domain transition | Yes | No | No | `test_126_postOnGodkjenn_paa_vegne_avReturns400`, `test_167_postOnGodkjenn_paa_vegne_avReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B19. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| approve on behalf of participant | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `IKKE_TILGANG_TIL_DELTAKER` | PDL reports address protection code 6 for the participant<br>**Why:** The advisor on-behalf path explicitly blocks code-6 processing.<br>**Violated prerequisite:** The advisor may not approve this protected participant through the on-behalf flow. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.blokkereKode6Prosessering`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `HENTING_AV_INNSATS_BEHOV_FEILET` | Arena has no complete follow-up status for the participant<br>**Why:** The on-behalf approval path rejects the Arena business result before mutating approvals.<br>**Violated prerequisite:** The participant must have complete, eligible follow-up status for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET` | Arena returns an invalid qualification group<br>**Why:** The on-behalf approval path rejects the Arena business result before mutating approvals.<br>**Violated prerequisite:** The participant must have complete, eligible follow-up status for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNTILSKUDD_OG_SOMMERJOBB_FEIL` | the qualification group is ineligible for the selected temporary wage subsidy, summer-job, or mentor measure<br>**Why:** The on-behalf approval path rejects the Arena business result before mutating approvals.<br>**Violated prerequisite:** The participant must have complete, eligible follow-up status for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `KVALIFISERINGSGRUPPE_VARIG_LONNTILSKUDD_FEIL` | the qualification group is ineligible for permanent wage subsidy<br>**Why:** The on-behalf approval path rejects the Arena business result before mutating approvals.<br>**Violated prerequisite:** The participant must have complete, eligible follow-up status for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `ALT_MA_VAERE_FYLT_UT` | at least one measure-specific required agreement field is empty<br>**Why:** The agreement cannot be approved while its required-field inventory is incomplete.<br>**Violated prerequisite:** All measure-specific required fields must be filled. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `MANGLER_BEREGNING` | a subsidy-backed agreement lacks a complete calculation or subsidy periods<br>**Why:** The agreement cannot be approved without its subsidy calculation.<br>**Violated prerequisite:** Calculation values and subsidy periods must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `MANGLER_VEILEDER_PÅ_AVTALE` | the agreement has no assigned advisor<br>**Why:** The aggregate requires an assigned advisor for approval.<br>**Violated prerequisite:** An advisor must be assigned. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `DELTAKER_HAR_GODKJENT` | the participant has already approved<br>**Why:** The aggregate's participant on-behalf approval guard rejects the request.<br>**Violated prerequisite:** The advisor cannot replace an existing participant approval. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `ARBEIDSGIVER_SKAL_GODKJENNE_FOER_VEILEDER` | the employer has not approved<br>**Why:** The aggregate's participant on-behalf approval guard rejects the request.<br>**Violated prerequisite:** Employer approval must precede this combined advisor/participant approval. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT` | the advisor has already approved<br>**Why:** The aggregate's participant on-behalf approval guard rejects the request.<br>**Violated prerequisite:** Advisor approval may be recorded only once. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `SOMMERJOBB_FOR_GAMMEL_FRA_OPPSTARTDATO` | the summer-job participant is over 30 at start<br>**Why:** The aggregate's participant on-behalf approval guard rejects the request.<br>**Violated prerequisite:** The participant must meet the summer-job age ceiling at start. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `MENTOR_MÅ_SIGNERE_TAUSHETSERKLÆRING` | a mentor agreement lacks the mentor signature<br>**Why:** The aggregate's participant on-behalf approval guard rejects the request.<br>**Violated prerequisite:** The mentor must sign before advisor on-behalf approval. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `DELTAKER_72_AAR` | a permanent-wage-subsidy participant is over 72 at end<br>**Why:** The aggregate's participant on-behalf approval guard rejects the request.<br>**Violated prerequisite:** Permanent wage subsidy must end before the participant exceeds 72. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `DELTAKER_67_AAR` | a non-permanent-subsidy participant is over 67 at end<br>**Why:** The aggregate's participant on-behalf approval guard rejects the request.<br>**Violated prerequisite:** The applicable agreement must end before the participant exceeds 67. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant | `GODKJENT_PAA_VEGNE_GRUNN_MAA_VELGES` | no participant on-behalf reason is selected<br>**Why:** The aggregate's participant on-behalf approval guard rejects the request.<br>**Violated prerequisite:** At least one concrete on-behalf reason must be recorded. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/19`
- Behavior outcome checklist summary: `0/20`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 19 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B19-HP`, `B19-F01`, `B19-F02`, `B19-F03`, `B19-F04`, `B19-F05`, `B19-F06`, `B19-F07`, `B19-F08`, `B19-F09`, `B19-F10`, `B19-F11`, `B19-F12`, `B19-F13`, `B19-F14`, `B19-F15`, `B19-F16`, `B19-F17`, `B19-F18`, `B19-F19`, `B19-V01`.

### `B20`: `Advisor approval on behalf of employer`

- Business goal: Advisor records both advisor approval and employer approval on behalf of the employer.
- Starting point: Existing service state
- Expected business result: Advisor records both advisor approval and employer approval on behalf of the employer.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | approve on behalf of employer | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver` with path `avtaleId`; body `GodkjentPaVegneAvArbeidsgiverGrunn` with at least one reason; advisor caller to complete the domain transition | Yes | No | No | `test_127_postOnGodkjenn_paa_vegne_av_arbeidsgiverReturns400`, `test_168_postOnGodkjenn_paa_vegne_av_arbeidsgiverReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B20. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| approve on behalf of employer | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `IKKE_TILGANG_TIL_DELTAKER` | PDL reports address protection code 6 for the participant<br>**Why:** The advisor on-behalf path explicitly blocks code-6 processing.<br>**Violated prerequisite:** The advisor may not approve this protected participant through the on-behalf flow. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.blokkereKode6Prosessering`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `HENTING_AV_INNSATS_BEHOV_FEILET` | Arena has no complete follow-up status for the participant<br>**Why:** The on-behalf approval path rejects the Arena business result before mutating approvals.<br>**Violated prerequisite:** The participant must have complete, eligible follow-up status for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET` | Arena returns an invalid qualification group<br>**Why:** The on-behalf approval path rejects the Arena business result before mutating approvals.<br>**Violated prerequisite:** The participant must have complete, eligible follow-up status for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNTILSKUDD_OG_SOMMERJOBB_FEIL` | the qualification group is ineligible for the selected temporary wage subsidy, summer-job, or mentor measure<br>**Why:** The on-behalf approval path rejects the Arena business result before mutating approvals.<br>**Violated prerequisite:** The participant must have complete, eligible follow-up status for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `KVALIFISERINGSGRUPPE_VARIG_LONNTILSKUDD_FEIL` | the qualification group is ineligible for permanent wage subsidy<br>**Why:** The on-behalf approval path rejects the Arena business result before mutating approvals.<br>**Violated prerequisite:** The participant must have complete, eligible follow-up status for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `ALT_MA_VAERE_FYLT_UT` | at least one measure-specific required agreement field is empty<br>**Why:** The agreement cannot be approved while its required-field inventory is incomplete.<br>**Violated prerequisite:** All measure-specific required fields must be filled. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `MANGLER_BEREGNING` | a subsidy-backed agreement lacks a complete calculation or subsidy periods<br>**Why:** The agreement cannot be approved without its subsidy calculation.<br>**Violated prerequisite:** Calculation values and subsidy periods must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `MANGLER_VEILEDER_PÅ_AVTALE` | the agreement has no assigned advisor<br>**Why:** The aggregate requires an assigned advisor for approval.<br>**Violated prerequisite:** An advisor must be assigned. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `GODKJENN_PAA_VEGNE_AV_FEIL_TILTAKSTYPE` | the measure is not summer job, temporary wage subsidy, or permanent wage subsidy<br>**Why:** The aggregate's employer on-behalf approval guard rejects the request.<br>**Violated prerequisite:** Employer on-behalf approval is restricted to the three implemented measures. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgArbeidsgiver`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `ARBEIDSGIVER_HAR_GODKJENT` | the employer has already approved<br>**Why:** The aggregate's employer on-behalf approval guard rejects the request.<br>**Violated prerequisite:** The advisor cannot replace an existing employer approval. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgArbeidsgiver`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `DELTAKER_SKAL_GODKJENNE_FOER_VEILEDER` | the participant has not approved<br>**Why:** The aggregate's employer on-behalf approval guard rejects the request.<br>**Violated prerequisite:** Participant approval must precede employer on-behalf approval. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgArbeidsgiver`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT` | the advisor has already approved<br>**Why:** The aggregate's employer on-behalf approval guard rejects the request.<br>**Violated prerequisite:** Advisor approval may be recorded only once. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgArbeidsgiver`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `SOMMERJOBB_FOR_GAMMEL_FRA_OPPSTARTDATO` | the summer-job participant is over 30 at start<br>**Why:** The aggregate's employer on-behalf approval guard rejects the request.<br>**Violated prerequisite:** The participant must satisfy the summer-job age ceiling at start. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgArbeidsgiver`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of employer | `GODKJENT_PAA_VEGNE_GRUNN_MAA_VELGES` | no employer on-behalf reason is selected<br>**Why:** The aggregate's employer on-behalf approval guard rejects the request.<br>**Violated prerequisite:** At least one concrete employer reason must be recorded. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgArbeidsgiver`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/17`
- Behavior outcome checklist summary: `0/18`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 17 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B20-HP`, `B20-F01`, `B20-F02`, `B20-F03`, `B20-F04`, `B20-F05`, `B20-F06`, `B20-F07`, `B20-F08`, `B20-F09`, `B20-F10`, `B20-F11`, `B20-F12`, `B20-F13`, `B20-F14`, `B20-F15`, `B20-F16`, `B20-F17`, `B20-V01`.

### `B21`: `Advisor approval on behalf of participant and employer`

- Business goal: Advisor records advisor, participant, and employer approvals in one operation.
- Starting point: Existing service state
- Expected business result: Advisor records advisor, participant, and employer approvals in one operation.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | approve on behalf of participant and employer | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with path `avtaleId`; body with participant and employer on-behalf reason objects; advisor caller to complete the domain transition | Yes | No | No | `test_124_postOnGodkjenn_paa_vegne_av_deltaker_og_arbeidsgiverReturns400`, `test_165_postOnGodkjenn_paa_vegne_av_deltaker_og_arbeidsgiverReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B21. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| approve on behalf of participant and employer | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `IKKE_TILGANG_TIL_DELTAKER` | PDL reports address protection code 6 for the participant<br>**Why:** The advisor on-behalf path explicitly blocks code-6 processing.<br>**Violated prerequisite:** The advisor may not approve this protected participant through the on-behalf flow. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.blokkereKode6Prosessering`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `HENTING_AV_INNSATS_BEHOV_FEILET` | Arena has no complete follow-up status for the participant<br>**Why:** The on-behalf approval path rejects the Arena business result before mutating approvals.<br>**Violated prerequisite:** The participant must have complete, eligible follow-up status for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET` | Arena returns an invalid qualification group<br>**Why:** The on-behalf approval path rejects the Arena business result before mutating approvals.<br>**Violated prerequisite:** The participant must have complete, eligible follow-up status for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNTILSKUDD_OG_SOMMERJOBB_FEIL` | the qualification group is ineligible for the selected temporary wage subsidy, summer-job, or mentor measure<br>**Why:** The on-behalf approval path rejects the Arena business result before mutating approvals.<br>**Violated prerequisite:** The participant must have complete, eligible follow-up status for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `KVALIFISERINGSGRUPPE_VARIG_LONNTILSKUDD_FEIL` | the qualification group is ineligible for permanent wage subsidy<br>**Why:** The on-behalf approval path rejects the Arena business result before mutating approvals.<br>**Violated prerequisite:** The participant must have complete, eligible follow-up status for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `ALT_MA_VAERE_FYLT_UT` | at least one measure-specific required agreement field is empty<br>**Why:** The agreement cannot be approved while its required-field inventory is incomplete.<br>**Violated prerequisite:** All measure-specific required fields must be filled. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `MANGLER_BEREGNING` | a subsidy-backed agreement lacks a complete calculation or subsidy periods<br>**Why:** The agreement cannot be approved without its subsidy calculation.<br>**Violated prerequisite:** Calculation values and subsidy periods must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `MANGLER_VEILEDER_PÅ_AVTALE` | the agreement has no assigned advisor<br>**Why:** The aggregate requires an assigned advisor for approval.<br>**Violated prerequisite:** An advisor must be assigned. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `GODKJENN_PAA_VEGNE_AV_FEIL_TILTAKSTYPE` | the measure is not summer job, temporary wage subsidy, or permanent wage subsidy<br>**Why:** The aggregate's combined on-behalf approval guard rejects the request.<br>**Violated prerequisite:** Combined on-behalf approval is restricted to the implemented measures. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `DELTAKER_HAR_GODKJENT` | the participant has already approved<br>**Why:** The aggregate's combined on-behalf approval guard rejects the request.<br>**Violated prerequisite:** The combined call requires no prior participant approval. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `ARBEIDSGIVER_HAR_GODKJENT` | the employer has already approved<br>**Why:** The aggregate's combined on-behalf approval guard rejects the request.<br>**Violated prerequisite:** The combined call requires no prior employer approval. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT` | the advisor has already approved<br>**Why:** The aggregate's combined on-behalf approval guard rejects the request.<br>**Violated prerequisite:** Advisor approval may be recorded only once. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `SOMMERJOBB_FOR_GAMMEL_FRA_OPPSTARTDATO` | the summer-job participant is over 30 at start<br>**Why:** The aggregate's combined on-behalf approval guard rejects the request.<br>**Violated prerequisite:** The participant must satisfy the summer-job age ceiling at start. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `GODKJENT_PAA_VEGNE_GRUNN_MAA_VELGES` | no employer on-behalf reason is selected<br>**Why:** The aggregate's combined on-behalf approval guard rejects the request.<br>**Violated prerequisite:** At least one employer on-behalf reason must be recorded. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve on behalf of participant and employer | `GODKJENT_PAA_VEGNE_GRUNN_MAA_VELGES` | no participant on-behalf reason is selected<br>**Why:** The aggregate's combined on-behalf approval guard rejects the request.<br>**Violated prerequisite:** At least one participant on-behalf reason must be recorded. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/18`
- Behavior outcome checklist summary: `0/19`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 18 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B21-HP`, `B21-F01`, `B21-F02`, `B21-F03`, `B21-F04`, `B21-F05`, `B21-F06`, `B21-F07`, `B21-F08`, `B21-F09`, `B21-F10`, `B21-F11`, `B21-F12`, `B21-F13`, `B21-F14`, `B21-F15`, `B21-F16`, `B21-F17`, `B21-F18`, `B21-V01`.

### `B22`: `Approval revocation`

- Business goal: Clears existing participant, employer, and advisor approvals on an agreement that has not yet been entered.
- Starting point: Existing service state
- Expected business result: Clears existing participant, employer, and advisor approvals on an agreement that has not yet been entered.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | revoke approvals | `POST /avtaler/{avtaleId}/opphev-godkjenninger` with path `avtaleId`; authenticated caller with required role and access to complete the domain transition | Yes | No | No | `test_101_postOnOpphev_godkjenningerReturns400`, `test_159_postOnOpphev_godkjenningerReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B22. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| revoke approvals | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| revoke approvals | `KAN_IKKE_OPPHEVE` | the selected role is not permitted to revoke approvals, or an employer attempts revocation after advisor approval<br>**Why:** The role-specific revocation predicate rejects the transition.<br>**Violated prerequisite:** Only an advisor, or an employer before advisor approval, may revoke. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.opphevGodkjenninger`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| revoke approvals | `KAN_IKKE_OPPHEVE` | no participant, employer, or advisor approval exists<br>**Why:** There is no approval state to revoke.<br>**Violated prerequisite:** At least one approval must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.opphevGodkjenninger`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| revoke approvals | `KAN_IKKE_OPPHEVE_GODKJENNINGER_VED_INNGAATT_AVTALE` | the agreement is already entered<br>**Why:** Approval state is final after agreement entry.<br>**Violated prerequisite:** The agreement must not be entered. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.opphevGodkjenninger`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/4`
- Behavior outcome checklist summary: `0/5`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 4 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B22-HP`, `B22-F01`, `B22-F02`, `B22-F03`, `B22-F04`, `B22-V01`.

### `B23`: `After-registration eligibility marking`

- Business goal: Toggles an unentered agreement so it is approved for after-registration.
- Starting point: Existing service state
- Expected business result: Toggles an unentered agreement so it is approved for after-registration.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | mark agreement eligible for after-registration | `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` with path `avtaleId`; authenticated caller with required role and access to complete the domain transition | No — ambiguous | No | No | `test_99_postOnSet_om_avtalen_kan_etterregistreresReturns400`, `test_157_postOnSet_om_avtalen_kan_etterregistreresReturns401` calls the shared route but lacks the documented discriminator/actor/pre-state; exact-function credit is withheld. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B23. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| mark agreement eligible for after-registration | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| mark agreement eligible for after-registration | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| mark agreement eligible for after-registration | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| mark agreement eligible for after-registration | `KAN_IKKE_MERKES_FOR_ETTERREGISTRERING_AVTALE_GODKJENT` | the agreement is already entered<br>**Why:** The toggle is frozen after agreement entry.<br>**Violated prerequisite:** After-registration eligibility may change only before entry. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.togglegodkjennEtterregistrering`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `0/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/4`
- Behavior outcome checklist summary: `0/5`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: exact shared-route discrimination; context-valid required workflow and terminal-state proof; all 4 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B23-HP`, `B23-F01`, `B23-F02`, `B23-F03`, `B23-F04`, `B23-V01`.

### `B24`: `After-registration eligibility removal`

- Business goal: Toggles an unentered agreement so it is no longer approved for after-registration.
- Starting point: Existing service state
- Expected business result: Toggles an unentered agreement so it is no longer approved for after-registration.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | remove after-registration eligibility | `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` with path `avtaleId`; authenticated caller with required role and access to complete the domain transition | No — ambiguous | No | No | `test_99_postOnSet_om_avtalen_kan_etterregistreresReturns400`, `test_157_postOnSet_om_avtalen_kan_etterregistreresReturns401` calls the shared route but lacks the documented discriminator/actor/pre-state; exact-function credit is withheld. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B24. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| remove after-registration eligibility | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| remove after-registration eligibility | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| remove after-registration eligibility | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| remove after-registration eligibility | `KAN_IKKE_MERKES_FOR_ETTERREGISTRERING_AVTALE_GODKJENT` | the agreement is already entered<br>**Why:** The toggle is frozen after agreement entry.<br>**Violated prerequisite:** After-registration eligibility may change only before entry. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.togglegodkjennEtterregistrering`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `0/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/4`
- Behavior outcome checklist summary: `0/5`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: exact shared-route discrimination; context-valid required workflow and terminal-state proof; all 4 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B24-HP`, `B24-F01`, `B24-F02`, `B24-F03`, `B24-F04`, `B24-V01`.

### `B25`: `Subsidy period approval`

- Business goal: Decision-maker approves the current subsidy period; for the first decision-maker-controlled period, this also enters the agreement.
- Starting point: Existing service state
- Expected business result: Decision-maker approves the current subsidy period; for the first decision-maker-controlled period, this also enters the agreement.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | approve subsidy period | `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode` with path `avtaleId`; body `GodkjennTilskuddsperiodeRequest` with four-digit `enhet`; decision-maker caller to complete the domain transition | Yes | No | No | `test_106_postOnGodkjenn_tilskuddsperiodeReturns400`, `test_164_postOnGodkjenn_tilskuddsperiodeReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B25. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| approve subsidy period | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve subsidy period | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve subsidy period | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve subsidy period | `TILSKUDDSPERIODE_KAN_KUN_BEHANDLES_VED_INNGAATT_AVTALE` | the agreement lacks advisor approval<br>**Why:** Subsidy-period treatment is blocked before advisor approval.<br>**Violated prerequisite:** The agreement must be advisor-approved. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennTilskuddsperiode/avslåTilskuddsperiode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve subsidy period | `TILSKUDDSPERIODE_ER_ALLEREDE_BEHANDLET` | the current subsidy period is not `UBEHANDLET`<br>**Why:** A decided, rejected, annulled, or Arena-treated period cannot be decided again.<br>**Violated prerequisite:** The current active period must be untreated. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/TilskuddPeriode.java — TilskuddPeriode.sjekkOmKanBehandles`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve subsidy period | `TILSKUDDSPERIODE_BEHANDLE_FOR_TIDLIG` | the current subsidy period is more than three months in the future and is not the first period<br>**Why:** The period has not reached its decision window.<br>**Violated prerequisite:** A later period may be decided no earlier than three months before start. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/TilskuddPeriode.java — TilskuddPeriode.sjekkOmKanBehandles`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve subsidy period | `ENHET_FINNES_IKKE` | Norg2 returns no NAV unit for the requested cost-center unit<br>**Why:** The decision-maker service requires a real unit before approval.<br>**Violated prerequisite:** The supplied unit must exist in Norg2. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.godkjennTilskuddsperiode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve subsidy period | `TILSKUDDSPERIODE_ENHET_FIRE_SIFFER` | the unit is absent or is not exactly four digits<br>**Why:** The aggregate enforces the business unit-number format.<br>**Violated prerequisite:** Approval must record a four-digit NAV unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennTilskuddsperiode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| approve subsidy period | `TILSKUDDSPERIODE_IKKE_GODKJENNE_EGNE` | the decision-maker is the same advisor who approved the agreement<br>**Why:** Separation of duties blocks approval of one's own agreement.<br>**Violated prerequisite:** The subsidy-period decision-maker must differ from the approving advisor. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennTilskuddsperiode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/9`
- Behavior outcome checklist summary: `0/10`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 9 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B25-HP`, `B25-F01`, `B25-F02`, `B25-F03`, `B25-F04`, `B25-F05`, `B25-F06`, `B25-F07`, `B25-F08`, `B25-F09`, `B25-V01`.

### `B26`: `Subsidy period rejection`

- Business goal: Decision-maker rejects the current subsidy period with rejection causes and explanation.
- Starting point: Existing service state
- Expected business result: Decision-maker rejects the current subsidy period with rejection causes and explanation.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | reject subsidy period | `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode` with path `avtaleId`; body `AvslagRequest` with non-empty `avslagsårsaker` and non-blank `avslagsforklaring`; decision-maker caller to complete the domain transition | Yes | No | No | `test_117_postOnAvslag_tilskuddsperiodeReturns400`, `test_183_postOnAvslag_tilskuddsperiodeReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B26. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| reject subsidy period | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| reject subsidy period | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| reject subsidy period | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| reject subsidy period | `TILSKUDDSPERIODE_KAN_KUN_BEHANDLES_VED_INNGAATT_AVTALE` | the agreement lacks advisor approval<br>**Why:** Subsidy-period treatment is blocked before advisor approval.<br>**Violated prerequisite:** The agreement must be advisor-approved. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennTilskuddsperiode/avslåTilskuddsperiode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| reject subsidy period | `TILSKUDDSPERIODE_ER_ALLEREDE_BEHANDLET` | the current subsidy period is not `UBEHANDLET`<br>**Why:** A decided, rejected, annulled, or Arena-treated period cannot be decided again.<br>**Violated prerequisite:** The current active period must be untreated. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/TilskuddPeriode.java — TilskuddPeriode.sjekkOmKanBehandles`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| reject subsidy period | `TILSKUDDSPERIODE_BEHANDLE_FOR_TIDLIG` | the current subsidy period is more than three months in the future and is not the first period<br>**Why:** The period has not reached its decision window.<br>**Violated prerequisite:** A later period may be decided no earlier than three months before start. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/TilskuddPeriode.java — TilskuddPeriode.sjekkOmKanBehandles`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| reject subsidy period | `TILSKUDDSPERIODE_AVSLAGSFORKLARING_PAAKREVD` | the rejection explanation is blank<br>**Why:** The period entity requires a substantive explanation.<br>**Violated prerequisite:** A non-blank rejection explanation is mandatory. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/TilskuddPeriode.java — TilskuddPeriode.avslå`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| reject subsidy period | `TILSKUDDSPERIODE_INGEN_AVSLAGSAARSAKER` | the rejection-cause set is empty<br>**Why:** The period entity requires at least one rejection cause.<br>**Violated prerequisite:** At least one rejection cause is mandatory. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/TilskuddPeriode.java — TilskuddPeriode.avslå`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/8`
- Behavior outcome checklist summary: `0/9`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 8 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B26-HP`, `B26-F01`, `B26-F02`, `B26-F03`, `B26-F04`, `B26-F05`, `B26-F06`, `B26-F07`, `B26-F08`, `B26-V01`.

### `B27`: `Rejected subsidy period return`

- Business goal: Deactivates active rejected subsidy periods and creates new unhandled periods for correction.
- Starting point: Existing service state
- Expected business result: Deactivates active rejected subsidy periods and creates new unhandled periods for correction.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | send rejected subsidy period back | `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter` with path `avtaleId`; authenticated caller with required role and access to complete the domain transition | Yes | No | No | `test_100_postOnSend_tilbake_til_beslutterReturns400`, `test_158_postOnSend_tilbake_til_beslutterReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B27. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| send rejected subsidy period back | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| send rejected subsidy period back | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| send rejected subsidy period back | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 3 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B27-HP`, `B27-F01`, `B27-F02`, `B27-F03`, `B27-V01`.

### `B28`: `Agreement shortening`

- Business goal: Creates a new approved version with an earlier end date and adjusts subsidy periods.
- Starting point: Existing service state
- Expected business result: Creates a new approved version with an earlier end date and adjusts subsidy periods.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | shorten agreement | `POST /avtaler/{avtaleId}/forkort` with path `avtaleId`; body `ForkortAvtale` with earlier `sluttDato`, `grunn`, optional `annetGrunn`; advisor caller to complete the domain transition | Yes | No | No | `test_108_postOnForkortReturns400`, `test_137_postOnForkortReturns400`, `test_171_postOnForkortReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B28. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| shorten agreement | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `KAN_IKKE_FORKORTE_IKKE_GODKJENT_AVTALE` | the agreement lacks advisor approval<br>**Why:** Only approved agreements can be shortened.<br>**Violated prerequisite:** Advisor approval must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `KAN_IKKE_FORKORTE_ETTER_SLUTTDATO` | the proposed end date is equal to or later than the current end date<br>**Why:** The request is not a domain shortening.<br>**Violated prerequisite:** A shortening must reduce the end date. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `KAN_IKKE_FORKORTE_FOR_UTBETALT_TILSKUDDSPERIODE` | the proposed end date falls before the end of the latest active period with a sent, paid, failed-payment, approved-minus, or approved-zero refund<br>**Why:** The shortening would invalidate a financially final period.<br>**Violated prerequisite:** The new end date must preserve all financially committed periods. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `KAN_IKKE_FORKORTE_GRUNN_MANGLER` | the shortening reason is blank, or reason `Annet` has no free-text explanation<br>**Why:** A persisted shortening requires a concrete reason.<br>**Violated prerequisite:** A reason, and explanation for `Annet`, is mandatory. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `START_ETTER_SLUTT` | the new end date is before the persisted start date<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Start must not follow end. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `SLUTTDATO_GRENSE_NÅDD` | the new end date is after 2089-12-31<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The upper end-date bound is 2089-12-31. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `VARIGHET_FOR_LANG_ARBEIDSTRENING` | the resulting work-training duration exceeds 18 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Work training is limited to 18 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/ArbeidstreningStartOgSluttDatoStrategy.java — ArbeidstreningStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `VARIGHET_FOR_LANG_INKLUDERINGSTILSKUDD` | the resulting inclusion-subsidy duration exceeds 12 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Inclusion subsidy is limited to its implemented maximum. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/InkluderingstilskuddStartOgSluttDatoStrategy.java — InkluderingstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `VARIGHET_FOR_LANG_MENTOR_36_MND` | the resulting adapted-effort mentor duration exceeds 36 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The adapted-effort mentor maximum is 36 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MentorStartOgSluttDatoStrategy.java — MentorStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `VARIGHET_FOR_LANG_MENTOR_6_MND` | the resulting other mentor duration exceeds 6 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The ordinary mentor maximum is 6 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MentorStartOgSluttDatoStrategy.java — MentorStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `VARIGHET_FOR_LANG_MIDLERTIDIG_LONNSTILSKUDD_24_MND` | the resulting adapted-effort temporary subsidy exceeds 24 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The adapted-effort maximum is 24 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MidlertidigLonnstilskuddStartOgSluttDatoStrategy.java — MidlertidigLonnstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `VARIGHET_FOR_LANG_MIDLERTIDIG_LONNSTILSKUDD_12_MND` | the resulting temporary subsidy exceeds the applicable 12-month maximum<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Situational or missing qualification group is limited to 12 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MidlertidigLonnstilskuddStartOgSluttDatoStrategy.java — MidlertidigLonnstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `SOMMERJOBB_FOR_TIDLIG` | the resulting summer-job date is before its permitted window<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Summer-job dates must remain in the summer window. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `SOMMERJOBB_FOR_SENT` | the resulting summer-job date is after its permitted window<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Summer-job dates must remain in the summer window. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| shorten agreement | `SOMMERJOBB_FOR_LANG_VARIGHET` | the resulting summer job exceeds four weeks<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Summer jobs are limited to four weeks minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/18`
- Behavior outcome checklist summary: `0/19`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 18 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B28-HP`, `B28-F01`, `B28-F02`, `B28-F03`, `B28-F04`, `B28-F05`, `B28-F06`, `B28-F07`, `B28-F08`, `B28-F09`, `B28-F10`, `B28-F11`, `B28-F12`, `B28-F13`, `B28-F14`, `B28-F15`, `B28-F16`, `B28-F17`, `B28-F18`, `B28-V01`.

### `B29`: `Dry-run agreement shortening`

- Business goal: Returns the would-be shortened agreement without saving it.
- Starting point: Existing service state
- Expected business result: Returns the would-be shortened agreement without saving it.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | dry-run agreement shortening | `POST /avtaler/{avtaleId}/forkort-dry-run` with path `avtaleId`; body `ForkortAvtale` with earlier `sluttDato`; advisor caller to validate and preview the transition without saving it | Yes | No | No | `test_109_postOnForkort_dry_runReturns400`, `test_135_postOnForkort_dry_runReturns400`, `test_172_postOnForkort_dry_runReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| dry-run agreement shortening | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `KAN_IKKE_FORKORTE_IKKE_GODKJENT_AVTALE` | the agreement lacks advisor approval<br>**Why:** Only approved agreements can be shortened.<br>**Violated prerequisite:** Advisor approval must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `KAN_IKKE_FORKORTE_ETTER_SLUTTDATO` | the proposed end date is equal to or later than the current end date<br>**Why:** The request is not a domain shortening.<br>**Violated prerequisite:** A shortening must reduce the end date. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `KAN_IKKE_FORKORTE_FOR_UTBETALT_TILSKUDDSPERIODE` | the proposed end date falls before the end of the latest active period with a sent, paid, failed-payment, approved-minus, or approved-zero refund<br>**Why:** The shortening would invalidate a financially final period.<br>**Violated prerequisite:** The new end date must preserve all financially committed periods. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `START_ETTER_SLUTT` | the new end date is before the persisted start date<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Start must not follow end. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `SLUTTDATO_GRENSE_NÅDD` | the new end date is after 2089-12-31<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The upper end-date bound is 2089-12-31. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `VARIGHET_FOR_LANG_ARBEIDSTRENING` | the resulting work-training duration exceeds 18 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Work training is limited to 18 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/ArbeidstreningStartOgSluttDatoStrategy.java — ArbeidstreningStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `VARIGHET_FOR_LANG_INKLUDERINGSTILSKUDD` | the resulting inclusion-subsidy duration exceeds 12 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Inclusion subsidy is limited to its implemented maximum. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/InkluderingstilskuddStartOgSluttDatoStrategy.java — InkluderingstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `VARIGHET_FOR_LANG_MENTOR_36_MND` | the resulting adapted-effort mentor duration exceeds 36 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The adapted-effort mentor maximum is 36 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MentorStartOgSluttDatoStrategy.java — MentorStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `VARIGHET_FOR_LANG_MENTOR_6_MND` | the resulting other mentor duration exceeds 6 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The ordinary mentor maximum is 6 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MentorStartOgSluttDatoStrategy.java — MentorStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `VARIGHET_FOR_LANG_MIDLERTIDIG_LONNSTILSKUDD_24_MND` | the resulting adapted-effort temporary subsidy exceeds 24 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The adapted-effort maximum is 24 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MidlertidigLonnstilskuddStartOgSluttDatoStrategy.java — MidlertidigLonnstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `VARIGHET_FOR_LANG_MIDLERTIDIG_LONNSTILSKUDD_12_MND` | the resulting temporary subsidy exceeds the applicable 12-month maximum<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Situational or missing qualification group is limited to 12 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MidlertidigLonnstilskuddStartOgSluttDatoStrategy.java — MidlertidigLonnstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `SOMMERJOBB_FOR_TIDLIG` | the resulting summer-job date is before its permitted window<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Summer-job dates must remain in the summer window. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `SOMMERJOBB_FOR_SENT` | the resulting summer-job date is after its permitted window<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Summer-job dates must remain in the summer window. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement shortening | `SOMMERJOBB_FOR_LANG_VARIGHET` | the resulting summer job exceeds four weeks<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Summer jobs are limited to four weeks minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/17`
- Behavior outcome checklist summary: `0/18`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 17 concrete failure branches.
- Recommended test IDs that close the gap: `B29-HP`, `B29-F01`, `B29-F02`, `B29-F03`, `B29-F04`, `B29-F05`, `B29-F06`, `B29-F07`, `B29-F08`, `B29-F09`, `B29-F10`, `B29-F11`, `B29-F12`, `B29-F13`, `B29-F14`, `B29-F15`, `B29-F16`, `B29-F17`.

### `B30`: `Agreement extension`

- Business goal: Creates a new approved version with a later end date and adds or recalculates subsidy periods.
- Starting point: Existing service state
- Expected business result: Creates a new approved version with a later end date and adds or recalculates subsidy periods.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | extend agreement | `POST /avtaler/{avtaleId}/forleng` with path `avtaleId`; body `ForlengAvtale` with later `sluttDato`; advisor caller to complete the domain transition | Yes | No | No | `test_128_postOnForlengReturns400`, `test_136_postOnForlengReturns400`, `test_169_postOnForlengReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B30. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| extend agreement | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `HENTING_AV_INNSATS_BEHOV_FEILET` | Arena has no complete follow-up status<br>**Why:** Extension refreshes and validates the participant's measure eligibility before changing dates.<br>**Violated prerequisite:** The participant must remain eligible for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET` | Arena returns an invalid qualification group<br>**Why:** Extension refreshes and validates the participant's measure eligibility before changing dates.<br>**Violated prerequisite:** The participant must remain eligible for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNTILSKUDD_OG_SOMMERJOBB_FEIL` | the qualification group is ineligible for the selected temporary subsidy, summer-job, or mentor measure<br>**Why:** Extension refreshes and validates the participant's measure eligibility before changing dates.<br>**Violated prerequisite:** The participant must remain eligible for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `KVALIFISERINGSGRUPPE_VARIG_LONNTILSKUDD_FEIL` | the qualification group is ineligible for permanent wage subsidy<br>**Why:** Extension refreshes and validates the participant's measure eligibility before changing dates.<br>**Violated prerequisite:** The participant must remain eligible for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `KAN_IKKE_FORLENGE_IKKE_GODKJENT_AVTALE` | the agreement lacks advisor approval<br>**Why:** Only approved agreements can be extended.<br>**Violated prerequisite:** Advisor approval must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forlengAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `KAN_IKKE_FORLENGE_FEIL_SLUTTDATO` | the proposed end date is not later than the current end date<br>**Why:** The request is not a domain extension.<br>**Violated prerequisite:** An extension must increase the end date. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forlengAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `START_ETTER_SLUTT` | the new end date is before the persisted start date<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Start must not follow end. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `SLUTTDATO_GRENSE_NÅDD` | the new end date is after 2089-12-31<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The upper end-date bound is 2089-12-31. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `VARIGHET_FOR_LANG_ARBEIDSTRENING` | the resulting work-training duration exceeds 18 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Work training is limited to 18 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/ArbeidstreningStartOgSluttDatoStrategy.java — ArbeidstreningStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `VARIGHET_FOR_LANG_INKLUDERINGSTILSKUDD` | the resulting inclusion-subsidy duration exceeds 12 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Inclusion subsidy is limited to its implemented maximum. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/InkluderingstilskuddStartOgSluttDatoStrategy.java — InkluderingstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `VARIGHET_FOR_LANG_MENTOR_36_MND` | the resulting adapted-effort mentor duration exceeds 36 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The adapted-effort mentor maximum is 36 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MentorStartOgSluttDatoStrategy.java — MentorStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `VARIGHET_FOR_LANG_MENTOR_6_MND` | the resulting other mentor duration exceeds 6 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The ordinary mentor maximum is 6 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MentorStartOgSluttDatoStrategy.java — MentorStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `VARIGHET_FOR_LANG_MIDLERTIDIG_LONNSTILSKUDD_24_MND` | the resulting adapted-effort temporary subsidy exceeds 24 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The adapted-effort maximum is 24 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MidlertidigLonnstilskuddStartOgSluttDatoStrategy.java — MidlertidigLonnstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `VARIGHET_FOR_LANG_MIDLERTIDIG_LONNSTILSKUDD_12_MND` | the resulting temporary subsidy exceeds the applicable 12-month maximum<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Situational or missing qualification group is limited to 12 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MidlertidigLonnstilskuddStartOgSluttDatoStrategy.java — MidlertidigLonnstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `SOMMERJOBB_FOR_TIDLIG` | the resulting summer-job date is before its permitted window<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Summer-job dates must remain in the summer window. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `SOMMERJOBB_FOR_SENT` | the resulting summer-job date is after its permitted window<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Summer-job dates must remain in the summer window. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| extend agreement | `SOMMERJOBB_FOR_LANG_VARIGHET` | the resulting summer job exceeds four weeks<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Summer jobs are limited to four weeks minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/20`
- Behavior outcome checklist summary: `0/21`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 20 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B30-HP`, `B30-F01`, `B30-F02`, `B30-F03`, `B30-F04`, `B30-F05`, `B30-F06`, `B30-F07`, `B30-F08`, `B30-F09`, `B30-F10`, `B30-F11`, `B30-F12`, `B30-F13`, `B30-F14`, `B30-F15`, `B30-F16`, `B30-F17`, `B30-F18`, `B30-F19`, `B30-F20`, `B30-V01`.

### `B31`: `Dry-run agreement extension`

- Business goal: Returns the would-be extended agreement without saving it.
- Starting point: Existing service state
- Expected business result: Returns the would-be extended agreement without saving it.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | dry-run agreement extension | `POST /avtaler/{avtaleId}/forleng-dry-run` with path `avtaleId`; body `ForlengAvtale` with later `sluttDato`; advisor caller to validate and preview the transition without saving it | Yes | No | No | `test_107_postOnForleng_dry_runReturns400`, `test_170_postOnForleng_dry_runReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| dry-run agreement extension | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `HENTING_AV_INNSATS_BEHOV_FEILET` | Arena has no complete follow-up status<br>**Why:** Extension refreshes and validates the participant's measure eligibility before changing dates.<br>**Violated prerequisite:** The participant must remain eligible for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET` | Arena returns an invalid qualification group<br>**Why:** Extension refreshes and validates the participant's measure eligibility before changing dates.<br>**Violated prerequisite:** The participant must remain eligible for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNTILSKUDD_OG_SOMMERJOBB_FEIL` | the qualification group is ineligible for the selected temporary subsidy, summer-job, or mentor measure<br>**Why:** Extension refreshes and validates the participant's measure eligibility before changing dates.<br>**Violated prerequisite:** The participant must remain eligible for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `KVALIFISERINGSGRUPPE_VARIG_LONNTILSKUDD_FEIL` | the qualification group is ineligible for permanent wage subsidy<br>**Why:** Extension refreshes and validates the participant's measure eligibility before changing dates.<br>**Violated prerequisite:** The participant must remain eligible for the measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `KAN_IKKE_FORLENGE_IKKE_GODKJENT_AVTALE` | the agreement lacks advisor approval<br>**Why:** Only approved agreements can be extended.<br>**Violated prerequisite:** Advisor approval must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forlengAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `KAN_IKKE_FORLENGE_FEIL_SLUTTDATO` | the proposed end date is not later than the current end date<br>**Why:** The request is not a domain extension.<br>**Violated prerequisite:** An extension must increase the end date. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forlengAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `START_ETTER_SLUTT` | the new end date is before the persisted start date<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Start must not follow end. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `SLUTTDATO_GRENSE_NÅDD` | the new end date is after 2089-12-31<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The upper end-date bound is 2089-12-31. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `VARIGHET_FOR_LANG_ARBEIDSTRENING` | the resulting work-training duration exceeds 18 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Work training is limited to 18 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/ArbeidstreningStartOgSluttDatoStrategy.java — ArbeidstreningStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `VARIGHET_FOR_LANG_INKLUDERINGSTILSKUDD` | the resulting inclusion-subsidy duration exceeds 12 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Inclusion subsidy is limited to its implemented maximum. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/InkluderingstilskuddStartOgSluttDatoStrategy.java — InkluderingstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `VARIGHET_FOR_LANG_MENTOR_36_MND` | the resulting adapted-effort mentor duration exceeds 36 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The adapted-effort mentor maximum is 36 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MentorStartOgSluttDatoStrategy.java — MentorStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `VARIGHET_FOR_LANG_MENTOR_6_MND` | the resulting other mentor duration exceeds 6 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The ordinary mentor maximum is 6 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MentorStartOgSluttDatoStrategy.java — MentorStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `VARIGHET_FOR_LANG_MIDLERTIDIG_LONNSTILSKUDD_24_MND` | the resulting adapted-effort temporary subsidy exceeds 24 months<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** The adapted-effort maximum is 24 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MidlertidigLonnstilskuddStartOgSluttDatoStrategy.java — MidlertidigLonnstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `VARIGHET_FOR_LANG_MIDLERTIDIG_LONNSTILSKUDD_12_MND` | the resulting temporary subsidy exceeds the applicable 12-month maximum<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Situational or missing qualification group is limited to 12 months minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MidlertidigLonnstilskuddStartOgSluttDatoStrategy.java — MidlertidigLonnstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `SOMMERJOBB_FOR_TIDLIG` | the resulting summer-job date is before its permitted window<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Summer-job dates must remain in the summer window. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `SOMMERJOBB_FOR_SENT` | the resulting summer-job date is after its permitted window<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Summer-job dates must remain in the summer window. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run agreement extension | `SOMMERJOBB_FOR_LANG_VARIGHET` | the resulting summer job exceeds four weeks<br>**Why:** The selected date strategy rejects the resulting period.<br>**Violated prerequisite:** Summer jobs are limited to four weeks minus one day. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/20`
- Behavior outcome checklist summary: `0/21`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 20 concrete failure branches.
- Recommended test IDs that close the gap: `B31-HP`, `B31-F01`, `B31-F02`, `B31-F03`, `B31-F04`, `B31-F05`, `B31-F06`, `B31-F07`, `B31-F08`, `B31-F09`, `B31-F10`, `B31-F11`, `B31-F12`, `B31-F13`, `B31-F14`, `B31-F15`, `B31-F16`, `B31-F17`, `B31-F18`, `B31-F19`, `B31-F20`.

### `B32`: `Subsidy calculation change`

- Business goal: Creates a new approved version with changed monthly salary, holiday pay rate, employer tax, and pension rate, then recalculates unhandled subsidy amounts.
- Starting point: Existing service state
- Expected business result: Creates a new approved version with changed monthly salary, holiday pay rate, employer tax, and pension rate, then recalculates unhandled subsidy amounts.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | change subsidy calculation | `POST /avtaler/{avtaleId}/endre-tilskuddsberegning` with path `avtaleId`; body `EndreTilskuddsberegning` with calculation fields; advisor caller to complete the domain transition | Yes | No | No | `test_110_postOnEndre_tilskuddsberegningReturns400`, `test_173_postOnEndre_tilskuddsberegningReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B32. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| change subsidy calculation | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change subsidy calculation | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change subsidy calculation | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change subsidy calculation | `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE` | the agreement is not temporary wage subsidy, permanent wage subsidy, or summer job<br>**Why:** Only subsidy-backed measures support this calculation change.<br>**Violated prerequisite:** The agreement must use a supported subsidy measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.krevEnAvTiltakstyper`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change subsidy calculation | `KAN_IKKE_ENDRE_OKONOMI_IKKE_GODKJENT_AVTALE` | the agreement lacks advisor approval<br>**Why:** Economy changes are post-approval version changes.<br>**Violated prerequisite:** Advisor approval must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreTilskuddsberegning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change subsidy calculation | `KAN_IKKE_ENDRE_OKONOMI_UGYLDIG_INPUT` | `arbeidsgiveravgift` is missing<br>**Why:** The calculation-change guard requires this input before creating a new approved version.<br>**Violated prerequisite:** `arbeidsgiveravgift` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreTilskuddsberegning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change subsidy calculation | `KAN_IKKE_ENDRE_OKONOMI_UGYLDIG_INPUT` | `feriepengesats` is missing<br>**Why:** The calculation-change guard requires this input before creating a new approved version.<br>**Violated prerequisite:** `feriepengesats` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreTilskuddsberegning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change subsidy calculation | `KAN_IKKE_ENDRE_OKONOMI_UGYLDIG_INPUT` | `manedslonn` is missing<br>**Why:** The calculation-change guard requires this input before creating a new approved version.<br>**Violated prerequisite:** `manedslonn` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreTilskuddsberegning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change subsidy calculation | `KAN_IKKE_ENDRE_OKONOMI_UGYLDIG_INPUT` | `otpSats` is missing<br>**Why:** The calculation-change guard requires this input before creating a new approved version.<br>**Violated prerequisite:** `otpSats` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreTilskuddsberegning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/9`
- Behavior outcome checklist summary: `0/10`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 9 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B32-HP`, `B32-F01`, `B32-F02`, `B32-F03`, `B32-F04`, `B32-F05`, `B32-F06`, `B32-F07`, `B32-F08`, `B32-F09`, `B32-V01`.

### `B33`: `Dry-run subsidy calculation change`

- Business goal: Returns the would-be updated agreement after subsidy calculation changes without saving.
- Starting point: Existing service state
- Expected business result: Returns the would-be updated agreement after subsidy calculation changes without saving.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | dry-run subsidy calculation change | `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run` with path `avtaleId`; body `EndreTilskuddsberegning`; advisor caller to validate and preview the transition without saving it | Yes | No | No | `test_129_postOnEndre_tilskuddsberegning_dry_runReturns400`, `test_174_postOnEndre_tilskuddsberegning_dry_runReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| dry-run subsidy calculation change | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run subsidy calculation change | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run subsidy calculation change | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run subsidy calculation change | `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE` | the agreement is not temporary wage subsidy, permanent wage subsidy, or summer job<br>**Why:** Only subsidy-backed measures support this calculation change.<br>**Violated prerequisite:** The agreement must use a supported subsidy measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.krevEnAvTiltakstyper`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run subsidy calculation change | `KAN_IKKE_ENDRE_OKONOMI_IKKE_GODKJENT_AVTALE` | the agreement lacks advisor approval<br>**Why:** Economy changes are post-approval version changes.<br>**Violated prerequisite:** Advisor approval must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreTilskuddsberegning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run subsidy calculation change | `KAN_IKKE_ENDRE_OKONOMI_UGYLDIG_INPUT` | `arbeidsgiveravgift` is missing<br>**Why:** The calculation-change guard requires this input before creating a new approved version.<br>**Violated prerequisite:** `arbeidsgiveravgift` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreTilskuddsberegning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run subsidy calculation change | `KAN_IKKE_ENDRE_OKONOMI_UGYLDIG_INPUT` | `feriepengesats` is missing<br>**Why:** The calculation-change guard requires this input before creating a new approved version.<br>**Violated prerequisite:** `feriepengesats` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreTilskuddsberegning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run subsidy calculation change | `KAN_IKKE_ENDRE_OKONOMI_UGYLDIG_INPUT` | `manedslonn` is missing<br>**Why:** The calculation-change guard requires this input before creating a new approved version.<br>**Violated prerequisite:** `manedslonn` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreTilskuddsberegning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run subsidy calculation change | `KAN_IKKE_ENDRE_OKONOMI_UGYLDIG_INPUT` | `otpSats` is missing<br>**Why:** The calculation-change guard requires this input before creating a new approved version.<br>**Violated prerequisite:** `otpSats` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreTilskuddsberegning`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/9`
- Behavior outcome checklist summary: `0/10`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 9 concrete failure branches.
- Recommended test IDs that close the gap: `B33-HP`, `B33-F01`, `B33-F02`, `B33-F03`, `B33-F04`, `B33-F05`, `B33-F06`, `B33-F07`, `B33-F08`, `B33-F09`.

### `B34`: `Contact information change`

- Business goal: Creates a new approved version with changed participant, advisor, employer, and refund contact information.
- Starting point: Existing service state
- Expected business result: Creates a new approved version with changed participant, advisor, employer, and refund contact information.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | change contact information | `POST /avtaler/{avtaleId}/endre-kontaktinfo` with path `avtaleId`; body `EndreKontaktInformasjon` with participant, advisor, and employer contact fields; advisor caller to complete the domain transition | Yes | No | No | `test_114_postOnEndre_kontaktinfoReturns400`, `test_180_postOnEndre_kontaktinfoReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B34. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| change contact information | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change contact information | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change contact information | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change contact information | `KAN_IKKE_ENDRE_KONTAKTINFO_GRUNN_IKKE_GODKJENT_AVTALE` | the agreement lacks advisor approval<br>**Why:** The operation is a post-approval version change.<br>**Violated prerequisite:** Advisor approval must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreKontaktInformasjon`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change contact information | `KAN_IKKE_ENDRE_KONTAKTINFO_GRUNN_MANGLER` | `deltakerFornavn` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `deltakerFornavn` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreKontaktInformasjon`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change contact information | `KAN_IKKE_ENDRE_KONTAKTINFO_GRUNN_MANGLER` | `deltakerEtternavn` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `deltakerEtternavn` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreKontaktInformasjon`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change contact information | `KAN_IKKE_ENDRE_KONTAKTINFO_GRUNN_MANGLER` | `deltakerTlf` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `deltakerTlf` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreKontaktInformasjon`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change contact information | `KAN_IKKE_ENDRE_KONTAKTINFO_GRUNN_MANGLER` | `veilederFornavn` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `veilederFornavn` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreKontaktInformasjon`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change contact information | `KAN_IKKE_ENDRE_KONTAKTINFO_GRUNN_MANGLER` | `veilederEtternavn` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `veilederEtternavn` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreKontaktInformasjon`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change contact information | `KAN_IKKE_ENDRE_KONTAKTINFO_GRUNN_MANGLER` | `veilederTlf` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `veilederTlf` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreKontaktInformasjon`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change contact information | `KAN_IKKE_ENDRE_KONTAKTINFO_GRUNN_MANGLER` | `arbeidsgiverFornavn` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `arbeidsgiverFornavn` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreKontaktInformasjon`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change contact information | `KAN_IKKE_ENDRE_KONTAKTINFO_GRUNN_MANGLER` | `arbeidsgiverEtternavn` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `arbeidsgiverEtternavn` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreKontaktInformasjon`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change contact information | `KAN_IKKE_ENDRE_KONTAKTINFO_GRUNN_MANGLER` | `arbeidsgiverTlf` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `arbeidsgiverTlf` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreKontaktInformasjon`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/13`
- Behavior outcome checklist summary: `0/14`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 13 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B34-HP`, `B34-F01`, `B34-F02`, `B34-F03`, `B34-F04`, `B34-F05`, `B34-F06`, `B34-F07`, `B34-F08`, `B34-F09`, `B34-F10`, `B34-F11`, `B34-F12`, `B34-F13`, `B34-V01`.

### `B35`: `Job description change`

- Business goal: Creates a new approved version with changed title, work tasks, occupation code, position percent, and days per week.
- Starting point: Existing service state
- Expected business result: Creates a new approved version with changed title, work tasks, occupation code, position percent, and days per week.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | change job description | `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse` with path `avtaleId`; body `EndreStillingsbeskrivelse` with job-title, task, occupation, percentage, and workday fields; advisor caller to complete the domain transition | Yes | No | No | `test_111_postOnEndre_stillingbeskrivelseReturns400`, `test_175_postOnEndre_stillingbeskrivelseReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B35. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| change job description | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change job description | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change job description | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change job description | `KAN_IKKE_ENDRE_STILLINGSBESKRIVELSE_GRUNN_IKKE_GODKJENT_AVTALE` | the agreement lacks advisor approval<br>**Why:** The operation is a post-approval version change.<br>**Violated prerequisite:** Advisor approval must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreStillingsbeskrivelse`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change job description | `KAN_IKKE_ENDRE_STILLINGSBESKRIVELSE_GRUNN_MANGLER` | `stillingstittel` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `stillingstittel` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreStillingsbeskrivelse`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change job description | `KAN_IKKE_ENDRE_STILLINGSBESKRIVELSE_GRUNN_MANGLER` | `arbeidsoppgaver` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `arbeidsoppgaver` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreStillingsbeskrivelse`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change job description | `KAN_IKKE_ENDRE_STILLINGSBESKRIVELSE_GRUNN_MANGLER` | `stillingStyrk08` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `stillingStyrk08` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreStillingsbeskrivelse`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change job description | `KAN_IKKE_ENDRE_STILLINGSBESKRIVELSE_GRUNN_MANGLER` | `stillingKonseptId` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `stillingKonseptId` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreStillingsbeskrivelse`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change job description | `KAN_IKKE_ENDRE_STILLINGSBESKRIVELSE_GRUNN_MANGLER` | `stillingprosent` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `stillingprosent` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreStillingsbeskrivelse`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change job description | `KAN_IKKE_ENDRE_STILLINGSBESKRIVELSE_GRUNN_MANGLER` | `antallDagerPerUke` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `antallDagerPerUke` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreStillingsbeskrivelse`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/10`
- Behavior outcome checklist summary: `0/11`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 10 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B35-HP`, `B35-F01`, `B35-F02`, `B35-F03`, `B35-F04`, `B35-F05`, `B35-F06`, `B35-F07`, `B35-F08`, `B35-F09`, `B35-F10`, `B35-V01`.

### `B36`: `Follow-up and adaptation text change`

- Business goal: Creates a new approved version with changed follow-up and adaptation descriptions.
- Starting point: Existing service state
- Expected business result: Creates a new approved version with changed follow-up and adaptation descriptions.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | change follow-up and adaptation text | `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging` with path `avtaleId`; body `EndreOppfølgingOgTilrettelegging` with `oppfolging` and `tilrettelegging`; advisor caller to complete the domain transition | Yes | No | No | `test_112_postOnEndre_oppfolging_og_tilretteleggingReturns400`, `test_176_postOnEndre_oppfolging_og_tilretteleggingReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B36. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| change follow-up and adaptation text | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change follow-up and adaptation text | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change follow-up and adaptation text | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change follow-up and adaptation text | `KAN_IKKE_ENDRE_OPPFØLGING_OG_TILRETTELEGGING_GRUNN_IKKE_GODKJENT_AVTALE` | the agreement lacks advisor approval<br>**Why:** The operation is a post-approval version change.<br>**Violated prerequisite:** Advisor approval must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreOppfølgingOgTilrettelegging`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change follow-up and adaptation text | `KAN_IKKE_ENDRE_OPPFØLGING_OG_TILRETTELEGGING_GRUNN_MANGLER` | `oppfolging` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `oppfolging` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreOppfølgingOgTilrettelegging`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change follow-up and adaptation text | `KAN_IKKE_ENDRE_OPPFØLGING_OG_TILRETTELEGGING_GRUNN_MANGLER` | `tilrettelegging` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `tilrettelegging` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreOppfølgingOgTilrettelegging`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/6`
- Behavior outcome checklist summary: `0/7`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 6 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B36-HP`, `B36-F01`, `B36-F02`, `B36-F03`, `B36-F04`, `B36-F05`, `B36-F06`, `B36-V01`.

### `B37`: `Work-training goal replacement`

- Business goal: Replaces goals on an approved work-training agreement.
- Starting point: Existing service state
- Expected business result: Replaces goals on an approved work-training agreement.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | change work-training goals | `POST /avtaler/{avtaleId}/endre-maal` with path `avtaleId`; body `EndreMål` with non-empty `maal` list; advisor caller to complete the domain transition | Yes | No | No | `test_130_postOnEndre_maalReturns400`, `test_178_postOnEndre_maalReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B37. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| change work-training goals | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change work-training goals | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change work-training goals | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change work-training goals | `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE` | the agreement is not work training<br>**Why:** The aggregate restricts this versioned change to its matching measure.<br>**Violated prerequisite:** The agreement must have `ARBEIDSTRENING`. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.krevEnAvTiltakstyper`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change work-training goals | `KAN_IKKE_ENDRE_MAAL_IKKE_INNGAATT_AVTALE` | the agreement lacks advisor approval<br>**Why:** The operation is a post-approval version change.<br>**Violated prerequisite:** Advisor approval must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreMål`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change work-training goals | `KAN_IKKE_ENDRE_MAAL_TOM_LISTE` | the replacement goal list is empty<br>**Why:** The operation requires at least one goal.<br>**Violated prerequisite:** At least one work-training goal must remain. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreMål`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change work-training goals | `KAN_IKKE_ENDRE_MAAL_IKKE_BESKRIVELSE_ELLER_KATEGORI` | a replacement goal has no description<br>**Why:** Each goal is validated independently.<br>**Violated prerequisite:** Every goal must have a description. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreMål`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change work-training goals | `KAN_IKKE_ENDRE_MAAL_IKKE_BESKRIVELSE_ELLER_KATEGORI` | a replacement goal has no category<br>**Why:** Each goal is validated independently.<br>**Violated prerequisite:** Every goal must have a category. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreMål`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/8`
- Behavior outcome checklist summary: `0/9`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 8 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B37-HP`, `B37-F01`, `B37-F02`, `B37-F03`, `B37-F04`, `B37-F05`, `B37-F06`, `B37-F07`, `B37-F08`, `B37-V01`.

### `B38`: `Inclusion subsidy expense replacement`

- Business goal: Replaces inclusion subsidy expense lines on an approved inclusion subsidy agreement.
- Starting point: Existing service state
- Expected business result: Replaces inclusion subsidy expense lines on an approved inclusion subsidy agreement.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | change inclusion subsidy expenses | `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd` with path `avtaleId`; body `EndreInkluderingstilskudd` with expense rows; advisor caller to complete the domain transition | Yes | No | No | `test_115_postOnEndre_inkluderingstilskuddReturns400`, `test_181_postOnEndre_inkluderingstilskuddReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B38. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| change inclusion subsidy expenses | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change inclusion subsidy expenses | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change inclusion subsidy expenses | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change inclusion subsidy expenses | `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE` | the agreement is not inclusion subsidy<br>**Why:** The aggregate restricts this versioned change to its matching measure.<br>**Violated prerequisite:** The agreement must have `INKLUDERINGSTILSKUDD`. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.krevEnAvTiltakstyper`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change inclusion subsidy expenses | `KAN_IKKE_ENDRE_INKLUDERINGSTILSKUDD_IKKE_INNGAATT_AVTALE` | the agreement lacks advisor approval<br>**Why:** The operation is a post-approval version change.<br>**Violated prerequisite:** Advisor approval must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreInkluderingstilskudd`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change inclusion subsidy expenses | `KAN_IKKE_ENDRE_INKLUDERINGSTILSKUDD_TOM_LISTE` | the replacement expense list is empty<br>**Why:** At least one expense is required.<br>**Violated prerequisite:** The inclusion-subsidy expense list must not be empty. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreInkluderingstilskudd`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change inclusion subsidy expenses | `INKLUDERINGSTILSKUDD_SUM_FOR_HØY` | the replacement expense total exceeds 136000<br>**Why:** The post-approval change applies its explicit maximum.<br>**Violated prerequisite:** The replacement total must be at most 136000. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreInkluderingstilskudd`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change inclusion subsidy expenses | `KAN_IKKE_ENDRE_INKLUDERINGSTILSKUDD_IKKE_BELOP_ELLER_TYPE` | an expense line has no amount<br>**Why:** Each replacement line is validated independently.<br>**Violated prerequisite:** Every expense line must have an amount. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreInkluderingstilskudd`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change inclusion subsidy expenses | `KAN_IKKE_ENDRE_INKLUDERINGSTILSKUDD_IKKE_BELOP_ELLER_TYPE` | an expense line has no expense type<br>**Why:** Each replacement line is validated independently.<br>**Violated prerequisite:** Every expense line must have an expense type. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreInkluderingstilskudd`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change inclusion subsidy expenses | `KAN_IKKE_ENDRE_INKLUDERINGSTILSKUDD_TOM_LISTE` | the count of submitted existing expense IDs differs from the persisted previous-version expense count<br>**Why:** The aggregate detects a stale/incomplete client representation before versioning.<br>**Violated prerequisite:** Every persisted previous expense must be represented by ID. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreInkluderingstilskudd`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/10`
- Behavior outcome checklist summary: `0/11`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 10 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B38-HP`, `B38-F01`, `B38-F02`, `B38-F03`, `B38-F04`, `B38-F05`, `B38-F06`, `B38-F07`, `B38-F08`, `B38-F09`, `B38-F10`, `B38-V01`.

### `B39`: `Mentor details change`

- Business goal: Creates a new approved version with changed mentor name, phone, tasks, hours, and hourly wage.
- Starting point: Existing service state
- Expected business result: Creates a new approved version with changed mentor name, phone, tasks, hours, and hourly wage.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | change mentor details | `POST /avtaler/{avtaleId}/endre-om-mentor` with path `avtaleId`; body `EndreOmMentor` with mentor contact, wage, hours, and task fields; advisor caller to complete the domain transition | Yes | No | No | `test_113_postOnEndre_om_mentorReturns400`, `test_177_postOnEndre_om_mentorReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B39. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| change mentor details | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change mentor details | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change mentor details | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change mentor details | `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE` | the agreement is not a mentor agreement<br>**Why:** The aggregate restricts this versioned change to its matching measure.<br>**Violated prerequisite:** The agreement must have `MENTOR`. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.krevEnAvTiltakstyper`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change mentor details | `KAN_IKKE_ENDRE_OM_MENTOR_IKKE_INNGAATT_AVTALE` | the agreement lacks advisor approval<br>**Why:** The operation is a post-approval version change.<br>**Violated prerequisite:** Advisor approval must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreOmMentor`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change mentor details | `KAN_IKKE_ENDRE_OM_MENTOR_UGYLDIG_INPUT` | `mentorFornavn` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `mentorFornavn` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreOmMentor`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change mentor details | `KAN_IKKE_ENDRE_OM_MENTOR_UGYLDIG_INPUT` | `mentorEtternavn` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `mentorEtternavn` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreOmMentor`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change mentor details | `KAN_IKKE_ENDRE_OM_MENTOR_UGYLDIG_INPUT` | `mentorTlf` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `mentorTlf` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreOmMentor`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change mentor details | `KAN_IKKE_ENDRE_OM_MENTOR_UGYLDIG_INPUT` | `mentorTimelonn` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `mentorTimelonn` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreOmMentor`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change mentor details | `KAN_IKKE_ENDRE_OM_MENTOR_UGYLDIG_INPUT` | `mentorAntallTimer` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `mentorAntallTimer` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreOmMentor`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change mentor details | `KAN_IKKE_ENDRE_OM_MENTOR_UGYLDIG_INPUT` | `mentorOppgaver` is missing<br>**Why:** The named change guard requires this concrete field before creating a new approved version.<br>**Violated prerequisite:** `mentorOppgaver` must be supplied. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreOmMentor`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/11`
- Behavior outcome checklist summary: `0/12`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 11 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B39-HP`, `B39-F01`, `B39-F02`, `B39-F03`, `B39-F04`, `B39-F05`, `B39-F06`, `B39-F07`, `B39-F08`, `B39-F09`, `B39-F10`, `B39-F11`, `B39-V01`.

### `B40`: `Cost center change`

- Business goal: Sets the cost-center unit and unit name on unhandled or rejected subsidy periods.
- Starting point: Existing service state
- Expected business result: Sets the cost-center unit and unit name on unhandled or rejected subsidy periods.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | change cost center | `POST /avtaler/{avtaleId}/endre-kostnadssted` with path `avtaleId`; body `EndreKostnadsstedRequest` with `enhet`; advisor caller to complete the domain transition | Yes | No | No | `test_131_postOnEndre_kostnadsstedReturns400`, `test_179_postOnEndre_kostnadsstedReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B40. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| change cost center | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change cost center | `ENHET_FINNES_IKKE` | Norg2 returns no unit for the requested cost center<br>**Why:** The requested cost center has no domain unit name.<br>**Violated prerequisite:** The cost-center unit must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.oppdatereKostnadssted`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change cost center | `TILSKUDDSPERIODE_ER_IKKE_SATT` | the agreement has no active untreated or rejected subsidy period<br>**Why:** There is no mutable period on which to apply the cost center.<br>**Violated prerequisite:** At least one active untreated or rejected period must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.oppdatereKostnadssted`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change cost center | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| change cost center | `KAN_IKKE_OPPDATERE_KOSTNADSSTED_INGAATT_AVTALE` | the agreement is already entered<br>**Why:** The aggregate freezes cost-center assignment after entry.<br>**Violated prerequisite:** The agreement must not be entered. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.oppdatereKostnadsstedForTilskuddsperioder`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/5`
- Behavior outcome checklist summary: `0/6`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 5 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B40-HP`, `B40-F01`, `B40-F02`, `B40-F03`, `B40-F04`, `B40-F05`, `B40-V01`.

### `B41`: `Arena migration date adjustment`

- Business goal: Recomputes subsidy periods around a migration date and saves or updates the agreement’s Arena cleanup marker.
- Starting point: Existing service state
- Expected business result: Recomputes subsidy periods around a migration date and saves or updates the agreement’s Arena cleanup marker.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | adjust Arena migration date | `POST /avtaler/{avtaleId}/juster-arena-migreringsdato` with path `avtaleId`; body `JusterArenaMigreringsdato` with `migreringsdato`; advisor caller to complete the domain transition | Yes | No | No | `test_123_postOnJuster_arena_migreringsdatoReturns400`, `test_162_postOnJuster_arena_migreringsdatoReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B41. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| adjust Arena migration date | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| adjust Arena migration date | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| adjust Arena migration date | `FORLENG_MIDLERTIDIG_IKKE_TILGJENGELIG` | the persisted reduced-percentage date lies after the entire agreement period being regenerated<br>**Why:** The subsidy-period calculator has no valid branch for a period wholly before the stored reduction date.<br>**Violated prerequisite:** The stored reduction date must be consistent with the agreement start and end dates. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/RegnUtTilskuddsperioderForAvtale.java — RegnUtTilskuddsperioderForAvtale.beregnTilskuddsperioderForAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| adjust Arena migration date | `KAN_IKKE_ENDRE_ARENA_MIGRERINGSDATO_INNGAATT_AVTALE` | the agreement is already entered<br>**Why:** The persisted migration marker cannot be moved after entry.<br>**Violated prerequisite:** The agreement must not be entered. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.justerArenaMigreringsdato`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/4`
- Behavior outcome checklist summary: `0/5`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 4 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B41-HP`, `B41-F01`, `B41-F02`, `B41-F03`, `B41-F04`, `B41-V01`.

### `B42`: `Dry-run Arena migration date adjustment`

- Business goal: Returns the would-be agreement after recalculating periods around the migration date without saving it.
- Starting point: Existing service state
- Expected business result: Returns the would-be agreement after recalculating periods around the migration date without saving it.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | dry-run Arena migration date adjustment | `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run` with path `avtaleId`; body `JusterArenaMigreringsdato` with `migreringsdato`; advisor caller to validate and preview the transition without saving it | Yes | No | No | `test_187_postOnDry_runReturns400`, `test_194_postOnDry_runReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| dry-run Arena migration date adjustment | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run Arena migration date adjustment | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| dry-run Arena migration date adjustment | `FORLENG_MIDLERTIDIG_IKKE_TILGJENGELIG` | the persisted reduced-percentage date lies after the entire agreement period being regenerated<br>**Why:** The subsidy-period calculator has no valid branch for a period wholly before the stored reduction date.<br>**Violated prerequisite:** The stored reduction date must be consistent with the agreement start and end dates. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/RegnUtTilskuddsperioderForAvtale.java — RegnUtTilskuddsperioderForAvtale.beregnTilskuddsperioderForAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 3 concrete failure branches.
- Recommended test IDs that close the gap: `B42-HP`, `B42-F01`, `B42-F02`, `B42-F03`.

### `B43`: `Employer account number lookup`

- Business goal: Returns the employer’s bank account number for the agreement’s company.
- Starting point: Pre-existing service/upstream state required
- Expected business result: Returns the employer’s bank account number for the agreement’s company.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | get employer account number | `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver` with path `avtaleId`; authenticated caller with required role and access to return the requested view without changing persisted state | Yes | No | No | `test_94_getOnKontonummer_arbeidsgiverReturns400`, `test_96_getOnKontonummer_arbeidsgiverReturns400`, `test_143_getOnKontonummer_arbeidsgiverReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| get employer account number | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| get employer account number | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| get employer account number | `KONTOREGISTER_FEIL_BEDRIFT_IKKE_FUNNET` | the account register has no employer account for the agreement's company<br>**Why:** A business-significant not-found result is mapped to the specific company-not-found code.<br>**Violated prerequisite:** The persisted agreement company must have an account-register entry. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/okonomi/KontoregisterServiceImpl.java — KontoregisterServiceImpl.hentKontonummer`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 3 concrete failure branches.
- Recommended test IDs that close the gap: `B43-HP`, `B43-F01`, `B43-F02`, `B43-F03`.

### `B44`: `Agreement PDF download`

- Business goal: Returns a PDF representation of an advisor-approved agreement.
- Starting point: Existing service state
- Expected business result: Returns a PDF representation of an advisor-approved agreement.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | download agreement PDF | `GET /avtaler/{avtaleId}/pdf` with path `avtaleId`; authenticated caller with required role and access to return the requested view without changing persisted state | Yes | No | No | `test_93_getOnPdfReturns400`, `test_142_getOnPdfReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| download agreement PDF | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| download agreement PDF | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| download agreement PDF | `KAN_IKKE_LASTE_NED_PDF` | the agreement lacks advisor approval<br>**Why:** PDF generation is available only for advisor-approved content.<br>**Violated prerequisite:** Advisor approval must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.hentAvtalePdf`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 3 concrete failure branches.
- Recommended test IDs that close the gap: `B44-HP`, `B44-F01`, `B44-F02`, `B44-F03`.

### `B45`: `Salesforce dialog visibility check`

- Business goal: Returns `true` when the agreement belongs to a configured Salesforce office, has `MIDLERTIDIG_LONNSTILSKUDD`, and is `GJENNOMFØRES` or `AVSLUTTET`; otherwise returns `false`.
- Starting point: Existing service state
- Expected business result: Returns `true` when the agreement belongs to a configured Salesforce office, has `MIDLERTIDIG_LONNSTILSKUDD`, and is `GJENNOMFØRES` or `AVSLUTTET`; otherwise returns `false`.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | check Salesforce dialog visibility | `GET /avtaler/{avtaleId}/vis-salesforce-dialog` with path `avtaleId`; authenticated caller with required role and access to return the requested view without changing persisted state | Yes | No | No | `test_91_getOnVis_salesforce_dialogReturns400`, `test_140_getOnVis_salesforce_dialogReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| check Salesforce dialog visibility | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| check Salesforce dialog visibility | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 2 concrete failure branches.
- Recommended test IDs that close the gap: `B45-HP`, `B45-F01`, `B45-F02`.

### `B46`: `Follow-up unit refresh`

- Business goal: Refreshes participant data, follow-up status, geographic unit, and follow-up unit name, then saves the agreement.
- Starting point: Existing service state
- Expected business result: Refreshes participant data, follow-up status, geographic unit, and follow-up unit name, then saves the agreement.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | refresh follow-up unit | `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet` with path `avtaleId`; authenticated caller with required role and access to complete the domain transition | Yes | No | No | `test_102_postOnOppdaterOppf_lgingsEnhetReturns400`, `test_160_postOnOppdaterOppf_lgingsEnhetReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B46. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| refresh follow-up unit | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| refresh follow-up unit | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| refresh follow-up unit | `IKKE_TILGANG_TIL_DELTAKER` | PDL reports address protection code 6 for the participant<br>**Why:** The refresh reuses the advisor creation person-data guard.<br>**Violated prerequisite:** The advisor may not refresh this protected participant. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.hentPersonDataForOpprettelseAvAvtale/sjekkKode6`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| refresh follow-up unit | `HENTING_AV_INNSATS_BEHOV_FEILET` | Arena has no complete follow-up status<br>**Why:** The refreshed Arena business result is incompatible with the agreement.<br>**Violated prerequisite:** The participant must retain complete, eligible follow-up status. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| refresh follow-up unit | `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET` | Arena returns an invalid qualification group<br>**Why:** The refreshed Arena business result is incompatible with the agreement.<br>**Violated prerequisite:** The participant must retain complete, eligible follow-up status. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| refresh follow-up unit | `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNTILSKUDD_OG_SOMMERJOBB_FEIL` | the qualification group is ineligible for the temporary-subsidy, summer-job, or mentor measure<br>**Why:** The refreshed Arena business result is incompatible with the agreement.<br>**Violated prerequisite:** The participant must retain complete, eligible follow-up status. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| refresh follow-up unit | `KVALIFISERINGSGRUPPE_VARIG_LONNTILSKUDD_FEIL` | the qualification group is ineligible for permanent wage subsidy<br>**Why:** The refreshed Arena business result is incompatible with the agreement.<br>**Violated prerequisite:** The participant must retain complete, eligible follow-up status. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/7`
- Behavior outcome checklist summary: `0/8`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 7 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B46-HP`, `B46-F01`, `B46-F02`, `B46-F03`, `B46-F04`, `B46-F05`, `B46-F06`, `B46-F07`, `B46-V01`.

### `B47`: `Advisor takeover of agreement`

- Business goal: Assigns the logged-in advisor as the agreement’s advisor and refreshes follow-up data.
- Starting point: Existing service state
- Expected business result: Assigns the logged-in advisor as the agreement’s advisor and refreshes follow-up data.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | take over agreement as advisor | `PUT /avtaler/{avtaleId}/overta` with path `avtaleId`; authenticated caller with required role and access to complete the domain transition | Yes | No | No | `test_138_putOnOvertaReturns400`, `test_185_putOnOvertaReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B47. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| take over agreement as advisor | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| take over agreement as advisor | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| take over agreement as advisor | `HENTING_AV_INNSATS_BEHOV_FEILET` | Arena has no complete follow-up status<br>**Why:** Takeover refreshes and validates follow-up status before ownership changes.<br>**Violated prerequisite:** The participant must be eligible for the agreement's measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| take over agreement as advisor | `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET` | Arena returns an invalid qualification group<br>**Why:** Takeover refreshes and validates follow-up status before ownership changes.<br>**Violated prerequisite:** The participant must be eligible for the agreement's measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| take over agreement as advisor | `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNTILSKUDD_OG_SOMMERJOBB_FEIL` | the qualification group is ineligible for the selected temporary-subsidy, summer-job, or mentor measure<br>**Why:** Takeover refreshes and validates follow-up status before ownership changes.<br>**Violated prerequisite:** The participant must be eligible for the agreement's measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| take over agreement as advisor | `KVALIFISERINGSGRUPPE_VARIG_LONNTILSKUDD_FEIL` | the qualification group is ineligible for permanent wage subsidy<br>**Why:** Takeover refreshes and validates follow-up status before ownership changes.<br>**Violated prerequisite:** The participant must be eligible for the agreement's measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| take over agreement as advisor | `ER_ALLEREDE_VEILEDER` | the logged-in advisor is already assigned to the agreement<br>**Why:** The operation requires an actual advisor ownership change.<br>**Violated prerequisite:** The new advisor must differ from the persisted advisor. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.overtaAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| take over agreement as advisor | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/8`
- Behavior outcome checklist summary: `0/9`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 8 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B47-HP`, `B47-F01`, `B47-F02`, `B47-F03`, `B47-F04`, `B47-F05`, `B47-F06`, `B47-F07`, `B47-F08`, `B47-V01`.

### `B48`: `Agreement annulment`

- Business goal: Annuls an agreement and annuls/removes eligible subsidy periods; if reason is `Feilregistrering`, the agreement is also marked as incorrectly registered.
- Starting point: Existing service state
- Expected business result: Annuls an agreement and annuls/removes eligible subsidy periods; if reason is `Feilregistrering`, the agreement is also marked as incorrectly registered.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | annul agreement | `POST /avtaler/{avtaleId}/annuller` with path `avtaleId`; authenticated caller with required role and access to complete the domain transition | Yes | No | No | `test_118_postOnAnnullerReturns400`, `test_133_postOnAnnullerReturns400`, `test_184_postOnAnnullerReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B48. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| annul agreement | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| annul agreement | `SAMTIDIGE_ENDRINGER` | the supplied concurrency timestamp is absent or stale<br>**Why:** Annulment must target the latest aggregate state.<br>**Violated prerequisite:** The caller must use the current `sistEndret` value. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| annul agreement | `AVTALE_INNEHOLDER_UTBETALT_TILSKUDDSPERIODE` | an active subsidy period has refund status `UTBETALT` or `KORRIGERT`<br>**Why:** Paid/corrected financial state prevents agreement annulment.<br>**Violated prerequisite:** No paid or corrected subsidy period may exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtalenInneholderUtbetaltTilskuddsperiode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| annul agreement | `AVTALE_INNEHOLDER_TILSKUDDSPERIODE_MED_GODKJENT_REFUSJON` | an active subsidy period has refund status `SENDT_KRAV`, `GODKJENT_MINUSBELØP`, or `GODKJENT_NULLBELØP`<br>**Why:** A committed refund prevents agreement annulment.<br>**Violated prerequisite:** No subsidy period with an approved/sent refund may exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtalenInneholderUtbetaltTilskuddsperiode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| annul agreement | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/5`
- Behavior outcome checklist summary: `0/6`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 5 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B48-HP`, `B48-F01`, `B48-F02`, `B48-F03`, `B48-F04`, `B48-F05`, `B48-V01`.

### `B49`: `Agreement soft deletion`

- Business goal: Marks the agreement as deleted/hidden.
- Starting point: Existing service state
- Expected business result: Marks the agreement as deleted/hidden.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | soft-delete agreement | `POST /avtaler/{avtaleId}/slettemerk` with path `avtaleId`; authenticated caller with required role and access to complete the domain transition | Yes | No | No | `test_98_postOnSlettemerkReturns400`, `test_156_postOnSlettemerkReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B49. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| soft-delete agreement | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController repository lookup`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| soft-delete agreement | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 2 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B49-HP`, `B49-F01`, `B49-F02`, `B49-V01`.

### `B50`: `Employer Min Side agreement listing`

- Business goal: Returns all agreements for a company that the logged-in employer can view.
- Starting point: Existing service state
- Expected business result: Returns all agreements for a company that the logged-in employer can view.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | list employer agreements | `GET /avtaler/min-side-arbeidsgiver` with query/path values documented by the endpoint; cookie `innlogget-part` when required to return the requested view without changing persisted state | Yes | No | No | `test_32_getOnMin_side_arbeidsgiverReturns400`, `test_43_getOnMin_side_arbeidsgiverReturns400`, `test_69_getOnMin_side_arbeidsgiverReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof.
- Recommended test IDs that close the gap: `B50-HP`.

### `B51`: `Decision-maker work queue listing`

- Business goal: Returns paginated agreements whose subsidy periods match decision-maker filters and the decision-maker’s NAV units.
- Starting point: Existing service state
- Expected business result: Returns paginated agreements whose subsidy periods match decision-maker filters and the decision-maker’s NAV units.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | list decision-maker agreements | `GET /avtaler/beslutter-liste` with query/path values documented by the endpoint; cookie `innlogget-part` when required to return the requested view without changing persisted state | Yes | No | No | `test_35_getOnBeslutter_listeReturns400`, `test_36_getOnBeslutter_listeReturns400`, `test_37_getOnBeslutter_listeReturns400` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| list decision-maker agreements | `NAV_ENHET_IKKE_FUNNET` | the decision-maker has no NAV units<br>**Why:** The work queue cannot establish the decision-maker's business unit scope.<br>**Violated prerequisite:** At least one decision-maker NAV unit must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.finnGodkjenteAvtalerMedTilskuddsperiodestatusOgNavEnheterListe`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 1 concrete failure branches.
- Recommended test IDs that close the gap: `B51-HP`, `B51-F01`.

### `B52`: `Logged-in user context lookup`

- Business goal: Returns role-specific information for the logged-in user.
- Starting point: Existing service state
- Expected business result: Returns role-specific information for the logged-in user.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | get logged-in user | `GET /innlogget-bruker` with authenticated caller and endpoint-specific required request values to return the requested view without changing persisted state | Yes | No | No | `test_10_getOnInnlogget_brukerReturns400`, `test_17_getOnInnlogget_brukerReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof.
- Recommended test IDs that close the gap: `B52-HP`.

### `B53`: `Employer organization lookup`

- Business goal: Returns organization data for an employer unit.
- Starting point: Pre-existing service/upstream state required
- Expected business result: Returns organization data for an employer unit.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | look up organization | `GET /organisasjoner` with authenticated caller and endpoint-specific required request values to return the requested view without changing persisted state | Yes | No | No | `test_9_getOnOrganisasjonerReturns400`, `test_13_getOnOrganisasjonerReturns400`, `test_16_getOnOrganisasjonerReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| look up organization | `EnhetErJuridiskException` | Ereg identifies the number as a legal entity<br>**Why:** The lookup requires a business unit rather than a top-level legal entity.<br>**Violated prerequisite:** The number must identify a business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| look up organization | `EnhetErOrganisasjonsleddException` | Ereg identifies the number as an organizational link<br>**Why:** The lookup rejects organizational-link entities.<br>**Violated prerequisite:** The number must identify a supported business unit. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| look up organization | `EnhetFinnesIkkeException` | Ereg cannot find the organization number<br>**Why:** The requested business entity is absent.<br>**Violated prerequisite:** The organization must exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 3 concrete failure branches.
- Recommended test IDs that close the gap: `B53-HP`, `B53-F01`, `B53-F02`, `B53-F03`.

### `B54`: `Altinn rights request URL lookup`

- Business goal: Returns URLs that let an employer request Altinn rights for each supported measure type.
- Starting point: Existing service state
- Expected business result: Returns URLs that let an employer request Altinn rights for each supported measure type.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | get Altinn rights request URLs | `GET /be-om-altinn-rettighet-urler` with authenticated caller and endpoint-specific required request values to return the requested view without changing persisted state | Yes | Yes | Yes | `test_3_getOnBe_om_altinn_rettighet_urlerReturnsObject` asserts HTTP 200 and documented response content; no state mutation is required. | BeOmAltinnRettighetUrlerController.beOmRettighetUrler, line 22: 26/26 instructions and 7/7 lines. |

- Happy-path item: `Covered` — `test_3_getOnBe_om_altinn_rettighet_urlerReturnsObject` completes the single required stateless operation and asserts its documented response.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`, counting one happy path plus all documented failures
- Status and confidence: `Covered / High`
- Exact gap: none for the authoritative happy path; no failures or optional steps are documented.
- Recommended test IDs that close the gap: none.

### `B55`: `Combined code-list lookup`

- Business goal: Returns both measure types and agreement statuses.
- Starting point: Existing service state
- Expected business result: Returns both measure types and agreement statuses.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | get all code lists | `GET /kodeverk` with authenticated caller and endpoint-specific required request values to return the requested view without changing persisted state | Yes | Yes | Yes | `test_1_getOnKodeverkReturnsObject` asserts HTTP 200 and documented response content; no state mutation is required. | KodeverkController.get, line 25: 18/18 instructions and 4/4 lines. |

- Happy-path item: `Covered` — `test_1_getOnKodeverkReturnsObject` completes the single required stateless operation and asserts its documented response.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`, counting one happy path plus all documented failures
- Status and confidence: `Covered / High`
- Exact gap: none for the authoritative happy path; no failures or optional steps are documented.
- Recommended test IDs that close the gap: none.

### `B56`: `Agreement status code-list lookup`

- Business goal: Returns all `Status` enum names and descriptions.
- Starting point: Existing service state
- Expected business result: Returns all `Status` enum names and descriptions.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | get status code list | `GET /kodeverk/statuser` with authenticated caller and endpoint-specific required request values to return the requested view without changing persisted state | Yes | Yes | Yes | `test_26_getOnStatuserReturns7Elements` asserts HTTP 200 and documented response content; no state mutation is required. | KodeverkController.statuser, line 33: 8/8 instructions and 2/2 lines; serialization lambda also covered. |

- Happy-path item: `Covered` — `test_26_getOnStatuserReturns7Elements` completes the single required stateless operation and asserts its documented response.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`, counting one happy path plus all documented failures
- Status and confidence: `Covered / High`
- Exact gap: none for the authoritative happy path; no failures or optional steps are documented.
- Recommended test IDs that close the gap: none.

### `B57`: `Measure type code-list lookup`

- Business goal: Returns all `Tiltakstype` enum names, descriptions, treatment themes, and optional Arena codes.
- Starting point: Existing service state
- Expected business result: Returns all `Tiltakstype` enum names, descriptions, treatment themes, and optional Arena codes.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | get measure type code list | `GET /kodeverk/tiltakstyper` with authenticated caller and endpoint-specific required request values to return the requested view without changing persisted state | Yes | Yes | Yes | `test_23_getOnTiltakstyperReturns6Elements` asserts HTTP 200 and documented response content; no state mutation is required. | KodeverkController.tiltakstyper, line 43: 8/8 instructions and 2/2 lines; mapping lambda has 33/33 instructions and 2/2 branches. |

- Happy-path item: `Covered` — `test_23_getOnTiltakstyperReturns6Elements` completes the single required stateless operation and asserts its documented response.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`, counting one happy path plus all documented failures
- Status and confidence: `Covered / High`
- Exact gap: none for the authoritative happy path; no failures or optional steps are documented.
- Recommended test IDs that close the gap: none.

### `B58`: `Feature toggle evaluation`

- Business goal: Returns enabled/disabled values for requested feature names.
- Starting point: Existing service state
- Expected business result: Returns enabled/disabled values for requested feature names.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | evaluate feature toggles | `GET /feature` with query list `feature` to return the requested view without changing persisted state | Yes | Yes | Yes | `test_2_getOnFeatureReturnsObject` asserts HTTP 200 and documented response content; no state mutation is required. | FeatureToggleController.feature, line 26: 5/5 instructions and 1/1 line; FeatureToggleService.hentFeatureToggles 9/9 instructions. |

- Happy-path item: `Covered` — `test_2_getOnFeatureReturnsObject` completes the single required stateless operation and asserts its documented response.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`, counting one happy path plus all documented failures
- Status and confidence: `Covered / High`
- Exact gap: none for the authoritative happy path; no failures or optional steps are documented.
- Recommended test IDs that close the gap: none.

### `B59`: `Feature variant lookup`

- Business goal: Returns Unleash variant objects for requested feature names.
- Starting point: Existing service state
- Expected business result: Returns Unleash variant objects for requested feature names.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | get feature variants | `GET /feature/variant` with query list `feature` to return the requested view without changing persisted state | Yes | Yes | Yes | `test_27_getOnFeatureVariantWithQueryParamsReturnsObject` asserts HTTP 200 and documented response content; no state mutation is required. | FeatureToggleController.variant, line 31: 5/5 instructions and 1/1 line; FeatureToggleService.hentVarianter 9/9 instructions. |

- Happy-path item: `Covered` — `test_27_getOnFeatureVariantWithQueryParamsReturnsObject` completes the single required stateless operation and asserts its documented response.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`, counting one happy path plus all documented failures
- Status and confidence: `Covered / High`
- Exact gap: none for the authoritative happy path; no failures or optional steps are documented.
- Recommended test IDs that close the gap: none.

### `B60`: `Internal health probe`

- Business goal: Returns `ok` if the database query succeeds.
- Starting point: Existing service state
- Expected business result: Returns `ok` if the database query succeeds.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | run health check | `GET /internal/healthcheck` with authenticated caller and endpoint-specific required request values to return the requested view without changing persisted state | Yes | Yes | Yes | `test_24_getOnHealthcheckReturnsContent` asserts HTTP 200 and documented response content; no state mutation is required. | HealthCheckController.healthcheck, line 20: 7/7 instructions and 1/1 line. |

- Happy-path item: `Covered` — `test_24_getOnHealthcheckReturnsContent` completes the single required stateless operation and asserts its documented response.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`, counting one happy path plus all documented failures
- Status and confidence: `Covered / High`
- Exact gap: none for the authoritative happy path; no failures or optional steps are documented.
- Recommended test IDs that close the gap: none.

### `B61`: `Overview notification listing`

- Business goal: Returns unread bell notifications for the logged-in party’s identifiers.
- Starting point: Existing service state
- Expected business result: Returns unread bell notifications for the logged-in party’s identifiers.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | list overview notifications | `GET /varsler/oversikt` with cookie `innlogget-part` to obtain notification ids visible to the caller | Yes | No | No | `test_40_getOnOversiktReturns400`, `test_66_getOnOversiktReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof.
- Recommended test IDs that close the gap: `B61-HP`.

### `B62`: `Agreement modal notification listing`

- Business goal: Returns unread bell notifications for a specific agreement and logged-in party.
- Starting point: Existing service state
- Expected business result: Returns unread bell notifications for a specific agreement and logged-in party.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | list agreement modal notifications | `GET /varsler/avtale-modal` with query `avtaleId`; cookie `innlogget-part` to return the requested view without changing persisted state | Yes | No | No | `test_41_getOnAvtale_modalReturns400`, `test_67_getOnAvtale_modalReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof.
- Recommended test IDs that close the gap: `B62-HP`.

### `B63`: `Agreement notification log listing`

- Business goal: Returns all notifications for a specific agreement and receiver role.
- Starting point: Existing service state
- Expected business result: Returns all notifications for a specific agreement and receiver role.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | list agreement notification log | `GET /varsler/avtale-logg` with query `avtaleId`; cookie `innlogget-part` to return the requested view without changing persisted state | Yes | No | No | `test_42_getOnAvtale_loggReturns400`, `test_68_getOnAvtale_loggReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| list agreement notification log | `AvtaleRepository.findById empty outcome (NoSuchElementException)` | the requested agreement does not exist<br>**Why:** The notification-log function cannot establish the aggregate whose log was requested.<br>**Violated prerequisite:** An existing agreement identified by `avtaleId` is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/varsel/VarselController.java — VarselController.hentAlleVarslerForAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| list agreement notification log | `TilgangskontrollException` | the authenticated party does not own or otherwise have domain access to the concrete agreement<br>**Why:** The object-scoped access check evaluates the persisted agreement's participant, employer, mentor, advisor, or decision-maker relationship and rejects the party.<br>**Violated prerequisite:** The caller must have the concrete relationship required for this agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 2 concrete failure branches.
- Recommended test IDs that close the gap: `B63-HP`, `B63-F01`, `B63-F02`.

### `B64`: `Single notification read marking`

- Business goal: Mark one notification as read for the logged-in party.
- Starting point: Existing service state
- Expected business result: Marks one notification as read for the logged-in party.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | list overview notifications | `GET /varsler/oversikt` with cookie `innlogget-part` to obtain notification ids visible to the caller | Yes | No | No | `test_40_getOnOversiktReturns400`, `test_66_getOnOversiktReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |
| 2 | mark notification as read | `POST /varsler/{varselId}/sett-til-lest` with path `varselId` returned from `list overview notifications`; same cookie `innlogget-part` to set that notification to read | Yes | No | No | `test_119_postOnSett_til_lestReturns400`, `test_145_postOnSett_til_lestReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | list overview notifications | `GET /varsler/oversikt` with cookie `innlogget-part` to verify the submitted ids are absent from unread notifications | No | Similar route probes occur only in separately reset tests and therefore do not verify B64. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| mark notification as read | `VarselRepository.findByIdAndIdentifikatorIn returns null` | the notification is unknown or is not owned by any identifier of the logged-in party<br>**Why:** The object-scoped lookup withholds the notification, so read state cannot be mutated.<br>**Violated prerequisite:** The notification must exist and belong to the logged-in party. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/varsel/VarselController.java — VarselController.settTilLest`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `2/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 1 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B64-HP`, `B64-F01`, `B64-V01`.

### `B65`: `Bulk notification read marking`

- Business goal: Mark several caller-owned notifications as read in one request.
- Starting point: Existing service state
- Expected business result: Marks each notification id in the request body as read.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | list overview notifications | `GET /varsler/oversikt` with cookie `innlogget-part` to obtain notification ids visible to the caller | Yes | No | No | `test_40_getOnOversiktReturns400`, `test_66_getOnOversiktReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |
| 2 | mark multiple notifications as read | `POST /varsler/sett-alle-til-lest` with body array of `varselId` values returned from `list overview notifications`; same cookie `innlogget-part` to set all submitted notifications to read | Yes | No | No | `test_52_postOnSett_alle_til_lestReturns400`, `test_73_postOnSett_alle_til_lestReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | list overview notifications | `GET /varsler/oversikt` with cookie `innlogget-part` to verify the submitted ids are absent from unread notifications | No | Similar route probes occur only in separately reset tests and therefore do not verify B65. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| mark multiple notifications as read | `VarselRepository.findByIdAndIdentifikatorIn returns null` | at least one selected notification is unknown or not owned by the logged-in party<br>**Why:** Bulk processing delegates each ID to the same object-scoped read mutation and stops on the invalid selection.<br>**Violated prerequisite:** Every selected notification must exist and belong to the logged-in party. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/varsel/VarselController.java — VarselController.settFlereVarslerTilLest/settTilLest`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `2/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 1 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B65-HP`, `B65-F01`, `B65-V01`.

### `B66`: `Journal export and completion marking`

- Business goal: Export unjournaled agreement versions and mark the consumed versions as journaled.
- Starting point: Existing service state
- Expected business result: Stores journal post ids on agreement content versions.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | list unjournaled agreements | `GET /internal/avtaler` with system issuer caller accepted by `validerSystembruker` to obtain agreement version ids requiring journalføring | Yes | No | No | `test_64_getOnInternalAvtalerWithQueryParamReturns401`, `test_72_getOnInternalAvtalerReturns401`, `test_83_getOnAvtalerReturns403` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |
| 2 | mark agreement versions as journaled | `PUT /internal/avtaler` with body map from `avtaleVersjonId` UUIDs returned by `list unjournaled agreements` to upstream `journalpostId` strings; same system caller to persist journal post ids on those versions | Yes | No | No | `test_62_putOnAvtalerReturns400`, `test_80_putOnAvtalerReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | list unjournaled agreements | `GET /internal/avtaler` with system issuer caller accepted by `validerSystembruker` to verify processed versions are no longer returned | No | Similar route probes occur only in separately reset tests and therefore do not verify B66. |

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `2/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B66-HP`, `B66-V01`.

### `B67`: `Selected agreement wage-subsidy recalculation`

- Business goal: Recalculates missing wage subsidy totals for each selected agreement.
- Starting point: Existing service state
- Expected business result: Recalculates missing wage subsidy totals for each selected agreement.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | recalculate wage subsidy | `POST /utvikler-admin/reberegn` with body array of `avtaleId` UUIDs; developer-admin caller to complete the domain transition | Yes | No | No | `test_20_postOnReberegnCauses500_internalServerError`, `test_53_postOnReberegnReturns400`, `test_74_postOnReberegnReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| recalculate wage subsidy | `AvtaleRepository.findById empty outcome` | a selected agreement ID does not exist<br>**Why:** The selected recalculation loop requires every selected aggregate.<br>**Violated prerequisite:** Every selected ID must identify an agreement. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.reberegnLønnstilskudd`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| recalculate wage subsidy | `KAN_IKKE_ENDRE_ANNULLERT_AVTALE` | the agreement is annulled or interrupted<br>**Why:** The aggregate blocks the requested lifecycle mutation after either terminal marker is present.<br>**Violated prerequisite:** The agreement must be active and not annulled or interrupted. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| recalculate wage subsidy | `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE` | a selected agreement is not a supported wage-subsidy or summer-job measure<br>**Why:** Recalculation is defined only for the three subsidy-backed measures.<br>**Violated prerequisite:** Every selected agreement must use a supported measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.reberegnLønnstilskudd/krevEnAvTiltakstyper`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| recalculate wage subsidy | `KAN_IKKE_REBEREGNE` | a selected agreement already has total subsidy, or lacks one or more required calculation inputs<br>**Why:** The repair function only fills a missing total when percentage, tax, holiday pay, monthly salary, and pension rate are all present.<br>**Violated prerequisite:** The aggregate must match the narrow repair-state predicate. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.reberegnLønnstilskudd`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/4`
- Behavior outcome checklist summary: `0/5`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 4 concrete failure branches.
- Recommended test IDs that close the gap: `B67-HP`, `B67-F01`, `B67-F02`, `B67-F03`, `B67-F04`.

### `B68`: `Missing reduced-percent date repair`

- Business goal: Finds entered permanent wage-subsidy agreements missing reduced-percent data and recalculates reduced values and migrated periods.
- Starting point: Existing service state
- Expected business result: Finds entered permanent wage-subsidy agreements missing reduced-percent data and recalculates reduced values and migrated periods.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | fix missing reduced-percent date | `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}` with path `migreringsDato` in `yyyy-MM-dd`; developer-admin caller to complete the domain transition | Yes | No | No | `test_85_postOnReberegn_mangler_dato_for_redusert_prosCauses500_internalServerError`, `test_147_postOnReberegn_mangler_dato_for_redusert_prosReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| fix missing reduced-percent date | `KAN_IKKE_REBEREGNE` | a selected repair candidate lacks one or more required dates or calculation inputs<br>**Why:** The reduced-value repair validates its complete calculation state before recalculating.<br>**Violated prerequisite:** Start, end, total, percentage, tax, holiday pay, monthly salary, and pension rate must all exist. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.reUtregnRedusert`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 1 concrete failure branches.
- Recommended test IDs that close the gap: `B68-HP`, `B68-F01`.

### `B69`: `Dry-run missing reduced-percent date fix`

- Business goal: Counts/logs permanent wage-subsidy agreements that would be repaired, without saving changes.
- Starting point: Existing service state
- Expected business result: Counts/logs permanent wage-subsidy agreements that would be repaired, without saving changes.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | dry-run missing reduced-percent date fix | `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}` with path `migreringsDato` in `yyyy-MM-dd`; developer-admin caller to validate and preview the transition without saving it | Yes | No | No | `test_86_postOnReberegn_mangler_dato_for_redusert_prosent_dry_runCauses500_internalServerError`, `test_148_postOnReberegn_mangler_dato_for_redusert_prosent_dry_runReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof.
- Recommended test IDs that close the gap: `B69-HP`.

### `B70`: `Admin subsidy-period generation for one agreement`

- Business goal: Generates subsidy periods for one agreement after an Arena migration date.
- Starting point: Existing service state
- Expected business result: Generates subsidy periods for one agreement after an Arena migration date.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | generate subsidy periods for agreement | `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}` with path `avtaleId`; developer-admin caller to complete the domain transition | Yes | No | No | `test_188_postOnLag_tilskuddsperioder_for_en_avtalReturns400`, `test_191_postOnLag_tilskuddsperioder_for_en_avtalReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B70. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| generate subsidy periods for agreement | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.lagTilskuddsperioderPåEnAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| generate subsidy periods for agreement | `FORLENG_MIDLERTIDIG_IKKE_TILGJENGELIG` | the persisted reduced-percentage date lies after the entire agreement period being generated<br>**Why:** The subsidy-period calculator cannot reconcile that reduction date with the generation range.<br>**Violated prerequisite:** The stored reduction date must be consistent with the agreement period. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/RegnUtTilskuddsperioderForAvtale.java — RegnUtTilskuddsperioderForAvtale.beregnTilskuddsperioderForAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 2 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B70-HP`, `B70-F01`, `B70-F02`, `B70-V01`.

### `B71`: `Unhandled subsidy-period recalculation`

- Business goal: Removes unhandled periods and recreates them from the first unhandled point through agreement end.
- Starting point: Existing service state
- Expected business result: Removes unhandled periods and recreates them from the first unhandled point through agreement end.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | recalculate unhandled subsidy periods | `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}` with path `avtaleId`; developer-admin caller to complete the domain transition | Yes | No | No | `test_120_postOnReberegn_ubehandlede_tilskuddsperiodReturns400`, `test_146_postOnReberegn_ubehandlede_tilskuddsperiodReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B71. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| recalculate unhandled subsidy periods | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.reberegnUbehandledeTilskuddsperioder`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| recalculate unhandled subsidy periods | `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE` | the agreement is not temporary wage subsidy, permanent wage subsidy, or summer job<br>**Why:** Unhandled-period recalculation supports only subsidy-backed measures.<br>**Violated prerequisite:** The agreement must use a supported subsidy measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.reberegnUbehandledeTilskuddsperioder`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| recalculate unhandled subsidy periods | `TreeSet.first empty outcome (NoSuchElementException)` | removing untreated periods leaves no approved or other period from which to derive the new start date<br>**Why:** The recalculation assumes a retained period when no approved periods exist and cannot establish its regeneration boundary.<br>**Violated prerequisite:** The period collection must retain a non-untreated anchor period, or contain an approved period. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.reberegnUbehandledeTilskuddsperioder`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| recalculate unhandled subsidy periods | `FORLENG_MIDLERTIDIG_IKKE_TILGJENGELIG` | the regenerated range lies wholly before the persisted reduced-percentage date<br>**Why:** The subsidy-period calculator cannot reconcile the reduction date with the derived range.<br>**Violated prerequisite:** The stored reduction date must be consistent with the periods being regenerated. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/RegnUtTilskuddsperioderForAvtale.java — RegnUtTilskuddsperioderForAvtale.beregnTilskuddsperioderForAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/4`
- Behavior outcome checklist summary: `0/5`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 4 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B71-HP`, `B71-F01`, `B71-F02`, `B71-F03`, `B71-F04`, `B71-V01`.

### `B72`: `Subsidy-period date-order diagnostic`

- Business goal: Scans temporary wage-subsidy agreements and logs periods whose start date is earlier than the previous sequence number’s start date.
- Starting point: Existing service state
- Expected business result: Scans temporary wage-subsidy agreements and logs periods whose start date is earlier than the previous sequence number’s start date.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | find subsidy period date-order problems | `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer` with authenticated caller and endpoint-specific required request values to complete the domain transition | Yes | No | No | `test_21_postOnFinn_avtaler_med_tilskuddsperioder_feil_datoerCauses500_internalServerError`, `test_75_postOnFinn_avtaler_med_tilskuddsperioder_feil_datoerReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| find subsidy period date-order problems | `predecessor lookup empty outcome (NoSuchElementException)` | a subsidy period has sequence number greater than one but no period with the immediately preceding sequence number<br>**Why:** The diagnostic assumes contiguous sequence numbers before comparing adjacent start dates.<br>**Violated prerequisite:** Every period after sequence one must have its immediate predecessor in the agreement's period collection. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.finnTilskuddsperioderMedFeilDatoer`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 1 concrete failure branches.
- Recommended test IDs that close the gap: `B72-HP`, `B72-F01`.

### `B73`: `Subsidy-period annulment`

- Business goal: Marks a subsidy period as annulled and emits an annulment event unless its refund status is expired.
- Starting point: Existing service state
- Expected business result: Marks a subsidy period as annulled and emits an annulment event unless its refund status is expired.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | annul subsidy period | `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}` with path `tilskuddsperiodeId` from agreement detail or operational record; developer-admin caller to complete the domain transition | Yes | No | No | `test_121_postOnAnnuller_tilskuddsperiodReturns400`, `test_153_postOnAnnuller_tilskuddsperiodReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| annul subsidy period | `RessursFinnesIkkeException` | the requested subsidy period does not exist<br>**Why:** The owned function cannot enter the subsidy period's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing subsidy period identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.annullerTilskuddsperiode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 1 concrete failure branches.
- Recommended test IDs that close the gap: `B73-HP`, `B73-F01`.

### `B74`: `Annul and resend approved subsidy period`

- Business goal: Annuls an existing subsidy period and creates a replacement period with approved status, reusing approval metadata.
- Starting point: Existing service state
- Expected business result: Annuls an existing subsidy period and creates a replacement period with approved status, reusing approval metadata.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | annul and resend approved subsidy period | `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}` with path `tilskuddsperiodeId` from agreement detail or operational record; developer-admin caller to complete the domain transition | Yes | No | No | `test_97_postOnAnnuller_og_resend_tilskuddsperiodReturns400`, `test_154_postOnAnnuller_og_resend_tilskuddsperiodReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| annul and resend approved subsidy period | `RessursFinnesIkkeException` | the requested subsidy period does not exist<br>**Why:** The owned function cannot enter the subsidy period's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing subsidy period identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.lagNyGodkjentTilskuddsperiodeFraAnnullertPeriode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| annul and resend approved subsidy period | `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE` | the period belongs to an agreement outside the three subsidy-backed measures<br>**Why:** Replacement generation is restricted to subsidy-backed agreements.<br>**Violated prerequisite:** The parent agreement must use a supported subsidy measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.lagNyGodkjentTilskuddsperiodeFraAnnullertPeriode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| annul and resend approved subsidy period | `TILSKUDDSPERIODE_ER_ALLEREDE_BEHANDLET` | the period has expired refund status, so annulment intentionally leaves its status unchanged<br>**Why:** Replacement requires `ANNULLERT`, but the preceding annul operation preserves an expired-refund period's original status.<br>**Violated prerequisite:** A replacement can be generated only from a period that became annulled. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.annullerTilskuddsperiode/lagNyGodkjentTilskuddsperiodeFraAnnullertPeriode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 3 concrete failure branches.
- Recommended test IDs that close the gap: `B74-HP`, `B74-F01`, `B74-F02`, `B74-F03`.

### `B75`: `Annul and generate unhandled subsidy period`

- Business goal: Annuls an existing subsidy period and creates a replacement with unhandled status.
- Starting point: Existing service state
- Expected business result: Annuls an existing subsidy period and creates a replacement with unhandled status.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | annul and generate unhandled subsidy period | `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}` with path `tilskuddsperiodeId` from agreement detail or operational record; developer-admin caller to complete the domain transition | Yes | No | No | `test_122_postOnAnnuller_og_generer_tilskuddsperiodReturns400`, `test_155_postOnAnnuller_og_generer_tilskuddsperiodReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| annul and generate unhandled subsidy period | `RessursFinnesIkkeException` | the requested subsidy period does not exist<br>**Why:** The owned function cannot enter the subsidy period's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing subsidy period identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.lagNyTilskuddsperiodeFraAnnullertPeriode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| annul and generate unhandled subsidy period | `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE` | the period belongs to an agreement outside the three subsidy-backed measures<br>**Why:** Replacement generation is restricted to subsidy-backed agreements.<br>**Violated prerequisite:** The parent agreement must use a supported subsidy measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.lagNyTilskuddsperiodeFraAnnullertPeriode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| annul and generate unhandled subsidy period | `TILSKUDDSPERIODE_ER_ALLEREDE_BEHANDLET` | the period has expired refund status, so annulment intentionally leaves its status unchanged<br>**Why:** Replacement requires `ANNULLERT`, but the preceding annul operation preserves an expired-refund period's original status.<br>**Violated prerequisite:** A replacement can be generated only from a period that became annulled. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.annullerTilskuddsperiode/lagNyTilskuddsperiodeFraAnnullertPeriode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 3 concrete failure branches.
- Recommended test IDs that close the gap: `B75-HP`, `B75-F01`, `B75-F02`, `B75-F03`.

### `B76`: `Annul and generate Arena-treated periods`

- Business goal: Annuls all subsidy periods on an agreement ending before `{dato}` and creates replacements with `BEHANDLET_I_ARENA` status.
- Starting point: Existing service state
- Expected business result: Annuls all subsidy periods on an agreement ending before `{dato}` and creates replacements with `BEHANDLET_I_ARENA` status.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | annul and generate Arena-treated periods | `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}` with path `avtaleId`; path `dato` in `yyyy-MM-dd`; developer-admin caller to complete the domain transition | Yes | No | No | `test_190_postOnAnnuller_og_generer_behandlet_i_arena_periodReturns400`, `test_193_postOnAnnuller_og_generer_behandlet_i_arena_periodReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B76. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| annul and generate Arena-treated periods | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.annullerOgGenererBehandletIArenaPerioder`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| annul and generate Arena-treated periods | `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE` | a selected period belongs to an agreement outside the three subsidy-backed measures<br>**Why:** Arena-treated replacement generation is restricted to subsidy-backed agreements.<br>**Violated prerequisite:** The parent agreement must use a supported subsidy measure. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.lagNyBehandletIArenaTilskuddsperiodeFraAnnullertPeriode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |
| annul and generate Arena-treated periods | `TILSKUDDSPERIODE_ER_ALLEREDE_BEHANDLET` | a selected period has expired refund status, so it is not changed to `ANNULLERT`<br>**Why:** The replacement guard rejects the still-non-annulled period after the intentional no-op annulment branch.<br>**Violated prerequisite:** Every replacement source period must become annulled. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.annullerTilskuddsperiode/lagNyBehandletIArenaTilskuddsperiodeFraAnnullertPeriode`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 3 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B76-HP`, `B76-F01`, `B76-F02`, `B76-F03`, `B76-V01`.

### `B77`: `Selected data warehouse patching`

- Business goal: Creates DVH patch message entities for selected agreement ids found in the repository.
- Starting point: Existing service state
- Expected business result: Creates DVH patch message entities for selected agreement ids found in the repository.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | patch selected data warehouse messages | `POST /utvikler-admin/dvh-melding/patch` with body object with `avtaleIder` array; DVH patch group caller to complete the domain transition | Yes | No | No | `test_88_postOnPatchCauses500_internalServerError`, `test_132_postOnPatchReturns400`, `test_150_postOnPatchReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof.
- Recommended test IDs that close the gap: `B77-HP`.

### `B78`: `All-agreement data warehouse patching`

- Business goal: Creates DVH patch messages for all agreements.
- Starting point: Existing service state
- Expected business result: Creates DVH patch messages for all agreements.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | patch all data warehouse messages | `POST /utvikler-admin/dvh-melding/patchalleavtaler` with authenticated caller and endpoint-specific required request values to complete the domain transition | Yes | No | No | `test_87_postOnPatchalleavtalerCauses500_internalServerError`, `test_149_postOnPatchalleavtalerReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof.
- Recommended test IDs that close the gap: `B78-HP`.

### `B79`: `Single agreement event publication`

- Business goal: Sends an agreement event message for one existing agreement.
- Starting point: Existing service state
- Expected business result: Sends an agreement event message for one existing agreement.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | send event message for one agreement | `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}` with path `avtaleId`; developer-admin caller to complete the domain transition | Yes | No | No | `test_189_postOnSend_melding_en_avtalReturns400`, `test_192_postOnSend_melding_en_avtalReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | retrieve agreement by id | `GET /avtaler/{avtaleId}` with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state | No | Similar route probes occur only in separately reset tests and therefore do not verify B79. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| send event message for one agreement | `RessursFinnesIkkeException` | the requested agreement does not exist<br>**Why:** The owned function cannot enter the agreement's domain operation because its repository lookup is empty.<br>**Violated prerequisite:** An existing agreement identified by the request is required. | No | High | No generated test intentionally establishes this exact post-binding business condition with all unrelated prerequisites valid; status-only probes do not qualify. | No generated test verifies rollback, persistence, or side effects. Documented persisted outcome: Not documented; no mutation may be inferred. | Documented implementation: `src/main/java/no/nav/tag/tiltaksgjennomforing/datadeling/AvtaleHendelseController.java — AvtaleHendelseController.sendMeldingForEnAvtale`. No test-specific method/line/branch evidence plus deterministic setup uniquely reaches this discriminator. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof; all 1 concrete failure branches; 1 optional verification step(s).
- Recommended test IDs that close the gap: `B79-HP`, `B79-F01`, `B79-V01`.

### `B80`: `All-agreement event publication`

- Business goal: Sends agreement event messages for all agreements.
- Starting point: Existing service state
- Expected business result: Sends agreement event messages for all agreements.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | send event messages for all agreements | `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler` with authenticated caller and endpoint-specific required request values to complete the domain transition | Yes | No | No | `test_89_postOnSend_melding_alle_avtalerCauses500_internalServerError`, `test_151_postOnSend_melding_alle_avtalerReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof.
- Recommended test IDs that close the gap: `B80-HP`.

### `B81`: `All-agreement event publication dry-run`

- Business goal: Performs the all-agreement event-message operation in dry-run mode.
- Starting point: Existing service state
- Expected business result: Performs the all-agreement event-message operation in dry-run mode.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | dry-run event messages for all agreements | `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler` with authenticated caller and endpoint-specific required request values to validate and preview the transition without saving it | Yes | No | No | `test_90_postOnDry_send_melding_alle_avtalerCauses500_internalServerError`, `test_152_postOnDry_send_melding_alle_avtalerReturns401` is a route probe only; observed results are binding/auth/admission/5xx and no documented terminal state is asserted. | No precise JaCoCo evidence proves successful admission and the documented operation. Covered framework/DTO lines are non-dispositive. |

- Happy-path item: `Not Covered` — no single isolated test executes every required step with valid actor/state/bindings and proves the terminal business result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure items are documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered / High`
- Exact gap: context-valid required workflow and terminal-state proof.
- Recommended test IDs that close the gap: `B81-HP`.

## Cross-Behavior Gaps

- Generic-gate attrition dominates: 100 calls return 400, 75 return 401, five return 403, and ten return 500; only 13 calls return 200. The 400s use malformed UUIDs/dates/enums/FNRs, omit required cookies/headers/bodies, or otherwise fail before a countable business condition.
- The generated class contains no `.cookie(...)` call, while many endpoints require `innlogget-part`. This prevents role resolution and makes the shared approval route non-distinguishable.
- Database and SUT reset occurs before every test with no generated business fixtures. No agreement, agreement version, notification, subsidy period, or saved search produced by one test survives into another.
- There are no response-to-request business bindings: no `Location -> avtaleId`, `sokId`, `sistEndret -> If-Unmodified-Since`, notification id, version id, or subsidy-period id is captured and reused.
- Assertions are overwhelmingly status/body-shape checks. There are no direct persistence checks, aggregate invariant checks, event/message checks, notification checks, rollback checks, or forbidden-side-effect assertions.
- JaCoCo reports partial entry signal for `AvtaleController.opprettMentorAvtale` (2/66 instructions, 1/13 lines, 0/8 branches), `InternalDvhMeldingProdusentController.patcheAvtale/patchAlleAvtaler`, `AvtaleHendelseController` all-agreement methods, and `InternalAvtaleController.hentIkkeJournalfoerteAvtaler`. The requests still do not pass all relevant binding/admission gates or prove a business outcome, so this is execution evidence only.
- No concrete documented `Feilkode`, exception discriminator, state-after-failure, or source branch is uniquely asserted. Consequently all 461 documented failure occurrences and all 461 normalized unique source branches remain uncovered.
- No optional verification call executes in the same continuous scenario as its target mutation. Separately reset route probes do not count as verification.
- Async/event/publication endpoints assert neither completion nor resulting records/messages; 5xx responses originate from unexpected/infrastructure paths and receive no business credit.

## Suggested Additional Tests

### Test `T1`: `advisor creates an ordinary work-training agreement`

- Priority: `P0`
- Target behavior ID and name: B2, Advisor-created agreement
- Target checklist item: happy path and required step; exact function `create advisor agreement`
- Test category: success
- Why needed: creation is the root prerequisite for most agreement lifecycle behaviors, yet current tests never create an aggregate or bind `Location -> avtaleId`.
- Coverage delta if passing: function invocation remains covered; required-step application reach +1; required-step context-valid success +1; happy-path behavior +1; behavior outcome +1; B2 changes from Not Covered to Partially Covered because its 13 failures remain.

#### Initial state and fixture plan

- Reset database and SUT once at test start; start with no `Avtale`, `AvtaleInnhold`, subsidy-period, or notification rows.
- Fix the clock at `2026-07-01T10:00:00Z` and deterministic UUID generation at `11111111-1111-4111-8111-111111111111` if the harness supports it.
- Authenticate AAD advisor `Q123456` with issuer `aad`, OID `aaaaaaaa-aaaa-4aaa-8aaa-aaaaaaaaaaaa`; ABAC write access for participant `31129118213` returns true.
- PDL returns participant name `Ada Nordmann`, address protection `UGRADERT`, and geographic municipality `0906`. Arena returns `formidlingsgruppe=ARBS`, `kvalifiseringsgruppe=BFORM`, `oppfolgingsenhet=0906`. Norg2 returns geographic/follow-up unit name `NAV Arendal`. Ereg returns business unit `999999999`, type `Virksomhet`, name `Saltrød og Høneby`.
- Use the transaction committed by the HTTP request; wait synchronously for the response. Drain or synchronously inspect emitted creation events/notifications before final assertions; no unbounded async wait.
- No direct database setup represents creation; database reads are verification only.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B2.1 / `create advisor agreement` | AAD advisor `Q123456`; issuer `aad`; OID `aaaaaaaa-aaaa-4aaa-8aaa-aaaaaaaaaaaa`; ABAC write access to `31129118213` | `POST /tiltaksgjennomforing-api/avtaler?ryddeavtale=false` | `Authorization: Bearer <AAD token>`; `Content-Type: application/json`; `Accept: application/json`; no role cookie required by this controller method | Query `ryddeavtale=false`; no path/form values | `{"deltakerFnr":"31129118213","bedriftNr":"999999999","tiltakstype":"ARBEIDSTRENING"}` | Literal fixture values; capture `Location: /avtaler/11111111-1111-4111-8111-111111111111` as `avtalePath`, and its final segment as `avtaleId` | `201 Created`; exact `Location` header; empty response body | One `Avtale` with captured id, participant/company/type/advisor, participant/company names and NAV units; one current empty `AvtaleInnhold`; ordinary agreement has no `ArenaRyddeAvtale`; creation event/expected notifications are emitted exactly once |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `deltakerFnr` | JSON body | `31129118213` | 11 ASCII digits / `Fnr` | Yes | Exactly 11 digits; participant must be at least 16 | ABAC writable; PDL not code 6; Arena status complete and eligible | Existing deterministic fixture is adult and has eligible `BFORM` status |
| 1 | `bedriftNr` | JSON body | `999999999` | organization number string | Yes | Non-null identifier accepted by `BedriftNr` | Ereg must classify it as `Virksomhet`, not legal entity or organizational link | Existing Ereg business-unit fixture |
| 1 | `tiltakstype` | JSON body | `ARBEIDSTRENING` | enum | Yes | One of six `Tiltakstype` names | Avoids summer-job age ceiling and wage-subsidy-specific eligibility/calculation paths | Minimal valid ordinary creation case |
| 1 | `ryddeavtale` | query | `false` | boolean | No, but explicit here | `true` or `false` | Must be false to distinguish B2 from B3 | Proves the ordinary advisor-created variant |
| 1 | advisor context | token/upstream | `Q123456`, issuer `aad`, write access true | NAV ident + JWT claims | Yes | Supported issuer/role; valid Azure OID | Access is scoped to the concrete participant | Passes generic and business-scoped access gates |

#### Assertions

- Assert HTTP `201`, exact `Location`, and empty body; parse and retain `avtaleId`.
- Query the database by `avtaleId`; assert exactly one aggregate and one current content row, `tiltakstype=ARBEIDSTRENING`, `deltakerFnr=31129118213`, `bedriftNr=999999999`, `veilederNavIdent=Q123456`, names/units from stubs, non-null `sistEndret`, and no approvals/deletion/annulment.
- Assert no `ArenaRyddeAvtale` row and no subsidy periods.
- Assert exactly one advisor-creation domain event and the implementation-defined creation side effects; assert no duplicate messages/notifications.
- Corroborate `AvtaleController.opprettAvtaleSomVeileder`, `Veileder.opprettAvtale`, `Veileder.sjekkTilgangskontroll`, `Avtale.veilederOppretterAvtale`, Ereg enrichment, repository save, and the `ryddeavtale=false` branch in JaCoCo.

#### Isolation and variants

- Roll back or reset all database rows, recorded messages, deterministic UUID state, clock, ABAC/PDL/Arena/Norg2/Ereg stubs, and token service after the test.
- Commit the request transaction before database/event assertions; poll an async outbox only if the implementation actually dispatches asynchronously, at 100 ms intervals for at most 5 seconds.
- Separate variants: B3 with `ryddeavtale=true`; each of B2-F01…B2-F13 as one-fault tests; employer and mentor creation use separate actors/routes and must not be folded into this test.

### Test `T2`: `save and replay an empty advisor agreement search`

- Priority: `P0`
- Target behavior ID and name: B8, Saved agreement search registration and replay
- Target checklist item: happy path; exact functions `search agreements and save search` and `replay saved agreement search`
- Test category: state transition
- Why needed: it is the smallest authoritative multi-step workflow and directly tests the currently absent `sokId` response-to-request binding.
- Coverage delta if passing: two required-step application reaches +2; two context-valid successes +2; happy-path behavior +1; behavior outcome +1; B8 changes from Not Covered to Covered.

#### Initial state and fixture plan

- Reset database/SUT once. Keep the agreement table empty; this is a valid collection-read state. No direct setup is needed.
- Fix clock at `2026-07-01T10:00:00Z`. Authenticate AAD advisor `Q123456` with issuer `aad`, valid OID, and an empty accessible agreement set.
- Use one HTTP session and one database state for both calls. Calls and persistence are synchronous; no external service is required after role resolution/Axsys setup.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B8.1 / `search agreements and save search` | AAD advisor `Q123456`; issuer `aad`; role `VEILEDER` | `POST /tiltaksgjennomforing-api/avtaler/sok?page=0&size=10&sorteringskolonne=sistEndret` | `Authorization: Bearer <AAD token>`; cookie `innlogget-part=VEILEDER`; `Content-Type: application/json`; `Accept: application/json` | Query `page=0`, `size=10`, `sorteringskolonne=sistEndret`; no path/form | `{}` | Empty `AvtalePredicate`; capture response body `sokId` as `savedSokId` | `200 OK`; body has `avtaler=[]`, `size=10`, `currentPage=0`, `totalItems=0`, `totalPages=0`, `sokeParametere={}`, `sorteringskolonne="sistEndret"`, and non-empty `sokId` | One `FilterSok` row for `{}` with `sokId=savedSokId`, initial search count/time set |
| 2 | B8.2 / `replay saved agreement search` | Same AAD advisor, token, role, session, and database | `GET /tiltaksgjennomforing-api/avtaler/sok?sokId=<savedSokId>&page=0&size=10&sorteringskolonne=sistEndret` | `Authorization: Bearer <same token>`; cookie `innlogget-part=VEILEDER`; `Accept: application/json` | Query `sokId=savedSokId`, `page=0`, `size=10`, `sorteringskolonne=sistEndret`; no body/path/form | No body | `savedSokId` from call 1 response, not a hard-coded value | `200 OK`; same empty result and exact `sokId=savedSokId` | Same `FilterSok` row; `antallGangerSokt` incremented once and `sistSoktTidspunkt` advanced to the fixed replay time |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | predicate | JSON body | `{}` | `AvtalePredicate` object | Yes | Nullable fields omitted; valid JSON object | Hash generated from this exact object must be persisted | Exercises valid empty-search semantics without unrelated filters |
| 1,2 | role | cookie | `VEILEDER` | `Avtalerolle` enum | Yes | Supported issuer/role pair | AAD issuer must pair with `VEILEDER` | Prevents missing-cookie and invalid-role gates |
| 1,2 | paging | query | `page=0`, `size=10` | integers | No | Controller applies `Math.abs`; size must allow returned page | Same paging used during replay | Deterministic response |
| 1,2 | sort | query | `sistEndret` | field name string | No | Repository-sortable field | Must be identical across both calls for exact replay assertion | Controller default made explicit |
| 2 | `sokId` | query | exact call-1 body value | non-empty string/hash | Yes | Must identify persisted `FilterSok` | Same database and actor scenario | Proves response-to-request binding |

#### Assertions

- Assert both HTTP statuses and every listed response field; explicitly assert call 2 `sokId` equals the captured call 1 value.
- Assert one, not two, `FilterSok` rows; exact stored predicate `{}`; replay increments `antallGangerSokt` and updates `sistSoktTidspunkt`.
- Assert no `Avtale`, `AvtaleInnhold`, notification, event, or subsidy-period rows are created.
- Corroborate `AvtaleController.hentAlleAvtalerInnloggetBrukerHarTilgangTilMedPost` and `...MedGet`, the existing-filter lookup/replay branches, repository save, and role resolution in JaCoCo.

#### Isolation and variants

- Reset `FilterSok`, clock, token/Axsys stubs, and HTTP session after the test; do not reset between calls.
- Keep both calls committed in one scenario. No async polling is required.
- Separate variants: replay of an unknown `sokId`, hash-collision handling, negative page/size normalization, and actor-specific result visibility.

### Test `T3`: `valid scoped request returns not found for a missing agreement`

- Priority: `P0`
- Target behavior ID and name: B9, Agreement detail retrieval by id
- Target checklist item: concrete failure; exact function `retrieve agreement by id`; source discriminator `RessursFinnesIkkeException`; condition `the requested agreement does not exist`
- Test category: business failure
- Why needed: current tests use malformed non-UUID paths and prove only conversion failure; a valid absent UUID is the canonical documented domain-not-found branch.
- Coverage delta if passing: documented business-failure +1; unique source business-branch +1; behavior outcome +1; B9 changes from Not Covered to Partially Covered.

#### Initial state and fixture plan

- Reset database/SUT and assert no `Avtale` row has id `22222222-2222-4222-8222-222222222222`. No aggregate or child rows are inserted.
- Authenticate AAD advisor `Q123456`, issuer `aad`, role `VEILEDER`, valid OID/Axsys setup. The missing lookup occurs before agreement-scoped ABAC evaluation; keep generic actor setup valid.
- Fix clock at `2026-07-01T10:00:00Z`. No external-domain failure is stubbed; all unrelated stubs return normal values if called.
- Use the request transaction synchronously; no async work should begin on a read failure.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B9 failure / `retrieve agreement by id` | AAD advisor `Q123456`; issuer `aad`; role `VEILEDER`; valid generic identity context | `GET /tiltaksgjennomforing-api/avtaler/22222222-2222-4222-8222-222222222222` | `Authorization: Bearer <AAD token>`; cookie `innlogget-part=VEILEDER`; `Accept: application/json` | Path template `{avtaleId}` = `22222222-2222-4222-8222-222222222222`; no query/form | No body | Deterministic valid UUID reserved as absent after reset | `404 Not Found`; no `feilkode` header; framework error body, if enabled, identifies this resolved path and status 404 | Database remains byte-for-byte unchanged; no events, notifications, journal/DVH records, or external mutation calls |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `avtaleId` | path | `22222222-2222-4222-8222-222222222222` | RFC 4122 UUID | Yes | Syntactically valid UUID | Must be absent after reset; no competing failure condition | Reaches repository lookup instead of UUID conversion failure |
| 1 | role | cookie | `VEILEDER` | `Avtalerolle` enum | Yes | Supported AAD pairing | Generic authentication and role admission must succeed | Isolates domain not-found |
| 1 | database state | repository | no row for target UUID | absence invariant | Yes for target branch | All unrelated tables may be empty | No deleted/annulled/inaccessible row with same id | Ensures exactly one documented failing condition |

#### Assertions

- Assert HTTP `404`; assert the absence of a `feilkode` header because `RessursFinnesIkkeException` is `@ResponseStatus(NOT_FOUND)`, not `FeilkodeException`. If the configured error serializer emits a body, assert exact status/path and do not invent a discriminator field.
- Assert repository lookup for the exact UUID occurred and `Avtalepart.hentAvtale` threw `RessursFinnesIkkeException`.
- Snapshot relevant table counts and message/event collectors before/after; assert no persisted mutation, event, notification, journal, DVH, recalculation, or external side effect.
- Corroborate `AvtaleController.hent`, `InnloggingService.hentAvtalepart(VEILEDER)`, `Avtalepart.hentAvtale`, the `Optional.orElseThrow(RessursFinnesIkkeException::new)` line, and the 404 exception mapping in JaCoCo.

#### Isolation and variants

- Reset database, token/Axsys stubs, clock, and collectors after the test. No direct setup beyond asserting absence is required.
- The read is synchronous; fail the test if any async side effect appears rather than polling for one.
- Separate variants: existing but inaccessible agreement (`TilgangskontrollException`) and mentor-owned unsigned agreement (`IKKE_TILGANG_TIL_AVTALE`). Do not combine either with this missing-resource test.

## Notes And Assumptions

- The API was not executed during this review. Results are derived from the generated test source, source code, and pre-existing JaCoCo XML/CSV artifacts.
- JaCoCo XML is treated as the primary report. XML and CSV counters agree at report level; no overlapping reports required union logic.
- Application-call counts use the generated `Calls:` records paired with each of 195 test methods. Token-service calls are authentication setup and are excluded from the application-call count.
- `Attempted` is deliberately broad for unique routes, even when requests fail at generic gates. Six exact functions on three shared-route families are withheld because the generated values do not identify the authoritative documented variant.
- Application reach is deliberately narrow. Partial method/line coverage that occurs before or inside an admission check is not credited as passed admission. Only the seven successful, precisely mapped B54–B60 methods have unambiguous application-reach evidence.
- All 400 status-only tests lack deterministic domain setup/discriminator/state evidence. They are classified as binding/generic failures, not documented business failures.
- Source/document discrepancy: B23 and B24 are separately authoritative mark/remove behaviors, while the source exposes one toggle method `setOmAvtalenKanEtterregistreres`; the generated request has no valid aggregate pre-state, so neither exact meaning is distinguishable. Scoring follows the two documented behaviors.
- Source/document discrepancy: `full-behavior.md` describes 79 OpenAPI operations but normalizes them into 83 exact functions because shared operations have distinct meanings. The report denominator is the authoritative 83 owned functions, not the raw OpenAPI operation count.
- No artifacts were missing or unreadable. No malformed inventory entry, denominator approximation, or branch-attribution substitution was required.
