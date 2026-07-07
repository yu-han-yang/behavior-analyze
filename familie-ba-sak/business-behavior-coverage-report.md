# Business Behavior Coverage Report

## Executive Summary

- Project under analysis: `familie-ba-sak` (`/Users/yangyuhan/behavior-analyze/familie-ba-sak`)
- Business specification: `business-behavior.md`
- Test suites analyzed: `EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java` (200 tests); `EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java` (200 tests); `EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java` (85 tests); total `485` tests
- Application calls analyzed: `492` calls and `184` distinct normalized method/routes
- Coverage reports analyzed: `reports/report.xml` (primary JaCoCo XML) and `reports/report.csv` (class-level cross-check)
- Source roots analyzed: `src/main/kotlin`; generated corpus under `tests`
- Total documented behaviors: `90`
- Total documented failure entries: `161`
- Covered / Partially Covered / Not Covered / Unclear: `7 / 7 / 72 / 4`
- Business behavior coverage: `10.5/90 (11.7%)`
- Function/API invocation coverage: `148/154 (96.1%)`, plus 3 ambiguous shared-route attempts spanning 6 exact functions
- Required-step attempt coverage: `153/153 (100.0%)`
- Required-step application-reach coverage: `88/153 (57.5%)`
- Required-step context-valid success coverage: `9/153 (5.9%)`
- Happy-path behavior coverage: `7/90 (7.8%)`
- Documented business-failure coverage: `7/161 (4.3%)`
- Unique source business-branch coverage: `7/161 (4.3%)`
- Behavior outcome checklist coverage: `14/251 (5.6%)`
- Optional verification execution coverage: `3/59 (5.1%)`
- Combined JaCoCo signal: lines `5234/29081 (18.0%)`; branches `178/11102 (1.6%)`; methods `1797/8965 (20.0%)`; classes `932/2242 (41.6%)`

The execution funnel is internally consistent: every required route was probed, 88 steps passed binding/admission and entered mapped application code, only 9 steps succeeded with the documented context, 7 complete single-step happy paths reached their business result, and 7 concrete documented failures were proved. The sharp collapse from route attempts to valid outcomes is caused by per-test database/SUT reset, no generated business fixtures, generic authorization failures, malformed payloads, unavailable external stubs, and missing database infrastructure. JaCoCo corroborates entry and selected branches but does not substitute for state and outcome proof.

## Inventory Validation

- Parsed behavior count: `90` (IDs `1`–`90`); expected baseline was `81`, a discrepancy of `+9`.
- Parsed failure-entry count: `161`; expected baseline was `461`, a discrepancy of `-300`.
- Behaviors with `Failure and exceptional cases: None.`: `25` — B6, B33, B37, B38, B44, B50, B51, B54, B55, B56, B57, B58, B59, B60, B64, B65, B66, B67, B70, B81, B82, B86, B88, B89, B90.
- Malformed or unparsed behavior/failure entries: `0`.
- Exact-function-name mapping failures: `0`.
- `full-behavior.md`: `164` distinct functions; `154` are owned by at least one authoritative behavior.
- Denominator reconciliation: `90 + 161 = 251` behavior-outcome items, not `542`; unique-source deduplication yields `161` because no normalized duplicate key was found.
- Inventory status: complete against the actual parsed document. The stated 81/461 baseline is stale or belongs to a different specification revision; no entry was silently omitted.

## Coverage Matrix

| ID | Business Behavior | Required Steps Attempted | Application Reached | Context-Valid Steps | Happy Path | Failure Coverage | Optional Verification | Status | Confidence |
|---|---|---:|---:|---:|---|---:|---:|---|---|
| B1 | Establish a case and reuse the case identity | 2/2 | 0/2 | 0/2 | Not Covered | 0/1 | 0/2 | Not Covered | High |
| B2 | Inspect case views and locate cases by person | 4/4 | 3/4 | 0/4 | Not Covered | 2/3 | 0/0 | Partially Covered | High |
| B3 | Resolve case participation and ongoing benefit relationships | 4/4 | 4/4 | 0/4 | Not Covered | 0/2 | 0/1 | Not Covered | High |
| B4 | Create and check manual repayment treatment on a case | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | 0/2 | Not Covered | High |
| B5 | Create a treatment and restart an early active treatment | 2/2 | 0/2 | 0/2 | Not Covered | 0/8 | 0/1 | Not Covered | High |
| B6 | Queue automated birth-event treatment processing | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | 0/0 | Not Covered | High |
| B7 | Change treatment theme | 1/1 | 0/1 | 0/1 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B8 | Execute the caseworker treatment step flow through decision | 6/6 | 0/6 | 0/6 | Not Covered | 0/8 | 0/3 | Not Covered | High |
| B9 | Dismiss an active treatment | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | 0/1 | Not Covered | High |
| B10 | Register institution and guardian information on treatment | 1/1 | 1/1 | 0/1 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B11 | Add a child to treatment basis and reset later treatment steps | 1/1 | 1/1 | 0/1 | Not Covered | 0/5 | 0/1 | Not Covered | High |
| B12 | Put treatment on wait, update wait, and resume | 3/3 | 0/3 | 0/3 | Not Covered | 0/7 | 0/1 | Not Covered | High |
| B13 | Read person information for case handling | 3/3 | 3/3 | 0/3 | Not Covered | 0/9 | 0/0 | Not Covered | High |
| B14 | Refresh treatment register basis and manually record death | 2/2 | 1/2 | 0/2 | Not Covered | 1/5 | 0/1 | Partially Covered | High |
| B15 | Maintain condition assessment records | 6/6 | 4/6 | 0/6 | Not Covered | 0/5 | 1/2 | Not Covered | High |
| B16 | Maintain EEA competence intervals | 2/2 | 1/2 | 0/2 | Not Covered | 0/3 | 0/2 | Not Covered | High |
| B17 | Maintain foreign period amounts | 2/2 | 1/2 | 0/2 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B18 | Update existing currency rate from ECB | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | 0/1 | Not Covered | High |
| B19 | Set historical ISK currency rate manually and delete currency rate | 2/2 | 1/2 | 0/2 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B20 | Maintain changed payment shares and reset treatment result | 4/4 | 2/4 | 0/4 | Not Covered | 0/3 | 0/2 | Not Covered | High |
| B21 | Inspect EEA timelines | 1/1 | 1/1 | 0/1 | Not Covered | 1/2 | 0/0 | Partially Covered | High |
| B22 | Maintain EEA refund periods | 4/4 | 1/4 | 0/4 | Not Covered | 0/2 | 0/1 | Not Covered | High |
| B23 | Maintain overpaid currency periods | 4/4 | 2/4 | 0/4 | Not Covered | 0/2 | 0/1 | Not Covered | High |
| B24 | Activate and deactivate corrected decision metadata | 2/2 | 2/2 | 0/2 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B25 | Activate, list, and deactivate corrected after-payment metadata | 3/3 | 2/3 | 0/3 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B26 | Add and remove small child supplement correction | 2/2 | 0/2 | 0/2 | Not Covered | 0/2 | 0/1 | Not Covered | High |
| B27 | Preview repayment warning letter | 1/1 | 1/1 | 0/1 | Not Covered | 1/2 | 0/0 | Partially Covered | High |
| B28 | Generate and retrieve decision letter | 2/2 | 1/2 | 0/2 | Not Covered | 0/3 | 0/1 | Not Covered | High |
| B29 | Preview and send manual treatment letter | 2/2 | 0/2 | 0/2 | Not Covered | 0/3 | 0/1 | Not Covered | High |
| B30 | Preview and send manual case letter | 2/2 | 0/2 | 0/2 | Not Covered | 0/2 | 0/1 | Not Covered | High |
| B31 | Maintain manual letter recipients | 3/3 | 2/3 | 0/3 | Not Covered | 0/3 | 0/1 | Not Covered | High |
| B32 | Edit decision periods and regenerate letter explanations | 6/6 | 3/6 | 0/6 | Not Covered | 0/4 | 0/2 | Not Covered | High |
| B33 | Retrieve treatment log | 1/1 | 1/1 | 0/1 | Not Covered | 0/0 | 0/0 | Not Covered | High |
| B34 | Retrieve external benefit data for BISYS | 1/1 | 0/1 | 0/1 | Not Covered | 0/2 | 0/0 | Not Covered | High |
| B35 | Retrieve pension child benefit | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | 0/0 | Not Covered | High |
| B36 | Order pension yearly export | 1/1 | 0/1 | 0/1 | Not Covered | 0/1 | 0/0 | Not Covered | High |
| B37 | Production tax data export | 2/2 | 2/2 | 0/2 | Not Covered | 0/0 | 0/0 | Not Covered | High |
| B38 | Tax test endpoint data retrieval | 2/2 | 2/2 | 0/2 | Not Covered | 0/0 | 0/0 | Not Covered | High |
| B39 | Retrieve Infotrygd case and benefit context | 3/3 | 3/3 | 0/3 | Not Covered | 0/2 | 0/0 | Not Covered | High |
| B40 | Discover collaborators by search and organization number | 2/2 | 2/2 | 0/2 | Not Covered | 1/2 | 0/0 | Partially Covered | High |
| B41 | Create and list complaint treatments for a case | 2/2 | 1/2 | 0/2 | Not Covered | 0/3 | 0/1 | Not Covered | High |
| B42 | Let complaint system create a revision after precheck | 2/2 | 0/2 | 0/2 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B43 | Retrieve decisions for complaint system | 1/1 | 0/1 | 0/1 | Not Covered | 0/1 | 0/0 | Not Covered | High |
| B44 | Search external tasks | 1/1 | 1/1 | 0/1 | Not Covered | 0/0 | 0/0 | Not Covered | High |
| B45 | Assign external task | 2/2 | 1/2 | 0/2 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B46 | Reset external task assignment | 2/2 | 1/2 | 0/2 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B47 | Retrieve journaling task data | 1/1 | 1/1 | 0/1 | Not Covered | 0/2 | 0/0 | Not Covered | High |
| B48 | Complete external task | 2/2 | 1/2 | 0/2 | Not Covered | 0/2 | 0/1 | Not Covered | High |
| B49 | Complete task while linking a journalpost | 1/1 | 0/1 | 0/1 | Not Covered | 0/2 | 0/2 | Not Covered | High |
| B50 | Retrieve open extended-benefit deadlines | 1/1 | 1/1 | 0/1 | Not Covered | 0/0 | 0/0 | Not Covered | High |
| B51 | Clear application task ownership | 1/1 | 1/1 | 0/1 | Not Covered | 0/0 | 0/1 | Not Covered | High |
| B52 | Inspect journalpost and retrieve documents | 3/3 | 3/3 | 0/3 | Not Covered | 0/4 | 0/1 | Not Covered | High |
| B53 | Journal an incoming journalpost | 1/1 | 0/1 | 0/1 | Not Covered | 0/3 | 0/2 | Not Covered | High |
| B54 | Retrieve feature toggles | 1/1 | 1/1 | 1/1 | Covered | 0/0 | 0/0 | Covered | High |
| B55 | Check person access | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | 0/0 | Not Covered | High |
| B56 | Queue identity event handling | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | 0/0 | Not Covered | High |
| B57 | Queue transitional-benefit event handling | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | 0/0 | Not Covered | High |
| B58 | Check rate-change eligibility for one case | 1/1 | 1/1 | 0/1 | Not Covered | 0/0 | 0/0 | Not Covered | High |
| B59 | Queue rate change for one case | 1/1 | 1/1 | 0/1 | Not Covered | 0/0 | 0/1 | Not Covered | High |
| B60 | Queue rate change for multiple cases | 1/1 | 1/1 | 0/1 | Not Covered | 0/0 | 0/0 | Not Covered | High |
| B61 | Run synchronous rate change for one case | 2/2 | 1/2 | 0/2 | Not Covered | 0/4 | 0/1 | Not Covered | High |
| B62 | Queue rate change from identities | 1/1 | 1/1 | 0/1 | Not Covered | 0/1 | 0/0 | Not Covered | High |
| B63 | Queue technical dismissal for long-deadline treatments | 1/1 | 1/1 | 0/1 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B64 | Identify ongoing cases without latest rate | 1/1 | 1/1 | 1/1 | Covered | 0/0 | 0/0 | Covered | Medium |
| B65 | Run consistency reconciliation dry run | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | 0/0 | Not Covered | High |
| B66 | Run real consistency reconciliation | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | 0/0 | Not Covered | High |
| B67 | Retrieve internal and application statistics | 2/2 | 2/2 | 2/2 | Not Covered | 0/0 | 0/0 | Partially Covered | High |
| B68 | Retrieve treatment statistics payload | 1/1 | 1/1 | 0/1 | Not Covered | 0/2 | 0/0 | Unclear | Low |
| B69 | Retrieve case statistics payload | 1/1 | 1/1 | 0/1 | Not Covered | 0/2 | 0/0 | Unclear | Low |
| B70 | Register statistics message as sent | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | 0/0 | Not Covered | High |
| B71 | Retrieve benefit statistics decisions | 1/1 | 1/1 | 0/1 | Not Covered | 0/2 | 0/0 | Unclear | Low |
| B72 | Queue unsent benefit statistics | 1/1 | 1/1 | 0/1 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B73 | Manually queue benefit statistics | 1/1 | 1/1 | 0/1 | Not Covered | 1/1 | 0/1 | Partially Covered | High |
| B74 | Resend manual migration statistics | 1/1 | 1/1 | 0/1 | Not Covered | 0/1 | 0/0 | Not Covered | High |
| B75 | Complete an administrative task list with partial success | 1/1 | 1/1 | 0/1 | Not Covered | 0/2 | 0/1 | Unclear | Low |
| B76 | Restart small child supplement job | 1/1 | 0/1 | 0/1 | Not Covered | 0/1 | 0/0 | Not Covered | High |
| B77 | Send payment orders administratively | 1/1 | 1/1 | 0/1 | Not Covered | 0/4 | 0/0 | Not Covered | High |
| B78 | Bulk corrected payment-order resend | 1/1 | 1/1 | 0/1 | Not Covered | 0/1 | 1/1 | Not Covered | High |
| B79 | Single-version corrected payment-order resend | 1/1 | 1/1 | 0/1 | Not Covered | 0/2 | 0/1 | Not Covered | High |
| B80 | Run unvalidated rate change administratively | 1/1 | 1/1 | 0/1 | Not Covered | 0/2 | 0/1 | Not Covered | High |
| B81 | Identify payments over 100 percent | 1/1 | 1/1 | 1/1 | Covered | 0/0 | 0/0 | Covered | Medium |
| B82 | Find payment-order issue candidates | 1/1 | 1/1 | 1/1 | Covered | 0/0 | 0/0 | Covered | High |
| B83 | Check incorrect cessation dates for selected treatments | 1/1 | 1/1 | 0/1 | Not Covered | 0/1 | 0/0 | Not Covered | High |
| B84 | Populate support dates for one treatment | 1/1 | 1/1 | 0/1 | Not Covered | 0/1 | 0/1 | Not Covered | High |
| B85 | Populate support dates in bulk | 1/1 | 1/1 | 0/1 | Not Covered | 0/2 | 0/0 | Not Covered | High |
| B86 | Find cases to close | 1/1 | 1/1 | 1/1 | Covered | 0/0 | 0/1 | Covered | High |
| B87 | Update case ongoing status | 1/1 | 1/1 | 0/1 | Not Covered | 0/1 | 1/1 | Not Covered | High |
| B88 | Find migration duplicates with ongoing Infotrygd case | 1/1 | 1/1 | 1/1 | Covered | 0/0 | 0/0 | Covered | High |
| B89 | Find migration duplicates | 1/1 | 1/1 | 1/1 | Covered | 0/0 | 0/0 | Covered | High |
| B90 | Fill empty condition start dates in preprod | 1/1 | 0/1 | 0/1 | Not Covered | 0/0 | 0/1 | Not Covered | High |

## Function/API Invocation Checklist

| Exact Function Name | Method/Route | Attempted? | Distinguishable? | Representative Tests | Result Classes |
|---|---|---|---|---|---|
| `add EØS refund period` | `POST /api/refusjon-eøs/behandlinger/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_126_postOnBehandlingReturns401 (401) | auth/admission (HTTP 401,403) |
| `add child to basis` | `POST /api/behandlinger/{behandlingId}/legg-til-barn` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_122_postOnLegg_til_barnCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `add condition` | `POST /api/vilkaarsvurdering/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_20_postOnVilkaarsvurdCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `add letter recipient` | `POST /api/brevmottaker/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_83_postOnBrevmottakReturns401 (401) | auth/admission (HTTP 401,403) |
| `add overpaid currency period` | `POST /api/feilutbetalt-valuta/behandling/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_72_postOnFeilutbetalt_valutaBehandlCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `add small child supplement correction` | `POST /api/småbarnstilleggkorrigering/behandling/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_66_postOnSm_barnstilleggkorrigeringBehandlCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `assess repayment` | `POST /api/behandlinger/{behandlingId}/steg/tilbakekreving` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_178_postOnTilbakekrevingReturns401 (401) | auth/admission (HTTP 401,403) |
| `assign task` | `POST /api/oppgave/{oppgaveId}/fordel` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_129_postOnFordelReturns401 (401) | auth/admission (HTTP 401,403) |
| `change treatment theme` | `PUT /api/behandlinger/{behandlingId}/behandlingstema` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_143_putOnBehandlingstemaReturns401 (401) | auth/admission (HTTP 401,403) |
| `check complaint revision creation` | `GET /api/klage/fagsaker/{fagsakId}/kan-opprette-revurdering-klage` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_168_getOnKan_opprette_revurdering_klageReturns401 (401) | auth/admission (HTTP 401,403) |
| `check incorrect cessation dates` | `POST /api/forvalter/sjekkOmTilkjentYtelseForBehandlingHarUkorrektOpphørsdato` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_31_postOnSjekkOmTilkjentYtelseForBehandlingHarUkorrektOpph_rsdatoCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `check ongoing Infotrygd case` | `POST /api/infotrygd/har-lopende-sak` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_30_postOnHar_lopende_sakCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `check open repayment case` | `GET /api/fagsaker/{fagsakId}/har-apen-tilbakekreving` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_61_getOnHar_apen_tilbakekrevingCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `check person access` | `POST /api/tilgang` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_6_postOnTilgangReturns401 (401) | auth/admission (HTTP 401,403) |
| `check rate change eligibility` | `GET /api/satsendring/{fagsakId}/kan-kjore-satsendring` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_88_getOnKan_kjore_satsendringReturnsObject (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `clear application task ownership` | `POST /api/oppgave/fjern-behandles-av-applikasjon` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_41_postOnFjern_behandles_av_applikasjonReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `complete task` | `GET /api/oppgave/{oppgaveId}/ferdigstill` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_110_getOnFerdigstillReturns401 (401) | auth/admission (HTTP 401,403) |
| `complete task and link journalpost` | `POST /api/oppgave/{oppgaveId}/ferdigstillOgKnyttjournalpost` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_130_postOnFerdigstillOgKnyttjournalpostReturns401 (401) | auth/admission (HTTP 401,403) |
| `create case` | `POST /api/fagsaker` | Ambiguous only | No — shared route rejected before discriminator | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_10_postOnFagsakerReturns401 (401) | auth/admission (HTTP 401,403) |
| `create changed payment share` | `POST /api/endretutbetalingandel/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_82_postOnEndretutbetalingandelReturns401 (401) | auth/admission (HTTP 401,403) |
| `create complaint revision` | `POST /api/klage/fagsaker/{fagsakId}/opprett-revurdering-klage/` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_172_postOnOpprett_revurdering_klageReturns401 (401) | auth/admission (HTTP 401,403) |
| `create complaint treatment` | `POST /api/fagsaker/{fagsakId}/opprett-klagebehandling` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_136_postOnOpprett_klagebehandlingReturns401 (401) | auth/admission (HTTP 401,403) |
| `create corrected after-payment metadata` | `POST /api/korrigertetterbetaling/behandling/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_70_postOnKorrigertetterbetalingBehandlCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `create corrected decision metadata` | `POST /api/korrigertvedtak/behandling/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_69_postOnKorrigertvedtakBehandlCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `create repayment treatment` | `GET /api/fagsaker/{fagsakId}/opprett-tilbakekreving` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_112_getOnOpprett_tilbakekrevingReturns401 (401) | auth/admission (HTTP 401,403) |
| `create treatment` | `POST /api/behandlinger` | Ambiguous only | No — shared route rejected before discriminator | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_5_postOnBehandlingerReturns401 (401) | auth/admission (HTTP 401,403) |
| `deactivate corrected after-payment metadata` | `PATCH /api/korrigertetterbetaling/behandling/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_81_patchOnKorrigertetterbetalingBehandlCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `deactivate corrected decision metadata` | `PATCH /api/korrigertvedtak/behandling/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_80_patchOnKorrigertvedtakBehandlCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `decide treatment` | `POST /api/behandlinger/{behandlingId}/steg/iverksett-vedtak` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_182_postOnIverksett_vedtakReturns401 (401) | auth/admission (HTTP 401,403) |
| `delete EØS refund period` | `DELETE /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_197_deleteOnBehandlingPeriodReturns401 (401) | auth/admission (HTTP 401,403) |
| `delete changed payment share` | `DELETE /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_75_deleteOnEndretutbetalingandelCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `delete competence interval` | `DELETE /api/kompetanse/{behandlingId}/{kompetanseId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_78_deleteOnKompetansCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `delete condition` | `DELETE /api/vilkaarsvurdering/{behandlingId}/vilkaar` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_77_deleteOnVilkaarCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `delete condition period` | `DELETE /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_74_deleteOnVilkaarsvurdCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `delete currency rate` | `DELETE /api/differanseberegning/valutakurs/{behandlingId}/{valutakursId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_161_deleteOnValutakurCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `delete foreign period amount` | `DELETE /api/differanseberegning/utenlandskperidebeløp/{behandlingId}/{utenlandskPeriodebeløpId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_162_deleteOnUtenlandskperidebel_pCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `delete letter recipient` | `DELETE /api/brevmottaker/{behandlingId}/{mottakerId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_79_deleteOnBrevmottakCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `delete overpaid currency period` | `DELETE /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_194_deleteOnPeriodCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `derive treatment result` | `POST /api/behandlinger/{behandlingId}/steg/behandlingsresultat` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_183_postOnBehandlingsresultatReturns401 (401) | auth/admission (HTTP 401,403) |
| `dismiss treatment` | `PUT /api/behandlinger/{behandlingId}/steg/henlegg` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_183_putOnHenleggReturns401 (401) | auth/admission (HTTP 401,403) |
| `fill condition dates in preprod` | `PUT /api/preprod/{behandlingId}/fyll-ut-vilkarsvurdering` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_139_putOnFyll_ut_vilkarsvurderingReturns401 (401) | auth/admission (HTTP 401,403) |
| `find all minimal cases for person` | `POST /api/fagsaker/hent-fagsaker-paa-person` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_33_postOnHent_fagsaker_paa_personCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `find cases to close` | `GET /api/forvalter/finnFagsakerSomSkalAvsluttes` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_30_getOnFinnFagsakerSomSkalAvsluttesReturnsEmptyList (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `find cases without latest rate` | `POST /api/satsendring/saker-uten-sats` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_40_postOnSaker_uten_satsReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `find invalid after-payment periods` | `GET /api/behandlinger/{behandlingId}/personer-med-ugyldig-etterbetalingsperiode` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_63_getOnPersoner_med_ugyldig_etterbetalingsperiodeCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `find migration duplicates` | `GET /api/forvalter/finnÅpneFagsakerMedFlereMigreringsbehandlinger` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_28_getOnFinn_pneFagsakerMedFlereMigreringsbehandlingerReturnsEmptyList (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `find migration duplicates with Infotrygd` | `GET /api/forvalter/finnÅpneFagsakerMedFlereMigreringsbehandlingerOgLøpendeSakIInfotrygd` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_29_getOnFinn_pneFagsakerMedFlereMigreringsbehandlingerOgL_pendeSakIInfotrygdReturnsEmptyList (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `find minimal case for person` | `POST /api/fagsaker/hent-fagsak-paa-person` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_81_postOnHent_fagsak_paa_personReturns401 (401) | auth/admission (HTTP 401,403) |
| `find payment-order issues` | `POST /api/forvalter/finnBehandlingerMedPotensieltFeilUtbetalingsoppdrag` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_47_postOnFinnBehandlingerMedPotensieltFeilUtbetalingsoppdragReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `finish admin task list` | `POST /api/forvalter/ferdigstill-oppgaver` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_48_postOnFerdigstill_oppgaverReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `generate decision letter` | `POST /api/dokument/vedtaksbrev/{vedtakId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_127_postOnVedtaksbrevReturns401 (401) | auth/admission (HTTP 401,403) |
| `generate letter explanation texts` | `GET /api/vedtaksperioder/brevbegrunnelser/{vedtaksperiodeId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_56_getOnBrevbegrunnelsCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `get change date` | `GET /api/behandlinger/{behandlingId}/endringstidspunkt` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_93_getOnEndringstidspunktReturnsObject (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `handle identity event` | `POST /api/ident` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_8_postOnIdentReturns401 (401) | auth/admission (HTTP 401,403) |
| `handle transitional benefit event` | `POST /api/overgangsstonad` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_7_postOnOvergangsstonadReturns401 (401) | auth/admission (HTTP 401,403) |
| `identify payments over 100 percent` | `POST /api/forvalter/identifiser-utbetalinger-over-100-prosent` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_46_postOnIdentifiser_utbetalinger_over_100_prosentReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `journal journalpost` | `POST /api/journalpost/{journalpostId}/journalfør/{oppgaveId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_156_postOnJournalf_rCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `list EØS refund periods` | `GET /api/refusjon-eøs/behandlinger/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_82_getOnBehandlingReturnsObject (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `list complaint treatments` | `GET /api/fagsaker/{fagsakId}/hent-klagebehandlinger` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_91_getOnHent_klagebehandlingerReturnsObject (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `list condition explanation texts` | `GET /api/vilkaarsvurdering/vilkaarsbegrunnelser` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_23_getOnVilkaarsbegrunnelserReturnsObject (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `list corrected after-payment metadata` | `GET /api/korrigertetterbetaling/behandling/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_51_getOnKorrigertetterbetalingBehandlCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `list decision periods` | `GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_163_getOnHent_vedtaksperioderReturnsObject (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `list letter recipients` | `GET /api/brevmottaker/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_8_getOnBrevmottakCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `list overpaid currency periods` | `GET /api/feilutbetalt-valuta/behandling/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_52_getOnFeilutbetalt_valutaBehandlCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `list tax persons` | `GET /api/skatt/personer` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_17_getOnPersonerCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `list tax persons test` | `GET /api/skatt/personer/test` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_57_getOnTestCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `list user journalposts` | `POST /api/journalpost/for-bruker` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_27_postOnFor_brukerCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `order pension yearly export` | `GET /api/ekstern/pensjon/bestill-personer-med-barnetrygd/{år}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_171_getOnBestill_personer_med_barnetrygdReturns401 (401) | auth/admission (HTTP 401) |
| `populate support dates for treatment` | `POST /api/forvalter/populer-stonad-fom-tom/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_71_postOnPopuler_stonad_fom_tomCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `populate support dates in bulk` | `POST /api/forvalter/populer-stonad-fom-tom-alle/{limit}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_96_postOnPopuler_stonad_fom_tom_alReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `preview case letter` | `POST /api/dokument/fagsak/{fagsakId}/forhaandsvis-brev` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_158_postOnForhaandsvis_brevCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `preview repayment warning letter` | `POST /api/tilbakekreving/{behandlingId}/forhandsvis-varselbrev` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_65_postOnForhandsvis_varselbrevCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `preview treatment letter` | `POST /api/dokument/forhaandsvis-brev/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_121_postOnForhaandsvis_brevCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `queue benefit statistics manually` | `POST /api/stonadsstatistikk/send-til-dvh-manuell` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_39_postOnSend_til_dvh_manuellReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `queue long-deadline dismissals` | `POST /api/satsendring/henleggBehandlingerMedLangFristSenereEnn/{valideringsdato}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_97_postOnHenleggBehandlingerMedLangFristSenereEnnReturns400 (400) | binding/business validation, auth/admission (HTTP 400,401,403) |
| `queue treatment from birth event` | `PUT /api/behandlinger` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_11_putOnBehandlingerReturns401 (401) | auth/admission (HTTP 401,403) |
| `queue unsent benefit statistics` | `POST /api/stonadsstatistikk/send-til-dvh` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_38_postOnSend_til_dvhReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `refresh register information` | `GET /api/person/oppdater-registeropplysninger/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_59_getOnOppdater_registeropplysningCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `regenerate decision periods` | `PUT /api/vedtaksperioder/endringstidspunkt` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_86_putOnEndringstidspunktReturns401 (401) | auth/admission (HTTP 401,403) |
| `register application` | `POST /api/behandlinger/{behandlingId}/steg/registrer-søknad` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_159_postOnRegistrer_s_knadCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `register institution and guardian` | `POST /api/behandlinger/{behandlingId}/steg/registrer-institusjon-og-verge` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_165_postOnRegistrer_institusjon_og_vergeReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `register manual death` | `POST /api/person/registrer-manuell-dodsfall/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_68_postOnRegistrer_manuell_dodsfalCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `register statistics sent` | `POST /api/saksstatistikk/registrer-sendt-fra-statistikk` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_24_postOnRegistrer_sendt_fra_statistikkCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `remove small child supplement correction` | `DELETE /api/småbarnstilleggkorrigering/behandling/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_76_deleteOnBehandlCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `resend corrected payment order version` | `POST /api/forvalter/sendKorrigertUtbetalingsoppdragForBehandling/{behandlingId}/{versjon}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_164_postOnSendKorrigertUtbetalingsoppdragForBehandlReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `resend corrected payment orders` | `POST /api/forvalter/sendKorrigertUtbetalingsoppdragForBehandlinger` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_42_postOnSendKorrigertUtbetalingsoppdragForBehandlingerReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `resend migration statistics` | `POST /api/stonadsstatistikk/ettersend-manuell-migrering/{dryRun}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_95_postOnEttersend_manuell_migrReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `reset task assignment` | `POST /api/oppgave/{oppgaveId}/tilbakestill` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_128_postOnTilbakestillReturns401 (401) | auth/admission (HTTP 401,403) |
| `reset treatment to treatment result` | `POST /api/endretutbetalingandel/{behandlingId}/tilbakestill` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_119_postOnTilbakestillCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `resolve case participants` | `POST /api/fagsaker/sok/fagsakdeltagere` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_123_postOnFagsakdeltagereReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `restart active early treatment` | `POST /api/behandlinger` | Ambiguous only | No — shared route rejected before discriminator | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_5_postOnBehandlingerReturns401 (401) | auth/admission (HTTP 401,403) |
| `restart small child supplement job` | `POST /api/forvalter/start-manuell-restart-av-smaabarnstillegg-jobb/skalOppretteOppgaver/{skalOppretteOppgaver}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_160_postOnSkalOppretteOppgavCauses500_internalServerError (500) | business missing-state, binding, infrastructure, or external-dependency failure (HTTP 500) |
| `resume treatment` | `PUT /api/sett-på-vent/{behandlingId}/fortsettbehandling` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_137_putOnFortsettbehandlingReturns401 (401) | auth/admission (HTTP 401,403) |
| `retrieve BISYS extended benefit` | `POST /api/bisys/hent-utvidet-barnetrygd` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_84_postOnHent_utvidet_barnetrygdReturns401 (401) | auth/admission (HTTP 401,403) |
| `retrieve EØS timelines` | `GET /api/tidslinjer/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_15_getOnTidslinjCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve Infotrygd benefits` | `POST /api/infotrygd/hent-infotrygdstonader-for-soker` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_28_postOnHent_infotrygdstonader_for_sokerCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve Infotrygd cases` | `POST /api/infotrygd/hent-infotrygdsaker-for-soker` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_29_postOnHent_infotrygdsaker_for_sokerCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve application statistics` | `GET /api/internstatistikk/antallSoknader` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_27_getOnAntallSoknaderReturnsObject (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `retrieve benefit statistics decisions` | `POST /api/stonadsstatistikk/vedtakV2` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_37_postOnVedtakV2Returns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `retrieve case statistics` | `GET /api/saksstatistikk/sak/{fagsakId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_89_getOnSakReturnsObject (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `retrieve collaborator by organization` | `GET /api/samhandler/orgnr/{orgnr}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_64_getOnOrgnrCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve complaint decisions` | `GET /api/klage/fagsaker/{fagsakId}/vedtak` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_167_getOnVedtakReturns401 (401) | auth/admission (HTTP 401,403) |
| `retrieve decision letter` | `GET /api/dokument/vedtaksbrev/{vedtakId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_53_getOnVedtaksbrevCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve feature toggles` | `POST /api/feature` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_4_postOnFeatureReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401) |
| `retrieve full case` | `GET /api/fagsaker/{fagsakId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_31_getOnFagsakReturnsObject (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `retrieve full person information` | `GET /api/person` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_4_getOnPersonCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve internal statistics` | `GET /api/internstatistikk` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_5_getOnInternstatistikkReturnsObject (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `retrieve journal document PDF` | `GET /api/journalpost/{journalpostId}/dokument/{dokumentInfoId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_155_getOnDokumentCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve journal document resource` | `GET /api/journalpost/{journalpostId}/hent/{dokumentInfoId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_154_getOnHentCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve journaling task data` | `GET /api/oppgave/{oppgaveId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_20_getOnOppgavCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve journalpost` | `GET /api/journalpost/{journalpostId}/hent` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_60_getOnHentCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve minimal case` | `GET /api/fagsaker/minimal/{fagsakId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_92_getOnMinimReturnsObject (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `retrieve open treatment deadlines` | `POST /api/oppgave/hent-frister-for-apne-utvidet-barnetrygd-behandlinger` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_26_postOnHent_frister_for_apne_utvidet_barnetrygd_behandlingerCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve pension child benefit` | `POST /api/ekstern/pensjon/hent-barnetrygd` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_126_postOnHent_barnetrygdReturns401 (401) | auth/admission (HTTP 401) |
| `retrieve person address` | `GET /api/person/adresse` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_19_getOnAdresseCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve simple person information` | `GET /api/person/enkel` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_18_getOnEnkelCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve tax periods` | `POST /api/skatt/perioder` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_21_postOnPerioderCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve tax periods test` | `POST /api/skatt/perioder/test` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_67_postOnTestCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_22_getOnBehandlingCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve treatment log` | `GET /api/logg/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_21_getOnLoggCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `retrieve treatment statistics` | `GET /api/saksstatistikk/behandling/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_90_getOnBehandlReturnsObject (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `return existing case` | `POST /api/fagsaker` | Ambiguous only | No — shared route rejected before discriminator | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_10_postOnFagsakerReturns401 (401) | auth/admission (HTTP 401,403) |
| `run consistency dry run` | `POST /api/konsistensavstemming/dryrun` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_66_postOnDryrunReturns401 (401) | auth/admission (HTTP 401,403) |
| `run consistency reconciliation` | `POST /api/konsistensavstemming/run` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_65_postOnRunReturns401 (401) | auth/admission (HTTP 401,403) |
| `run rate change without validation` | `POST /api/forvalter/kjor-satsendring-uten-validering` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_45_postOnKjor_satsendring_uten_valideringReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `run synchronous rate change` | `PUT /api/satsendring/{fagsakId}/kjor-satsendring-synkront` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_138_putOnKjor_satsendring_synkrontReturns401 (401) | auth/admission (HTTP 401,403) |
| `search case participants` | `POST /api/fagsaker/sok` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_32_postOnSokCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `search cases where person participates` | `POST /api/fagsaker/sok/fagsaker-hvor-person-er-deltaker` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_118_postOnFagsaker_hvor_person_er_deltakerCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `search cases with ongoing benefit for person` | `POST /api/fagsaker/sok/fagsaker-hvor-person-mottar-lopende-ytelse` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_73_postOnFagsaker_hvor_person_mottar_lopende_ytelseCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `search collaborator` | `POST /api/samhandler/navn` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_50_postOnNavnReturns400 (400) | binding/business validation, auth/admission (HTTP 400,401,403) |
| `search tasks` | `POST /api/oppgave/hent-oppgaver` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_25_postOnHent_oppgaverCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `send case letter` | `POST /api/dokument/fagsak/{fagsakId}/send-brev` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_157_postOnSend_brevCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `send payment orders administratively` | `POST /api/forvalter/lag-og-send-utbetalingsoppdrag-til-økonomi` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_44_postOnLag_og_send_utbetalingsoppdrag_til__konomiReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `send to decision maker` | `POST /api/behandlinger/{behandlingId}/steg/send-til-beslutter` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_179_postOnSend_til_beslutterReturns401 (401) | auth/admission (HTTP 401,403) |
| `send treatment letter` | `POST /api/dokument/send-brev/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_120_postOnSend_brevCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `set historical ISK rate manually` | `PUT /api/differanseberegning/valutakurs/{behandlingId}` | Ambiguous only | No — shared route rejected before discriminator | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_141_putOnValutakurReturns401 (401) | auth/admission (HTTP 401,403) |
| `set treatment on wait` | `POST /api/sett-på-vent/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_19_postOnSett_p__ventCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `trigger rate change for case` | `GET /api/satsendring/kjorsatsendring/{fagsakId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_58_getOnKjorsatsendrCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `trigger rate change for cases` | `POST /api/satsendring/kjorsatsendring` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_22_postOnKjorsatsendringCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `trigger rate change from identities` | `POST /api/satsendring/kjorsatsendringForListeMedIdenter` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_23_postOnKjorsatsendringForListeMedIdenterCauses500_internalServerError (500) | auth/admission, business missing-state, binding, infrastructure, or external-dependency failure (HTTP 401,403,500) |
| `update EØS refund period` | `PUT /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_195_putOnBehandlingPeriodReturns401 (401) | auth/admission (HTTP 401,403) |
| `update case ongoing status` | `POST /api/forvalter/oppdaterLøpendeStatusPåFagsaker` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_43_postOnOppdaterL_pendeStatusP_FagsakerReturns200 (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |
| `update changed payment share` | `PUT /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_140_putOnEndretutbetalingandelReturns401 (401) | auth/admission (HTTP 401,403) |
| `update condition` | `PUT /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_134_putOnVilkaarsvurdReturns401 (401) | auth/admission (HTTP 401,403) |
| `update currency rate from ECB` | `PUT /api/differanseberegning/valutakurs/{behandlingId}` | Ambiguous only | No — shared route rejected before discriminator | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_141_putOnValutakurReturns401 (401) | auth/admission (HTTP 401,403) |
| `update decision free texts` | `PUT /api/vedtaksperioder/fritekster/{vedtaksperiodeId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_136_putOnFritekstReturns401 (401) | auth/admission (HTTP 401,403) |
| `update foreign period amount` | `PUT /api/differanseberegning/utenlandskperidebeløp/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_142_putOnUtenlandskperidebel_pReturns401 (401) | auth/admission (HTTP 401,403) |
| `update other assessment` | `PUT /api/vilkaarsvurdering/{behandlingId}/annenvurdering/{annenVurderingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_182_putOnAnnenvurdReturns401 (401) | auth/admission (HTTP 401,403) |
| `update overpaid currency period` | `PUT /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_196_putOnBehandlPeriodReturns401 (401) | auth/admission (HTTP 401,403) |
| `update standard explanations` | `PUT /api/vedtaksperioder/standardbegrunnelser/{vedtaksperiodeId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_135_putOnStandardbegrunnelsReturns401 (401) | auth/admission (HTTP 401,403) |
| `update wait` | `PUT /api/sett-på-vent/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_91_putOnSett_p__ventReturns401 (401) | auth/admission (HTTP 401,403) |
| `upsert competence interval` | `PUT /api/kompetanse/{behandlingId}` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_92_putOnKompetansReturns401 (401) | auth/admission (HTTP 401,403) |
| `validate conditions` | `POST /api/behandlinger/{behandlingId}/steg/vilkårsvurdering` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_177_postOnVilk_rsvurderingReturns401 (401) | auth/admission (HTTP 401,403) |
| `validate treatment result` | `GET /api/behandlinger/{behandlingId}/steg/behandlingsresultat/valider` | Yes | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_195_getOnValiderReturnsObject (200) | successful or application-wrapped result, auth/admission (HTTP 200,401,403) |

Ambiguous shared-route attempts:
- `POST /api/behandlinger`: `create treatment`, `restart active early treatment`. Generated requests were rejected at 401/403 before state/body discrimination, so no exact function received invocation credit.
- `POST /api/fagsaker`: `create case`, `return existing case`. Generated requests were rejected at 401/403 before state/body discrimination, so no exact function received invocation credit.
- `PUT /api/differanseberegning/valutakurs/{behandlingId}`: `set historical ISK rate manually`, `update currency rate from ECB`. Generated requests were rejected at 401/403 before state/body discrimination, so no exact function received invocation credit.

## Behavior Details

### `B1`: `Establish a case and reuse the case identity`

- Business goal: Create the domain parent case for later treatments, letters, complaints, repayment checks, and statistics.
- Starting point: `No prior service state`
- Expected business result: Exactly one case exists for the business key. Repeating the creation call returns the same domain case rather than creating a duplicate.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create case` | `POST /api/fagsaker` with body `personIdent=P1`, `fagsakType=NORMAL` to create a case and capture `RestMinimalFagsak.id=fagsakId` | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_10_postOnFagsakerReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_9_postOnFagsakerReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `return existing case` | `POST /api/fagsaker` with body `personIdent=P1`, `fagsakType=NORMAL` to return the existing case for the same key | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_10_postOnFagsakerReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_9_postOnFagsakerReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve full case` | `GET /api/fagsaker/{fagsakId}` with `fagsakId` from step 1 to inspect treatments and repayment treatments | No | No context-valid verification with required binding/state. |
| 2 | `retrieve minimal case` | `GET /api/fagsaker/minimal/{fagsakId}` with the same `fagsakId` to inspect compact case data | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `create case` | `FagsakService.hentEllerOpprettFagsak institution guard` | `fagsakType=INSTITUSJON` is requested without `institusjon.orgNummer`. Why: The owned service cannot form the institution-scoped uniqueness key and throws a functional exception. Constraint: An institution case must identify its organization. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: `T1` and `T2`.

### `B2`: `Inspect case views and locate cases by person`

- Business goal: Read case state by known `fagsakId` and by person/type lookup.
- Starting point: `Existing service state`
- Expected business result: The service returns full and compact views for the same case, and person-based lookup confirms the case can be found through the applicant/type key.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve full case` | `GET /api/fagsaker/{fagsakId}` with `fagsakId=F1` to retrieve the full case | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_31_getOnFagsakReturnsObject (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_49_getOnFagsakReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `retrieve minimal case` | `GET /api/fagsaker/minimal/{fagsakId}` with `fagsakId=F1` to retrieve compact case data | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_92_getOnMinimReturnsObject (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_115_getOnMinimReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 3 | `find minimal case for person` | `POST /api/fagsaker/hent-fagsak-paa-person` with body `personIdent=P1`, `fagsakType=NORMAL` to locate the case by person and type | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_81_postOnHent_fagsak_paa_personReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_40_postOnHent_fagsak_paa_personReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 4 | `find all minimal cases for person` | `POST /api/fagsaker/hent-fagsaker-paa-person` with body `personIdent=P1`, `fagsakTyper=[NORMAL]` to locate matching case set | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_33_postOnHent_fagsaker_paa_personCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_80_postOnHent_fagsaker_paa_personReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `find minimal case for person` | `FagsakService branch for no case exists for personIdent=P1 and fagsakType=NORMAL` | no case exists for `personIdent=P1` and `fagsakType=NORMAL`. Why: service returns a failure resource when no matching case is found. Constraint: required case state does not exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve full case` | `FagsakService.hentPåFagsakId not-found outcome` | No case exists for `fagsakId`. Why: The aggregate view cannot be built. Constraint: The case must exist. | Covered | High | Test_0::test_31_getOnFagsakReturnsObject: id 553; exact `Finner ikke fagsak med id 553`. | Reset-empty state establishes the condition; source throws before mutation. No direct post-failure DB query. | report.xml: FagsakService.kt 272 has 16 covered instructions and 1/2 branches; line 273 covered. |
| `retrieve minimal case` | `FagsakService.hentPåFagsakId not-found outcome` | No case exists for `fagsakId`. Why: The compact view cannot be built. Constraint: The case must exist. | Covered | High | Test_0::test_92_getOnMinimReturnsObject: id 653; exact `Finner ikke fagsak med id 653`. | Reset-empty state establishes the condition; source throws before mutation. No direct post-failure DB query. | report.xml: FagsakService.kt 272 has 16 covered instructions and 1/2 branches; line 273 covered. |

- Required-step summary: attempted `4/4`, application reached `3/4`, context-valid success `0/4`
- Happy-path summary: `0/1`
- Failure summary: `2/3`
- Behavior outcome checklist summary: `2/4`
- Status and confidence: `Partially Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B3`: `Resolve case participation and ongoing benefit relationships`

- Business goal: Determine where a person participates in child-benefit cases and whether the person has ongoing benefit.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The caller receives participant rows and case ids showing applicant/child participation and current benefit relationships.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `search case participants` | `POST /api/fagsaker/sok` with body `personIdent=P1` to search participant records | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_32_postOnSokCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_79_postOnSokReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `resolve case participants` | `POST /api/fagsaker/sok/fagsakdeltagere` with body `personIdent=P1`, `barnasIdenter=[C1]` to resolve applicant and child participants | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_123_postOnFagsakdeltagereReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_124_postOnFagsakdeltagereReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 3 | `search cases where person participates` | `POST /api/fagsaker/sok/fagsaker-hvor-person-er-deltaker` with body `personIdent=P1` to return cases where the person participates | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_118_postOnFagsaker_hvor_person_er_deltakerCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_138_postOnFagsaker_hvor_person_er_deltakerReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 4 | `search cases with ongoing benefit for person` | `POST /api/fagsaker/sok/fagsaker-hvor-person-mottar-lopende-ytelse` with body `personIdent=P1` to return cases with ongoing ordinary or extended benefit | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_73_postOnFagsaker_hvor_person_mottar_lopende_ytelseCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_137_postOnFagsaker_hvor_person_mottar_lopende_ytelseReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve full case` | `GET /api/fagsaker/{fagsakId}` with a `fagsakId` returned by steps 3 or 4 to inspect the case | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `resolve case participants` | `search methods branch for applicant or child identifier cannot be resolved` | applicant or child identifier cannot be resolved. Why: controller catches actor/participant lookup failures and returns a failure resource. Constraint: identifiers must resolve to actors. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `search cases with ongoing benefit for person` | `actor-resolution negative result` | The identity cannot be resolved to an actor. Why: The query has no domain subject. Constraint: Identity must resolve to an actor. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `4/4`, application reached `4/4`, context-valid success `0/4`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 2 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B4`: `Create and check manual repayment treatment on a case`

- Business goal: Create a repayment treatment for a case and inspect whether open repayment state exists.
- Starting point: `Existing service state`
- Expected business result: A repayment treatment is created or triggered for the case, and open repayment state can be observed through the case-scoped check.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create repayment treatment` | `GET /api/fagsaker/{fagsakId}/opprett-tilbakekreving` with `fagsakId=F1` to create a repayment treatment | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_112_getOnOpprett_tilbakekrevingReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_148_getOnOpprett_tilbakekrevingReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `check open repayment case` | `GET /api/fagsaker/{fagsakId}/har-apen-tilbakekreving` with `fagsakId=F1` to inspect open repayment state | No | No context-valid verification with required binding/state. |
| 2 | `retrieve full case` | `GET /api/fagsaker/{fagsakId}` with `fagsakId=F1` to inspect repayment treatments on the full case view | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `create repayment treatment` | `TilbakekrevingController branch for fagsakId=F1 does not identify an existing case` | `fagsakId=F1` does not identify an existing case. Why: repayment service cannot create repayment treatment without the parent case. Constraint: parent `fagsakId` must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `create repayment treatment` | `KanBehandlingOpprettesManueltRespons.kanBehandlingOpprettes=false` | The domain precheck rejects manual creation. Why: The explicit business rejection is returned. Constraint: The case must be eligible. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `create repayment treatment` | `kravgrunnlagsreferanse lifecycle outcome` | The reference is unknown or belongs to a non-closed repayment treatment. Why: It cannot be reused. Constraint: The reference must exist in the required state. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 3 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B5`: `Create a treatment and restart an early active treatment`

- Business goal: Start a treatment on a case, then reuse and reset it when another creation request arrives before decision stage.
- Starting point: `Existing service state`
- Expected business result: One active treatment exists under the case. The second creation request does not create a second active treatment; it resets the existing early treatment to initial step state.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create treatment` | `POST /api/behandlinger` with body `fagsakId=F1`, `søkersIdent=P1`, `behandlingType=FØRSTEGANGSBEHANDLING`, `behandlingÅrsak=SØKNAD`, `søknadMottattDato=D1` to create a treatment and capture `behandlingId=B1` | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_5_postOnBehandlingerReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_1_postOnBehandlingerReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `restart active early treatment` | `POST /api/behandlinger` with the same body values and `fagsakId=F1` while treatment `B1` is before `BESLUTTE_VEDTAK` to reset and reuse the active treatment | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_5_postOnBehandlingerReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_1_postOnBehandlingerReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect current step, person basis, and active decision | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `create treatment` | `BehandlingService.opprettBehandling branch for fagsakId=F404 does not exist` | `fagsakId=F404` does not exist. Why: `BehandlingService.opprettBehandling` throws when the case cannot be found. Constraint: treatment must belong to an existing case. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `create treatment` | `BehandlingService.opprettBehandling branch for required søkersIdent, søknadMottattDato, or nyMigreringsdato is missing for the chosen type/reason` | required `søkersIdent`, `søknadMottattDato`, or `nyMigreringsdato` is missing for the chosen type/reason. Why: `NyBehandling` validates these fields. Constraint: request body must satisfy treatment-type requirements. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `restart active early treatment` | `BehandlingService.opprettBehandling branch for active treatment is at or after BESLUTTE_VEDTAK` | active treatment is at or after `BESLUTTE_VEDTAK`. Why: implementation throws functional error for active unfinished decision-stage treatment. Constraint: only pre-decision active treatments can be reset through creation. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `create treatment` | `BehandlingService revision prerequisite guard` | A revision has no prior implemented decision. Why: There is no decision to revise. Constraint: A prior decision is required. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `create treatment` | `BehandlingService active-Infotrygd guard` | An active Infotrygd case blocks local creation. Why: Parallel processing is ineligible. Constraint: The case must be locally eligible. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `create treatment` | `BehandlingService active manual-migration guard` | An ongoing manual migration already exists. Why: A conflicting lifecycle is rejected. Constraint: Only one active migration may exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `create treatment` | `BehandlingService migration-rate prerequisite` | No latest applicable migration rate exists. Why: Calculation basis is incomplete. Constraint: The applicable rate must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `restart active early treatment` | `BehandlingService restart-step guard` | The treatment reached or passed decision. Why: It is too late to reset. Constraint: Only an early treatment may restart. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/8`
- Behavior outcome checklist summary: `0/9`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 8 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B6`: `Queue automated birth-event treatment processing`

- Business goal: Accept a birth event and queue asynchronous treatment processing.
- Starting point: `No prior service state`
- Expected business result: A task for birth-event handling exists; no immediate treatment id is returned.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `queue treatment from birth event` | `PUT /api/behandlinger` with body `morsIdent=P1`, `barnasIdenter=[C1]` to create the birth-event processing task | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_11_putOnBehandlingerReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_10_putOnBehandlingerReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B7`: `Change treatment theme`

- Business goal: Change treatment category and subcategory while treatment is editable.
- Starting point: `Existing service state`
- Expected business result: Treatment theme values are changed on the existing treatment.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `change treatment theme` | `PUT /api/behandlinger/{behandlingId}/behandlingstema` with `behandlingId=B1` and body `behandlingKategori=NASJONAL`, `behandlingUnderkategori=ORDINÆR` to update theme | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_143_putOnBehandlingstemaReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_77_putOnBehandlingstemaReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect the updated theme | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `change treatment theme` | `update method branch for treatment is not editable` | treatment is not editable. Why: controller validates editability before service update. Constraint: only editable active treatments can be changed. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B8`: `Execute the caseworker treatment step flow through decision`

- Business goal: Move a treatment from application registration through result, repayment assessment, decision-maker handoff, and decision implementation.
- Starting point: `Existing service state`
- Expected business result: Treatment has completed the caseworker/decision-maker API-visible flow, with result and decision state persisted and implementation continuation triggered by internal step logic.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `register application` | `POST /api/behandlinger/{behandlingId}/steg/registrer-søknad` with `behandlingId=B1` and body `RestRegistrerSøknad` to store application data | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_159_postOnRegistrer_s_knadCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_180_postOnRegistrer_s_knadReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `validate conditions` | `POST /api/behandlinger/{behandlingId}/steg/vilkårsvurdering` with `behandlingId=B1` to validate condition assessment | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_177_postOnVilk_rsvurderingReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_189_postOnVilk_rsvurderingReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 3 | `derive treatment result` | `POST /api/behandlinger/{behandlingId}/steg/behandlingsresultat` with `behandlingId=B1` to derive result and decision periods | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_183_postOnBehandlingsresultatReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_193_postOnBehandlingsresultatReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 4 | `assess repayment` | `POST /api/behandlinger/{behandlingId}/steg/tilbakekreving` with `behandlingId=B1` and body `RestTilbakekreving` to store repayment assessment | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_178_postOnTilbakekrevingReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_190_postOnTilbakekrevingReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 5 | `send to decision maker` | `POST /api/behandlinger/{behandlingId}/steg/send-til-beslutter` with `behandlingId=B1`, query `behandlendeEnhet=E1` to send the treatment to decision maker | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_179_postOnSend_til_beslutterReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_191_postOnSend_til_beslutterReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 6 | `decide treatment` | `POST /api/behandlinger/{behandlingId}/steg/iverksett-vedtak` with `behandlingId=B1` and body `beslutning=GODKJENT`, `begrunnelse=null`, `kontrollerteSider=[]` to record the decision | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_182_postOnIverksett_vedtakReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_192_postOnIverksett_vedtakReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `validate treatment result` | `GET /api/behandlinger/{behandlingId}/steg/behandlingsresultat/valider` with `behandlingId=B1` before step 3 to inspect readiness without advancing | No | No context-valid verification with required binding/state. |
| 2 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` after step 6 to inspect step/status | No | No context-valid verification with required binding/state. |
| 3 | `list decision periods` | `GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder` with `behandlingId=B1` to inspect generated periods | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `register application` | `StegService.utførSteg branch for treatment has advanced beyond application registration and request differs from existing application basis` | treatment has advanced beyond application registration and request differs from existing application basis. Why: `StegService` rejects executing an earlier caseworker step than the current step. Constraint: required step ordering. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `validate conditions` | `StegService.utførSteg branch for treatment status is SATT_PÅ_VENT` | treatment status is `SATT_PÅ_VENT`. Why: `StegService.validerBehandlingIkkeSattPåVent` rejects step execution. Constraint: waiting treatment cannot advance steps. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `validate conditions` | `VilkårsvurderingValidering required age-18 outcome` | The required age-18 condition is absent. Why: Eligibility validation is incomplete. Constraint: All required conditions must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `derive treatment result` | `BehandlingsresultatValidering named outcome` | Results or periods are incomplete or inconsistent. Why: The result cannot be finalized. Constraint: Results and periods must be consistent. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `assess repayment` | `TilbakekrevingService strategy-mismatch guard` | Assessment conflicts with calculated overpayment strategy. Why: The choice is incompatible. Constraint: Assessment must match calculation. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `send to decision maker` | `AnnenVurderingService unanswered-assessment guard` | A required other assessment is unanswered. Why: Decision control cannot start. Constraint: All assessments must be answered. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `decide treatment` | `TotrinnskontrollService control guards` | Control is missing or self-approved. Why: Two-step control is invalid. Constraint: A distinct controller and complete result are required. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `decide treatment` | `TilkjentYtelseValidering percentage guard` | Payment shares exceed 100 percent. Why: Implementation rejects over-allocation. Constraint: Total share may not exceed 100 percent. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `6/6`, application reached `0/6`, context-valid success `0/6`
- Happy-path summary: `0/1`
- Failure summary: `0/8`
- Behavior outcome checklist summary: `0/9`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 8 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B9`: `Dismiss an active treatment`

- Business goal: Close a treatment by dismissal rather than full decision implementation.
- Starting point: `Existing service state`
- Expected business result: The treatment is no longer an ordinary active treatment and carries a dismissal result/reason.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `dismiss treatment` | `PUT /api/behandlinger/{behandlingId}/steg/henlegg` with `behandlingId=B1` and body `årsak=FEILAKTIG_OPPRETTET`, `begrunnelse=R1` to dismiss the treatment | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_183_putOnHenleggReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_82_putOnHenleggReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect status/result | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `dismiss treatment` | `dismissal guards branch for treatment has been sent to external services` | treatment has been sent to external services. Why: controller calls `validerBehandlingIkkeSendtTilEksterneTjenester`. Constraint: externally sent treatments cannot be dismissed through this endpoint. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `dismiss treatment` | `dismissal guards branch for årsak=TEKNISK_VEDLIKEHOLD while feature toggle is disabled` | `årsak=TEKNISK_VEDLIKEHOLD` while feature toggle is disabled. Why: dismissal-type validation rejects the reason. Constraint: feature-gated dismissal reason is not enabled. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `dismiss treatment` | `BehandlingValidering external-finality guards` | A payment order, repayment instruction, external send, or distributed decision finalized state. Why: Dismissal conflicts with external finality. Constraint: No external finality may exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 3 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B10`: `Register institution and guardian information on treatment`

- Business goal: Store institution and/or guardian information required by the treatment flow.
- Starting point: `Existing service state`
- Expected business result: The treatment has persisted institution and/or guardian step data.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `register institution and guardian` | `POST /api/behandlinger/{behandlingId}/steg/registrer-institusjon-og-verge` with `behandlingId=B1` and body containing valid institution or guardian fields to persist the step data | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_165_postOnRegistrer_institusjon_og_vergeReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_181_postOnRegistrer_institusjon_og_vergeReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect institution/guardian data | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `register institution and guardian` | `registration method branch for request contains neither valid institution data nor valid guardian data` | request contains neither valid institution data nor valid guardian data. Why: controller returns `Ressurs.failure("Ugydig verge info")`. Constraint: registration must provide meaningful institution or guardian information. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B11`: `Add a child to treatment basis and reset later treatment steps`

- Business goal: Add a child into the treatment person basis after treatment creation.
- Starting point: `Existing service state`
- Expected business result: The child exists in the treatment basis, and treatment state is reset so later assessments can be redone with the new child.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `add child to basis` | `POST /api/behandlinger/{behandlingId}/legg-til-barn` with `behandlingId=B1` and body `barnIdent=C1` to add the child | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_122_postOnLegg_til_barnCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_130_postOnLegg_til_barnReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect person basis and current step | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `add child to basis` | `child-basis methods branch for treatment is closed or locked` | treatment is closed or locked. Why: implementation validates editability. Constraint: person basis can be changed only on editable treatments. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `add child to basis` | `child-basis methods branch for barnIdent=C1 cannot be resolved or processed` | `barnIdent=C1` cannot be resolved or processed. Why: person lookup/validation fails while building basis. Constraint: child must be a valid treatment person. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `add child to basis` | `PersongrunnlagService missing-basis guard` | No active person basis exists. Why: There is no basis to extend. Constraint: An active basis is required. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `add child to basis` | `PersongrunnlagService duplicate-child guard` | The child already belongs to the basis. Why: A duplicate relation is rejected. Constraint: A child may occur only once. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `add child to basis` | `PersongrunnlagService post-refresh guard` | The child remains absent after refresh. Why: The relation was not established. Constraint: The refreshed basis must contain the child. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/5`
- Behavior outcome checklist summary: `0/6`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 5 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B12`: `Put treatment on wait, update wait, and resume`

- Business goal: Pause a treatment with a deadline/reason, change the wait metadata, then resume the treatment.
- Starting point: `Existing service state`
- Expected business result: The treatment is paused, metadata is changed, then the active wait is deactivated and treatment resumes investigation status.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `set treatment on wait` | `POST /api/sett-på-vent/{behandlingId}` with `behandlingId=B1` and body `frist=D_future`, `årsak=A1` to place the treatment on wait | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_19_postOnSett_p__ventCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_51_postOnSett_p__ventReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `update wait` | `PUT /api/sett-på-vent/{behandlingId}` with `behandlingId=B1` and body `frist=D_later`, `årsak=A2` to update active wait metadata | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_91_putOnSett_p__ventReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_46_putOnSett_p__ventReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 3 | `resume treatment` | `PUT /api/sett-på-vent/{behandlingId}/fortsettbehandling` with `behandlingId=B1` to resume the treatment | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_137_putOnFortsettbehandlingReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_72_putOnFortsettbehandlingReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` after step 1 or 3 to inspect status and active wait | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `set treatment on wait` | `wait-state guards branch for treatment already has active wait` | treatment already has active wait. Why: `validerBehandlingKanSettesPåVent` rejects duplicate active wait. Constraint: only one active wait record per treatment. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `set treatment on wait` | `wait-state guards branch for frist is before today` | `frist` is before today. Why: deadline validation rejects past deadlines. Constraint: wait deadline must be current/future. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `update wait` | `wait-state guards branch for new frist and årsak equal existing values` | new `frist` and `årsak` equal existing values. Why: service rejects no-op updates. Constraint: update must change wait state. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `resume treatment` | `wait-state guards branch for no active wait exists` | no active wait exists. Why: `gjenopptaBehandling` requires active wait and waiting status. Constraint: treatment must currently be on wait. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `set treatment on wait` | `SettPåVentUtils wait-state guards` | An active wait exists, deadline is past, or lifecycle disallows waiting. Why: The transition is rejected. Constraint: Eligible state, no wait, and a non-past deadline are required. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `update wait` | `SettPåVentService active-wait/deadline guards` | No active wait exists or the new deadline is past. Why: No mutable valid wait exists. Constraint: An active wait and valid deadline are required. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `resume treatment` | `SettPåVentUtils resume-state guard` | No active wait exists or it is not manually resumable, including machine wait. Why: Resume is invalid. Constraint: A manual resumable wait is required. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `3/3`, application reached `0/3`, context-valid success `0/3`
- Happy-path summary: `0/1`
- Failure summary: `0/7`
- Behavior outcome checklist summary: `0/8`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 7 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B13`: `Read person information for case handling`

- Business goal: Retrieve detailed, simple, and address-focused person information for casework.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The caller receives person information suitable for casework and address/recipient decisions.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve full person information` | `GET /api/person` with header `personIdent=P1` to read detailed person information | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_4_getOnPersonCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_6_getOnPersonReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `retrieve simple person information` | `GET /api/person/enkel` with header `personIdent=P1` to read simple person information | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_18_getOnEnkelCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_41_getOnEnkelReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 3 | `retrieve person address` | `GET /api/person/adresse` with header `personIdent=P1` to read name and address | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_19_getOnAdresseCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_42_getOnAdresseReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `retrieve full person information` | `PersonopplysningerService missing-birth-data outcome` | Required birth information is absent. Why: The requested domain person view cannot be constructed. Constraint: Required birth information must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve full person information` | `PersonopplysningerService discontinued-identity outcome` | The identity has ceased without a usable current identity. Why: The person cannot be resolved as an active domain subject. Constraint: A usable current identity must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve full person information` | `PersonopplysningerService actor-not-found outcome` | No domain actor resolves for the supplied identity. Why: The person-scoped operation has no persisted subject. Constraint: The identity must resolve to a domain actor. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve simple person information` | `PersonopplysningerService missing-birth-data outcome` | Required birth information is absent. Why: The requested domain person view cannot be constructed. Constraint: Required birth information must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve simple person information` | `PersonopplysningerService discontinued-identity outcome` | The identity has ceased without a usable current identity. Why: The person cannot be resolved as an active domain subject. Constraint: A usable current identity must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve simple person information` | `PersonopplysningerService actor-not-found outcome` | No domain actor resolves for the supplied identity. Why: The person-scoped operation has no persisted subject. Constraint: The identity must resolve to a domain actor. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve person address` | `PersonopplysningerService missing-birth-data outcome` | Required birth information is absent. Why: The requested domain person view cannot be constructed. Constraint: Required birth information must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve person address` | `PersonopplysningerService discontinued-identity outcome` | The identity has ceased without a usable current identity. Why: The person cannot be resolved as an active domain subject. Constraint: A usable current identity must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve person address` | `PersonopplysningerService actor-not-found outcome` | No domain actor resolves for the supplied identity. Why: The person-scoped operation has no persisted subject. Constraint: The identity must resolve to a domain actor. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `3/3`, application reached `3/3`, context-valid success `0/3`
- Happy-path summary: `0/1`
- Failure summary: `0/9`
- Behavior outcome checklist summary: `0/10`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 9 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B14`: `Refresh treatment register basis and manually record death`

- Business goal: Update treatment person basis from register information and record manual death details for a person in the treatment.
- Starting point: `Existing service state`
- Expected business result: Register information is refreshed and manual death information exists on the relevant treatment person.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `refresh register information` | `GET /api/person/oppdater-registeropplysninger/{behandlingId}` with `behandlingId=B1` to refresh register basis | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_59_getOnOppdater_registeropplysningCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_109_getOnOppdater_registeropplysningReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `register manual death` | `POST /api/person/registrer-manuell-dodsfall/{behandlingId}` with `behandlingId=B1` and body `personIdent=P1`, `dødsfallDato=D_death`, `begrunnelse=R1` to store manual death data | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_68_postOnRegistrer_manuell_dodsfalCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_127_postOnRegistrer_manuell_dodsfalReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect treatment person basis | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `register manual death` | `register/death methods branch for personIdent=P2 is not part of treatment B1` | `personIdent=P2` is not part of treatment `B1`. Why: service validates treatment/person relationship. Constraint: manual death can only be recorded for a treatment participant. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `refresh register information` | `register/death methods branch for behandlingId=B404 does not exist` | `behandlingId=B404` does not exist. Why: treatment lookup fails. Constraint: treatment must exist. | Covered | Medium | Test_0::test_59_getOnOppdater_registeropplysningCauses500_internalServerError: id 369; exact missing-treatment message. | Reset-empty state establishes the condition; source throws before mutation. No direct post-failure DB query. | report.xml: BehandlingHentOgPersisterService.kt 40 has 7 covered instructions and 1/2 branches; line 41 covered. |
| `register manual death` | `PersongrunnlagService duplicate-death guard` | A death date is already registered. Why: A conflicting second record is rejected. Constraint: Only one effective death date may exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `register manual death` | `PersongrunnlagService death-before-birth guard` | Death date precedes birth date. Why: The chronology is impossible. Constraint: Death cannot precede birth. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `register manual death` | `missing-condition-assessment outcome` | Dependent condition assessment is absent. Why: Eligibility state cannot be updated. Constraint: Required assessments must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `1/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `1/5`
- Behavior outcome checklist summary: `1/6`
- Status and confidence: `Partially Covered` / `High`
- Exact gap: Missing continuous documented happy path and 4 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B15`: `Maintain condition assessment records`

- Business goal: Add, update, delete, and supplement condition assessment data on an editable treatment.
- Starting point: `Existing service state`
- Expected business result: Condition assessment data is changed, deleted, and supplemented; later result/decision state is reset or invalidated by these changes.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `add condition` | `POST /api/vilkaarsvurdering/{behandlingId}` with `behandlingId=B1` and body `RestNyttVilkår` to add a condition and capture `vilkaarId=V1` from the returned treatment | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_20_postOnVilkaarsvurdCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_52_postOnVilkaarsvurdReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `update condition` | `PUT /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}` with `behandlingId=B1`, `vilkaarId=V1`, and body `RestPersonResultat` to update the condition | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_134_putOnVilkaarsvurdReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_69_putOnVilkaarsvurdReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 3 | `delete condition period` | `DELETE /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}` with `behandlingId=B1`, `vilkaarId=V1`, and body `personIdent=P1` to delete one condition period | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_74_deleteOnVilkaarsvurdCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_139_deleteOnVilkaarsvurdReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 4 | `add condition` | `POST /api/vilkaarsvurdering/{behandlingId}` with `behandlingId=B1` and body `RestNyttVilkår` to create another condition for deletion and capture `vilkaarId=V2` | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_20_postOnVilkaarsvurdCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_52_postOnVilkaarsvurdReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 5 | `delete condition` | `DELETE /api/vilkaarsvurdering/{behandlingId}/vilkaar` with `behandlingId=B1` and body `RestSlettVilkår` referencing `vilkaarId=V2` to delete the condition | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_77_deleteOnVilkaarCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_142_deleteOnVilkaarReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 6 | `update other assessment` | `PUT /api/vilkaarsvurdering/{behandlingId}/annenvurdering/{annenVurderingId}` with `behandlingId=B1`, `annenVurderingId=A1`, and body `RestAnnenVurdering` to update other assessment state | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_182_putOnAnnenvurdReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_81_putOnAnnenvurdReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect condition and assessment data | No | No context-valid verification with required binding/state. |
| 2 | `list condition explanation texts` | `GET /api/vilkaarsvurdering/vilkaarsbegrunnelser` to inspect available explanation metadata | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_23_getOnVilkaarsbegrunnelserReturnsObject (200) |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `update condition` | `VilkårsvurderingValidering branch for vilkaarId=V404 does not identify a condition in the treatment` | `vilkaarId=V404` does not identify a condition in the treatment. Why: service cannot load/update the condition result. Constraint: condition id must come from treatment state. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `delete condition period` | `VilkårsvurderingValidering branch for body person identity does not own the requested period` | body person identity does not own the requested period. Why: deletion resolves person/condition ownership. Constraint: period deletion must target the correct treatment person. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `update other assessment` | `VilkårsvurderingValidering branch for annenVurderingId=A404 is missing` | `annenVurderingId=A404` is missing. Why: service cannot find the other-assessment record. Constraint: assessment id must exist under treatment. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `update condition` | `VilkårsvurderingValidering explanation-compatibility outcomes` | Result, type, and explanation are incompatible. Why: The named validator rejects the combination. Constraint: Explanation must match condition and result. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `add condition` | `duplicate-unassessed-condition guard` | An equivalent unassessed condition already exists. Why: Duplicate pending assessment is rejected. Constraint: Only one matching unassessed condition may exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `6/6`, application reached `4/6`, context-valid success `0/6`
- Happy-path summary: `0/1`
- Failure summary: `0/5`
- Behavior outcome checklist summary: `0/6`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 5 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B16`: `Maintain EEA competence intervals`

- Business goal: Create/update and delete EEA competence intervals for children in a treatment.
- Starting point: `Existing service state`
- Expected business result: Competence intervals are upserted, recalculated by the schema service, and then removed when requested.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `upsert competence interval` | `PUT /api/kompetanse/{behandlingId}` with `behandlingId=B1`, `fom=YM1`, `tom=YM2`, `barnIdenter=[C1]`, and required activity/result fields | Yes | No | No | Test_1::test_92_putOnKompetansReturns401 (401); Test_2::test_47_putOnKompetansReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission. |
| 2 | `delete competence interval` | `DELETE /api/kompetanse/{behandlingId}/{kompetanseId}` with `behandlingId=B1`, `kompetanseId=K1` | Yes | Yes | No | Test_0::test_78_deleteOnKompetansCauses500_internalServerError (500); Test_0::test_143_deleteOnKompetansReturns401 (401) | Missing-treatment lookup is covered; the documented ownership branch is not. |

- Happy-path item: `Not Covered` — no valid treatment, interval fixture, update/delete binding, or terminal state assertion exists.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` | No | No context-valid treatment verification. |
| 2 | `retrieve EØS timelines` | `GET /api/tidslinjer/{behandlingId}` with `behandlingId=B1` | No | Request reaches missing-treatment failure, not valid interval verification. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `upsert competence interval` | `PeriodeOgBarnSkjemaService branch for missing fom, empty barnIdenter, or fom > tom` | Missing `fom`, empty `barnIdenter`, or `fom > tom`. Why: schema validation rejects an invalid interval. Constraint: start, child selection, and date ordering must be valid. | Not Covered | High | Update requests stop at auth/admission. | No matching state or side-effect assertion. | No discriminator-specific evidence. |
| `delete competence interval` | `PeriodeOgBarnSkjemaService branch for kompetanseId=K1 belongs to another treatment` | `kompetanseId=K1` belongs to another treatment. Why: deletion validates treatment ownership. Constraint: interval id must belong to `behandlingId=B1`. | Not Covered | High | The 500 fails earlier because the treatment itself is absent. | No matching state or side-effect assertion. | No ownership-branch evidence. |
| `upsert competence interval` | `PeriodeOgBarnSkjemaService validation outcomes` | `fom` missing, reversed dates, no selected child, or invalid activities. Why: the schema cannot form a valid interval. Constraint: all interval fields and activities must be valid. | Not Covered | High | No post-binding update test establishes one exact validation condition. | No matching state or side-effect assertion. | No discriminator-specific evidence. |

- Required-step summary: attempted `2/2`, application reached `1/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 3 concrete failure items.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B17`: `Maintain foreign period amounts`

- Business goal: Update and delete an existing foreign benefit amount period.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The existing foreign amount period has updated amount data, then is deleted from the treatment.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `update foreign period amount` | `PUT /api/differanseberegning/utenlandskperidebeløp/{behandlingId}` with `behandlingId=B1` and body `id=U1`, `beløp=100`, period fields to update the existing amount | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_142_putOnUtenlandskperidebel_pReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_76_putOnUtenlandskperidebel_pReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `delete foreign period amount` | `DELETE /api/differanseberegning/utenlandskperidebeløp/{behandlingId}/{utenlandskPeriodebeløpId}` with `behandlingId=B1`, `utenlandskPeriodebeløpId=U1` to delete the amount | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_162_deleteOnUtenlandskperidebel_pCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_185_deleteOnUtenlandskperidebel_pReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect differential-calculation state | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `update foreign period amount` | `UtenlandskPeriodebeløpService branch for body id=U404 does not exist` | body `id=U404` does not exist. Why: implementation loads the existing row to preserve country data. Constraint: update requires pre-existing foreign amount row. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `1/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B18`: `Update existing currency rate from ECB`

- Business goal: Update an existing currency-rate period by fetching a rate from ECB when currency/date changes.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The existing currency-rate period contains updated ECB-sourced rate data.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `update currency rate from ECB` | `PUT /api/differanseberegning/valutakurs/{behandlingId}` with `behandlingId=B1` and body `id=VK1`, `valutakode=EUR`, `valutakursdato=D1`, `barnIdenter=[C1]` to update the existing currency rate | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_141_putOnValutakurReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_75_putOnValutakurReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect currency-rate state | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `update currency rate from ECB` | `update method branch for id=VK404 does not exist` | `id=VK404` does not exist. Why: controller/service loads existing rate for comparison. Constraint: update requires pre-existing currency-rate row. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `update currency rate from ECB` | `update method branch for ECB cannot provide a rate for supplied code/date` | ECB cannot provide a rate for supplied code/date. Why: ECB service throws. Constraint: external rate data must be available. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `update currency rate from ECB` | `ECBService no-business-rate result` | No applicable ECB rate exists for currency and date. Why: No domain rate can be calculated. Constraint: An applicable business rate must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 3 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B19`: `Set historical ISK currency rate manually and delete currency rate`

- Business goal: Store a manually supplied historical ISK rate, then remove the currency-rate row.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Historical ISK rate is manually stored, then the rate row is removed.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `set historical ISK rate manually` | `PUT /api/differanseberegning/valutakurs/{behandlingId}` with `behandlingId=B1` and body `id=VK1`, `valutakode=ISK`, `valutakursdato=2018-01-31`, `kurs=K_manual`, `barnIdenter=[C1]` to store manual rate | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_141_putOnValutakurReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_75_putOnValutakurReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `delete currency rate` | `DELETE /api/differanseberegning/valutakurs/{behandlingId}/{valutakursId}` with `behandlingId=B1`, `valutakursId=VK1` to delete the rate | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_161_deleteOnValutakurCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_184_deleteOnValutakurReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect currency-rate state | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `delete currency rate` | `ValutakursService branch for valutakursId=VK1 does not belong to behandlingId=B1` | `valutakursId=VK1` does not belong to `behandlingId=B1`. Why: shared schema deletion rejects id/treatment mismatch. Constraint: currency-rate id must belong to the treatment. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `1/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B20`: `Maintain changed payment shares and reset treatment result`

- Business goal: Create, fill, remove, and explicitly reset changed-payment share state in an editable treatment.
- Starting point: `Existing service state`
- Expected business result: Changed-payment share state is created, updated, removed, and the treatment is reset to treatment-result state after recalculation-sensitive changes.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create changed payment share` | `POST /api/endretutbetalingandel/{behandlingId}` with `behandlingId=B1` to create an empty share and capture `endretUtbetalingAndelId=EUA1` | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_82_postOnEndretutbetalingandelReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_85_postOnEndretutbetalingandelReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `update changed payment share` | `PUT /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}` with `behandlingId=B1`, `endretUtbetalingAndelId=EUA1`, and body `personIdent=P1`, period and payment-share fields to update the share | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_140_putOnEndretutbetalingandelReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_74_putOnEndretutbetalingandelReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 3 | `delete changed payment share` | `DELETE /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}` with `behandlingId=B1`, `endretUtbetalingAndelId=EUA1` to remove it | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_75_deleteOnEndretutbetalingandelCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_140_deleteOnEndretutbetalingandelReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 4 | `reset treatment to treatment result` | `POST /api/endretutbetalingandel/{behandlingId}/tilbakestill` with `behandlingId=B1` to explicitly reset treatment to result step | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_119_postOnTilbakestillCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_125_postOnTilbakestillReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect share list and current step | No | No context-valid verification with required binding/state. |
| 2 | `find invalid after-payment periods` | `GET /api/behandlinger/{behandlingId}/personer-med-ugyldig-etterbetalingsperiode` with `behandlingId=B1` to inspect after-payment validity | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `update changed payment share` | `EndretUtbetalingAndelValidering branch for personIdent=P2 is not in the treatment` | `personIdent=P2` is not in the treatment. Why: service resolves body person identity against treatment persons. Constraint: changed-payment share must target a treatment person. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `delete changed payment share` | `EndretUtbetalingAndelValidering branch for endretUtbetalingAndelId=EUA404 does not exist` | `endretUtbetalingAndelId=EUA404` does not exist. Why: service cannot find share for deletion. Constraint: share id must come from creation or treatment state. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `create changed payment share` | `EndretUtbetalingAndelValidering named outcomes` | Reason, period, percentage, person, entitlement overlap, or share relation is invalid. Why: A source-distinct validator rejects the adjustment. Constraint: All adjustment inputs and relationships must be valid. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `4/4`, application reached `2/4`, context-valid success `0/4`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 3 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B21`: `Inspect EEA timelines`

- Business goal: Read calculated EEA timelines for a treatment.
- Starting point: `Existing service state`
- Expected business result: Caller receives EEA timeline data for the treatment.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve EØS timelines` | `GET /api/tidslinjer/{behandlingId}` with `behandlingId=B1` to retrieve timelines | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_15_getOnTidslinjCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_34_getOnTidslinjReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `retrieve EØS timelines` | `timeline methods branch for behandlingId=B404 does not exist` | `behandlingId=B404` does not exist. Why: treatment lookup/access fails. Constraint: treatment must exist. | Covered | Medium | Test_0::test_15_getOnTidslinjCauses500_internalServerError: id 304; exact missing-treatment message. | Reset-empty state establishes the condition; source throws before mutation. No direct post-failure DB query. | report.xml: BehandlingHentOgPersisterService.kt lines 40-41 covered. |
| `retrieve EØS timelines` | `timeline prerequisite outcomes` | Treatment or condition-assessment basis is absent. Why: Timelines cannot be derived. Constraint: Treatment and assessment basis must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `1/2`
- Behavior outcome checklist summary: `1/3`
- Status and confidence: `Partially Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B22`: `Maintain EEA refund periods`

- Business goal: Add, list, update, and delete refund periods for EEA handling.
- Starting point: `Existing service state`
- Expected business result: A refund period is created, made visible, updated, and removed.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `add EØS refund period` | `POST /api/refusjon-eøs/behandlinger/{behandlingId}` with `behandlingId=B1` and body `RestRefusjonEøs` to create refund period and capture `id=R1` | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_126_postOnBehandlingReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_149_postOnBehandlingReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `list EØS refund periods` | `GET /api/refusjon-eøs/behandlinger/{behandlingId}` with `behandlingId=B1` to retrieve and confirm `id=R1` | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_82_getOnBehandlingReturnsObject (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_98_getOnRefusjon_e_sBehandlingReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 3 | `update EØS refund period` | `PUT /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}` with `behandlingId=B1`, `id=R1`, and body `RestRefusjonEøs` to update the period | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_195_putOnBehandlingPeriodReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_83_putOnBehandlingPeriodReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 4 | `delete EØS refund period` | `DELETE /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}` with `behandlingId=B1`, `id=R1` to delete the period | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_197_deleteOnBehandlingPeriodReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_199_deleteOnPeriodReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect returned treatment state | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `update EØS refund period` | `RefusjonEøsService branch for id=R404 does not exist` | `id=R404` does not exist. Why: service lookup throws when refund period id is missing. Constraint: period id must come from creation/list. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `delete EØS refund period` | `RefusjonEøsService branch for id=R404 does not exist` | `id=R404` does not exist. Why: service logs the period before deleting and lookup throws. Constraint: period id must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `4/4`, application reached `1/4`, context-valid success `0/4`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 2 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B23`: `Maintain overpaid currency periods`

- Business goal: Add, list, update, and delete periods with overpaid currency amount.
- Starting point: `Existing service state`
- Expected business result: Overpaid-currency period exists, is updated, and is eventually removed.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `add overpaid currency period` | `POST /api/feilutbetalt-valuta/behandling/{behandlingId}` with `behandlingId=B1` and body `RestFeilutbetaltValuta` to create period and capture `id=FV1` | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_72_postOnFeilutbetalt_valutaBehandlCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_135_postOnFeilutbetalt_valutaBehandlReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `list overpaid currency periods` | `GET /api/feilutbetalt-valuta/behandling/{behandlingId}` with `behandlingId=B1` to retrieve and confirm `id=FV1` | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_52_getOnFeilutbetalt_valutaBehandlCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_100_getOnFeilutbetalt_valutaBehandlReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 3 | `update overpaid currency period` | `PUT /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}` with `behandlingId=B1`, `id=FV1`, and body `RestFeilutbetaltValuta` to update period data | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_196_putOnBehandlPeriodReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_84_putOnBehandlPeriodReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 4 | `delete overpaid currency period` | `DELETE /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}` with `behandlingId=B1`, `id=FV1` to delete it | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_194_deleteOnPeriodCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_198_deleteOnBehandlPeriodReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect treatment state | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `update overpaid currency period` | `FeilutbetaltValutaService branch for id=FV404 does not exist` | `id=FV404` does not exist. Why: service lookup throws when id is not found. Constraint: period id must come from creation/list. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `delete overpaid currency period` | `FeilutbetaltValutaService branch for id=FV404 does not exist` | `id=FV404` does not exist. Why: service logs the period before deleting and lookup throws. Constraint: period id must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `4/4`, application reached `2/4`, context-valid success `0/4`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 2 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B24`: `Activate and deactivate corrected decision metadata`

- Business goal: Mark a treatment as having corrected decision metadata, then deactivate that metadata.
- Starting point: `Existing service state`
- Expected business result: Corrected-decision metadata is activated and then inactivated; previous active metadata is deactivated when a new record is created.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create corrected decision metadata` | `POST /api/korrigertvedtak/behandling/{behandlingId}` with `behandlingId=B1` and body `KorrigerVedtakRequest` to create active metadata | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_69_postOnKorrigertvedtakBehandlCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_131_postOnKorrigertvedtakBehandlReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `deactivate corrected decision metadata` | `PATCH /api/korrigertvedtak/behandling/{behandlingId}` with `behandlingId=B1` to deactivate active metadata | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_80_patchOnKorrigertvedtakBehandlCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_145_patchOnKorrigertvedtakBehandlWithQueryParamReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect returned treatment state | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `create corrected decision metadata` | `KorrigertVedtakService branch for treatment is not editable` | treatment is not editable. Why: controller calls `validerBehandlingKanRedigeres`. Constraint: correction metadata can be changed only on editable treatment. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `2/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B25`: `Activate, list, and deactivate corrected after-payment metadata`

- Business goal: Mark corrected after-payment metadata active, inspect all records, and deactivate the active correction.
- Starting point: `Existing service state`
- Expected business result: Corrected after-payment metadata is created, visible in list output, and then inactivated.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create corrected after-payment metadata` | `POST /api/korrigertetterbetaling/behandling/{behandlingId}` with `behandlingId=B1` and body `KorrigertEtterbetalingRequest` to create active metadata | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_70_postOnKorrigertetterbetalingBehandlCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_132_postOnKorrigertetterbetalingBehandlReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `list corrected after-payment metadata` | `GET /api/korrigertetterbetaling/behandling/{behandlingId}` with `behandlingId=B1` to list correction records | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_51_getOnKorrigertetterbetalingBehandlCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_99_getOnKorrigertetterbetalingBehandlReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 3 | `deactivate corrected after-payment metadata` | `PATCH /api/korrigertetterbetaling/behandling/{behandlingId}` with `behandlingId=B1` to deactivate active metadata | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_81_patchOnKorrigertetterbetalingBehandlCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_146_patchOnKorrigertetterbetalingBehandlReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect treatment state | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `create corrected after-payment metadata` | `KorrigertEtterbetalingService branch for treatment is not editable` | treatment is not editable. Why: controller validates editability. Constraint: correction metadata requires editable treatment. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `3/3`, application reached `2/3`, context-valid success `0/3`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B26`: `Add and remove small child supplement correction`

- Business goal: Add a small-child supplement correction for a month and remove it later.
- Starting point: `Existing service state`
- Expected business result: The monthly supplement correction is added and then removed from the treatment.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `add small child supplement correction` | `POST /api/småbarnstilleggkorrigering/behandling/{behandlingId}` with `behandlingId=B1` and body `årMåned=YM1` to add correction | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_66_postOnSm_barnstilleggkorrigeringBehandlCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_123_postOnSm_barnstilleggkorrigeringBehandlReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `remove small child supplement correction` | `DELETE /api/småbarnstilleggkorrigering/behandling/{behandlingId}` with `behandlingId=B1` and body `årMåned=YM1` to remove correction | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_76_deleteOnBehandlCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_141_deleteOnBehandlReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect treatment state | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `remove small child supplement correction` | `SmåbarnstilleggKorrigeringService branch for no correction exists for årMåned=YM1` | no correction exists for `årMåned=YM1`. Why: service cannot remove a non-existing correction for the treatment/month. Constraint: removal requires existing month correction. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `add small child supplement correction` | `duplicate-month guard` | A correction already exists for the month. Why: Duplicate correction is rejected. Constraint: Only one correction per month may exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 2 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B27`: `Preview repayment warning letter`

- Business goal: Generate a repayment warning letter preview without sending it.
- Starting point: `Existing service state`
- Expected business result: Caller receives preview PDF/resource; no send state is persisted by this function.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `preview repayment warning letter` | `POST /api/tilbakekreving/{behandlingId}/forhandsvis-varselbrev` with `behandlingId=B1` and body `fritekst=T1` to generate preview | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_65_postOnForhandsvis_varselbrevCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_121_postOnForhandsvis_varselbrevReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `preview repayment warning letter` | `warning preview branch for behandlingId=B404 does not exist` | `behandlingId=B404` does not exist. Why: treatment lookup/document-generation basis fails. Constraint: treatment must exist. | Covered | Medium | Test_0::test_65_postOnForhandsvis_varselbrevCauses500_internalServerError: id 27; exact missing-treatment message. | Reset-empty state establishes the condition; source throws before mutation. No direct post-failure DB query. | report.xml: BehandlingHentOgPersisterService.kt lines 40-41 covered. |
| `preview repayment warning letter` | `decision/person-basis/unit prerequisites` | Active decision, person basis, or handling unit is missing. Why: The warning-letter basis is incomplete. Constraint: All prerequisites must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `1/2`
- Behavior outcome checklist summary: `1/3`
- Status and confidence: `Partially Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B28`: `Generate and retrieve decision letter`

- Business goal: Generate the persisted decision letter for an active decision and retrieve it.
- Starting point: `Existing service state`
- Expected business result: A decision letter PDF exists for the decision and is retrievable.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `generate decision letter` | `POST /api/dokument/vedtaksbrev/{vedtakId}` with `vedtakId=Vd1` to generate and store the decision letter | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_127_postOnVedtaksbrevReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_170_postOnVedtaksbrevReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `retrieve decision letter` | `GET /api/dokument/vedtaksbrev/{vedtakId}` with `vedtakId=Vd1` to retrieve the stored PDF | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_53_getOnVedtaksbrevCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_101_getOnVedtaksbrevReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to find the active `vedtak.id` when it is not already known | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `retrieve decision letter` | `BrevService branch for letter has not been generated for vedtakId=Vd1` | letter has not been generated for `vedtakId=Vd1`. Why: retrieval expects stored/generated document state. Constraint: generation must precede retrieval. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `generate decision letter` | `BrevService branch for vedtakId=V404 does not exist` | `vedtakId=V404` does not exist. Why: document service cannot load decision basis. Constraint: decision id must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `generate decision letter` | `template/result/explanation guards` | Lifecycle, template, explanations, or cessation period structure is unsupported. Why: The letter cannot be composed. Constraint: Document prerequisites must be supported. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `1/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 3 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B29`: `Preview and send manual treatment letter`

- Business goal: Preview and send a manual letter tied to a treatment.
- Starting point: `Existing service state`
- Expected business result: A treatment-scoped manual letter is sent; preview was generated with the same request values.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `preview treatment letter` | `POST /api/dokument/forhaandsvis-brev/{behandlingId}` with `behandlingId=B1` and body `ManueltBrevRequest` to preview the letter | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_121_postOnForhaandsvis_brevCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_129_postOnForhaandsvis_brevReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `send treatment letter` | `POST /api/dokument/send-brev/{behandlingId}` with `behandlingId=B1` and body `ManueltBrevRequest` to send the letter | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_120_postOnSend_brevCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_128_postOnSend_brevReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment log` | `GET /api/logg/{behandlingId}` with `behandlingId=B1` to inspect logged communication events | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `send treatment letter` | `manual treatment letter methods branch for ManueltBrevRequest lacks required template/recipient content` | `ManueltBrevRequest` lacks required template/recipient content. Why: document service cannot generate/send a valid manual letter. Constraint: request body must define a valid manual letter. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `preview treatment letter` | `manual-template validation outcomes` | SED, cohabitation date, response weeks, or template choice is invalid. Why: The template strategy rejects the content. Constraint: Template-specific rules must hold. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `send treatment letter` | `participant/confidentiality guards` | Recipient is not a participant or confidentiality disallows it. Why: Distribution is rejected. Constraint: Recipient and confidentiality rules must hold. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 3 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B30`: `Preview and send manual case letter`

- Business goal: Preview and send a manual letter tied directly to a case.
- Starting point: `Existing service state`
- Expected business result: A case-scoped manual letter is sent.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `preview case letter` | `POST /api/dokument/fagsak/{fagsakId}/forhaandsvis-brev` with `fagsakId=F1` and body `ManueltBrevRequest` to preview the letter | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_158_postOnForhaandsvis_brevCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_176_postOnForhaandsvis_brevReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `send case letter` | `POST /api/dokument/fagsak/{fagsakId}/send-brev` with `fagsakId=F1` and body `ManueltBrevRequest` to send the letter | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_157_postOnSend_brevCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_175_postOnSend_brevReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve full case` | `GET /api/fagsaker/{fagsakId}` with `fagsakId=F1` to inspect case context | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `send case letter` | `manual case letter methods branch for fagsakId=F404 does not exist` | `fagsakId=F404` does not exist. Why: case/document basis cannot be loaded. Constraint: parent case must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `send case letter` | `case/institution lookup outcomes` | Case or selected institution relation is absent. Why: Recipient context is invalid. Constraint: Case and relation must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 2 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B31`: `Maintain manual letter recipients`

- Business goal: Add, list, and remove manual letter recipients for a treatment.
- Starting point: `Existing service state`
- Expected business result: Manual recipient is added, visible in list, and removed.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `add letter recipient` | `POST /api/brevmottaker/{behandlingId}` with `behandlingId=B1` and body `RestBrevmottaker` to add recipient and capture `mottakerId=M1` | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_83_postOnBrevmottakReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_42_postOnBrevmottakReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `list letter recipients` | `GET /api/brevmottaker/{behandlingId}` with `behandlingId=B1` to list recipients and confirm `mottakerId=M1` | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_8_getOnBrevmottakCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_32_getOnBrevmottakReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 3 | `delete letter recipient` | `DELETE /api/brevmottaker/{behandlingId}/{mottakerId}` with `behandlingId=B1`, `mottakerId=M1` to delete the recipient | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_79_deleteOnBrevmottakCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_144_deleteOnBrevmottakReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment log` | `GET /api/logg/{behandlingId}` with `behandlingId=B1` to inspect recipient add/remove logs | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `add letter recipient` | `BrevmottakerService branch for treatment contains strictly confidential person and manual recipient is disallowed` | treatment contains strictly confidential person and manual recipient is disallowed. Why: validation service rejects the combination. Constraint: confidentiality rules restrict manual recipients. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `delete letter recipient` | `BrevmottakerService branch for mottakerId=M404 does not exist` | `mottakerId=M404` does not exist. Why: service throws when recipient id is missing. Constraint: deletion requires existing recipient id. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `add letter recipient` | `editability/confidentiality guards` | Treatment is non-editable or confidentiality disallows recipient. Why: Recipient cannot be attached. Constraint: Editable state and permitted recipient are required. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `3/3`, application reached `2/3`, context-valid success `0/3`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 3 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B32`: `Edit decision periods and regenerate letter explanations`

- Business goal: Modify decision-period explanations, override change date, and generate final letter explanation texts.
- Starting point: `Existing service state`
- Expected business result: Decision-period explanation metadata and free texts are updated; change date override can regenerate the period set; final letter explanation text can be generated.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `list decision periods` | `GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder` with `behandlingId=B1` to obtain `vedtaksperiodeId=VP1` | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_163_getOnHent_vedtaksperioderReturnsObject (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_166_getOnHent_vedtaksperioderReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `update standard explanations` | `PUT /api/vedtaksperioder/standardbegrunnelser/{vedtaksperiodeId}` with `vedtaksperiodeId=VP1` and body `standardbegrunnelser=[S1]` to update standard explanations | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_135_putOnStandardbegrunnelsReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_70_putOnStandardbegrunnelsReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 3 | `update decision free texts` | `PUT /api/vedtaksperioder/fritekster/{vedtaksperiodeId}` with `vedtaksperiodeId=VP1` and body `RestPutVedtaksperiodeMedFritekster` to update free texts | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_136_putOnFritekstReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_71_putOnFritekstReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 4 | `regenerate decision periods` | `PUT /api/vedtaksperioder/endringstidspunkt` with body `behandlingId=B1`, overridden date fields to regenerate periods | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_86_putOnEndringstidspunktReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_45_putOnEndringstidspunktReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 5 | `list decision periods` | `GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder` with `behandlingId=B1` to obtain current `vedtaksperiodeId=VP2` | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_163_getOnHent_vedtaksperioderReturnsObject (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_166_getOnHent_vedtaksperioderReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 6 | `generate letter explanation texts` | `GET /api/vedtaksperioder/brevbegrunnelser/{vedtaksperiodeId}` with `vedtaksperiodeId=VP2` to generate final explanation text | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_56_getOnBrevbegrunnelsCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_102_getOnBrevbegrunnelsReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get change date` | `GET /api/behandlinger/{behandlingId}/endringstidspunkt` with `behandlingId=B1` to inspect the change date | No | No context-valid verification with required binding/state. |
| 2 | `generate decision letter` | `POST /api/dokument/vedtaksbrev/{vedtakId}` with `vedtakId=Vd1` to generate the full letter after explanations are set | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `update standard explanations` | `explanation methods branch for body contains an explanation string not convertible to a known enum` | body contains an explanation string not convertible to a known enum. Why: controller maps names through `IVedtakBegrunnelse.konverterTilEnumVerdi`. Constraint: explanation names must be recognized. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `generate letter explanation texts` | `explanation methods branch for decision period contains unsupported explanation data` | decision period contains unsupported explanation data. Why: controller throws `Feil("Ukjent begrunnelsestype")`. Constraint: explanation data must be a supported type. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `update standard explanations` | `period/explanation/rejection guards` | Period is missing, explanation unknown, or removal contradicts rejection result. Why: The explanation set would be invalid. Constraint: Period and compatible explanations must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `generate letter explanation texts` | `generated-type outcome` | A generated explanation type is unsupported. Why: Final text cannot be assembled. Constraint: Every generated type must be supported. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `6/6`, application reached `3/6`, context-valid success `0/6`
- Happy-path summary: `0/1`
- Failure summary: `0/4`
- Behavior outcome checklist summary: `0/5`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 4 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B33`: `Retrieve treatment log`

- Business goal: Read audit/log entries for a treatment.
- Starting point: `Existing service state`
- Expected business result: Caller receives log entries for the treatment.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve treatment log` | `GET /api/logg/{behandlingId}` with `behandlingId=B1` to retrieve logs | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_21_getOnLoggCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_44_getOnApiLoggReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B34`: `Retrieve external benefit data for BISYS`

- Business goal: Provide BISYS with extended child benefit and small child supplement periods for a person.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: BISYS receives benefit period data or a controlled external-service error.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve BISYS extended benefit` | `POST /api/bisys/hent-utvidet-barnetrygd` with body `personIdent=P1`, `fraDato=D_within_5_years` to retrieve benefit periods | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_84_postOnHent_utvidet_barnetrygdReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_43_postOnHent_utvidet_barnetrygdReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `retrieve BISYS extended benefit` | `lookup method branch for fraDato < today - 5 years` | `fraDato < today - 5 years`. Why: controller throws BAD_REQUEST external-service error. Constraint: BISYS lookup window is limited. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve BISYS extended benefit` | `lookup method branch for unknown personIdent` | unknown `personIdent`. Why: PDL not-found `Feil` is converted to BAD_REQUEST external-service error. Constraint: person must exist upstream. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 2 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B35`: `Retrieve pension child benefit`

- Business goal: Provide Pension with child-benefit case and period data for one person.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Pension receives mapped child-benefit data for the requested person and date range.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve pension child benefit` | `POST /api/ekstern/pensjon/hent-barnetrygd` with body `ident=P1`, `fraDato=D_within_two_years` to retrieve child-benefit data for Pension | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_126_postOnHent_barnetrygdReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `retrieve pension child benefit` | `lookup method branch for fraDato is older than the allowed two-year window` | `fraDato` is older than the allowed two-year window. Why: the controller throws a BAD_REQUEST external service error. Constraint: Pension lookup date window. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve pension child benefit` | `actor-not-found outcome` | Identity cannot resolve to actor. Why: No pension case can be scoped. Constraint: Identity must resolve to actor. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve pension child benefit` | `implemented-treatment prerequisite` | Implemented treatment lacks awarded-benefit or payment state. Why: Finalized response cannot be mapped. Constraint: Implemented state must be complete. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 3 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B36`: `Order pension yearly export`

- Business goal: Queue export of persons with child benefit for a Pension tax/reporting year.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: A yearly export job for Pension is queued.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `order pension yearly export` | `GET /api/ekstern/pensjon/bestill-personer-med-barnetrygd/{år}` with `år=2026` to queue the yearly export | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_171_getOnBestill_personer_med_barnetrygdReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_181_getOnBestill_personer_med_barnetrygdReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `order pension yearly export` | `export method branch for år=1969 or år=2301` | `år=1969` or `år=2301`. Why: the controller throws `IllegalArgumentException` for year outside range. Constraint: allowed export year. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B37`: `Production tax data export`

- Business goal: Return Skatteetaten production person and period data for one tax year.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The production tax consumer receives period data for listed tax persons.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `list tax persons` | `GET /api/skatt/personer` with query `aar=2026` to list production tax persons and capture `ident=P1` from the response | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_17_getOnPersonerCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_40_getOnPersonerReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `retrieve tax periods` | `POST /api/skatt/perioder` with body `identer=[P1]`, `aar=2026` to retrieve production tax periods for the listed person | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_21_postOnPerioderCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_56_postOnPerioderReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `2/2`, application reached `2/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B38`: `Tax test endpoint data retrieval`

- Business goal: Return Skatteetaten test-path person and period data for one tax year.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Test callers receive mapped tax periods through the explicit test endpoints.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `list tax persons test` | `GET /api/skatt/personer/test` with query `aar=2026` to list test-path tax persons and capture `ident=P1` from the response | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_57_getOnTestCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_104_getOnTestReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `retrieve tax periods test` | `POST /api/skatt/perioder/test` with body `identer=[P1]`, `aar=2026` to retrieve test-path tax periods | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_67_postOnTestCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_124_postOnTestReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `2/2`, application reached `2/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B39`: `Retrieve Infotrygd case and benefit context`

- Business goal: Read legacy Infotrygd case, benefit, and ongoing-state information for an applicant.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Caller receives legacy context for the applicant.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve Infotrygd cases` | `POST /api/infotrygd/hent-infotrygdsaker-for-soker` with body `ident=P1` to retrieve legacy cases | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_29_postOnHent_infotrygdsaker_for_sokerCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_69_postOnHent_infotrygdsaker_for_sokerReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `retrieve Infotrygd benefits` | `POST /api/infotrygd/hent-infotrygdstonader-for-soker` with body `ident=P1` to retrieve legacy benefits | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_28_postOnHent_infotrygdstonader_for_sokerCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_68_postOnHent_infotrygdstonader_for_sokerReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 3 | `check ongoing Infotrygd case` | `POST /api/infotrygd/har-lopende-sak` with body `ident=P1` to check ongoing state | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_30_postOnHar_lopende_sakCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_70_postOnHar_lopende_sakReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `retrieve Infotrygd cases` | `actor-resolution not-found outcome` | The applicant identity cannot be resolved to a domain actor. Why: The legacy query cannot be scoped to a persisted applicant. Constraint: The identity must resolve to a domain actor. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve Infotrygd benefits` | `actor-resolution not-found outcome` | The applicant identity cannot be resolved to a domain actor. Why: The legacy query cannot be scoped to a persisted applicant. Constraint: The identity must resolve to a domain actor. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `3/3`, application reached `3/3`, context-valid success `0/3`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 2 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B40`: `Discover collaborators by search and organization number`

- Business goal: Find collaborator/institution information and retrieve details by organization number.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Collaborator details are available for institution/case workflows.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `search collaborator` | `POST /api/samhandler/navn` with body `navn=N1` to search collaborators and capture `orgnr=ORG1` from a result | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_50_postOnNavnReturns400 (400); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_60_postOnNavnReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `retrieve collaborator by organization` | `GET /api/samhandler/orgnr/{orgnr}` with `orgnr=ORG1` to retrieve details | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_64_getOnOrgnrCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_120_getOnOrgnrReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `search collaborator` | `SamhandlerController branch for body has no navn, postnummer, or område` | body has no `navn`, `postnummer`, or `område`. Why: controller throws BAD_REQUEST. Constraint: at least one search variable is required. | Covered | High | Test_1::test_50_postOnNavnReturns400: body `{}`; exact FUNKSJONELL_FEIL message. | Reset-empty state establishes the condition; source throws before mutation. No direct post-failure DB query. | report.xml: SamhandlerController.kt lines 49-52 and the target branch covered. |
| `retrieve collaborator by organization` | `SamhandlerController branch for orgnr=UNKNOWN` | `orgnr=UNKNOWN`. Why: not-found exceptions are converted to functional 404. Constraint: collaborator must exist upstream. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `2/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `1/2`
- Behavior outcome checklist summary: `1/3`
- Status and confidence: `Partially Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B41`: `Create and list complaint treatments for a case`

- Business goal: Start a complaint treatment for a child-benefit case and list complaint treatments.
- Starting point: `Existing service state`
- Expected business result: A complaint treatment exists for the case and is visible through the list endpoint.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create complaint treatment` | `POST /api/fagsaker/{fagsakId}/opprett-klagebehandling` with `fagsakId=F1` and body `OpprettKlageDto` to create complaint treatment | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_136_postOnOpprett_klagebehandlingReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_153_postOnOpprett_klagebehandlingReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `list complaint treatments` | `GET /api/fagsaker/{fagsakId}/hent-klagebehandlinger` with `fagsakId=F1` to list complaint treatments | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_91_getOnHent_klagebehandlingerReturnsObject (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_113_getOnHent_klagebehandlingerReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve full case` | `GET /api/fagsaker/{fagsakId}` with `fagsakId=F1` to inspect case context | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `create complaint treatment` | `KlageService branch for fagsakId=F404 does not exist` | `fagsakId=F404` does not exist. Why: complaint creation cannot bind to parent case. Constraint: complaint treatment requires existing case. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `create complaint treatment` | `KlageService future-date guard` | Complaint date is in the future. Why: Chronology is invalid. Constraint: Received date may not be future. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `list complaint treatments` | `response-key consistency outcome` | Response omits requested case key. Why: Case-scoped list cannot be returned. Constraint: Response must contain requested case. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `1/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 3 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B42`: `Let complaint system create a revision after precheck`

- Business goal: Let the external complaint system check and create a complaint-triggered revision on a case.
- Starting point: `Existing service state`
- Expected business result: A complaint-triggered revision treatment is created for the case.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `check complaint revision creation` | `GET /api/klage/fagsaker/{fagsakId}/kan-opprette-revurdering-klage` with `fagsakId=F1` and Klage machine-to-machine caller context to check eligibility | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_168_getOnKan_opprette_revurdering_klageReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_187_getOnKan_opprette_revurdering_klageReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |
| 2 | `create complaint revision` | `POST /api/klage/fagsaker/{fagsakId}/opprett-revurdering-klage/` with `fagsakId=F1` and Klage machine-to-machine caller context to create the revision | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_172_postOnOpprett_revurdering_klageReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_188_postOnOpprett_revurdering_klageReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve complaint decisions` | `GET /api/klage/fagsaker/{fagsakId}/vedtak` with `fagsakId=F1` to inspect decisions available to complaint system | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `create complaint revision` | `explicit not-created outcome` | An open treatment exists or no decision can be revised. Why: The precheck returns not-created. Constraint: A revisable decision and no conflict are required. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B43`: `Retrieve decisions for complaint system`

- Business goal: Provide fagsystem decisions for a case to the complaint system.
- Starting point: `Existing service state`
- Expected business result: Complaint system receives decision data for the case.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve complaint decisions` | `GET /api/klage/fagsaker/{fagsakId}/vedtak` with `fagsakId=F1` to retrieve decisions | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_167_getOnVedtakReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_186_getOnVedtakReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `retrieve complaint decisions` | `finalized-decision prerequisites` | Case is missing or completed treatment lacks active timestamped decision. Why: The complaint payload is incomplete. Constraint: Case and timestamped decision must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B44`: `Search external tasks`

- Business goal: Find external task ids that can be acted on by later task workflows.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The caller receives task ids and task metadata from the external task system.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `search tasks` | `POST /api/oppgave/hent-oppgaver` with body `RestFinnOppgaveRequest` to search tasks and capture `oppgaveId=O1` | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_25_postOnHent_oppgaverCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_62_postOnHent_oppgaverReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B45`: `Assign external task`

- Business goal: Assign a known task to a caseworker.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The upstream task is assigned and the response returns the assigned task id/string.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `search tasks` | `POST /api/oppgave/hent-oppgaver` with body `RestFinnOppgaveRequest` to capture `oppgaveId=O1` | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_25_postOnHent_oppgaverCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_62_postOnHent_oppgaverReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `assign task` | `POST /api/oppgave/{oppgaveId}/fordel` with `oppgaveId=O1`, query `saksbehandler=S1` to assign the task | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_129_postOnFordelReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_151_postOnFordelReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve journaling task data` | `GET /api/oppgave/{oppgaveId}` with `oppgaveId=O1` to inspect task context | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `assign task` | `already-assigned/task-not-found outcomes` | Task is missing or assigned to another caseworker. Why: Conflicting ownership is rejected. Constraint: Task must exist and be assignable. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `1/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B46`: `Reset external task assignment`

- Business goal: Clear assignment on a known external task.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The external task is no longer assigned to a caseworker.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `search tasks` | `POST /api/oppgave/hent-oppgaver` with body `RestFinnOppgaveRequest` to capture `oppgaveId=O1` | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_25_postOnHent_oppgaverCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_62_postOnHent_oppgaverReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `reset task assignment` | `POST /api/oppgave/{oppgaveId}/tilbakestill` with `oppgaveId=O1` to clear assignment | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_128_postOnTilbakestillReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_150_postOnTilbakestillReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve journaling task data` | `GET /api/oppgave/{oppgaveId}` with `oppgaveId=O1` to inspect task state | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `reset task assignment` | `task-not-found/nonmutable outcome` | Task is missing or nonmutable. Why: Assignment cannot be cleared. Constraint: Task must be resettable. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `1/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B47`: `Retrieve journaling task data`

- Business goal: Gather task, person, minimal case, and optional journalpost context for manual journaling.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The caller receives task details plus person, minimal case, and journalpost data when available.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve journaling task data` | `GET /api/oppgave/{oppgaveId}` with `oppgaveId=O1` to retrieve task context | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_20_getOnOppgavCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_43_getOnOppgavReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `retrieve journaling task data` | `journaling-task method branch for oppgaveId=O1 cannot be loaded` | `oppgaveId=O1` cannot be loaded. Why: `oppgaveService.hentOppgave` must return an upstream task. Constraint: task id must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve journaling task data` | `task/person/journalpost outcomes` | Task is missing, person unresolved, or journalpost missing. Why: Context cannot be assembled. Constraint: All referenced objects must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 2 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B48`: `Complete external task`

- Business goal: Close a known external task without linking a journalpost.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The upstream task is closed and the response says the task was closed.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `search tasks` | `POST /api/oppgave/hent-oppgaver` with body `RestFinnOppgaveRequest` to capture `oppgaveId=O1` | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_25_postOnHent_oppgaverCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_62_postOnHent_oppgaverReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `complete task` | `GET /api/oppgave/{oppgaveId}/ferdigstill` with `oppgaveId=O1` to complete the task | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_110_getOnFerdigstillReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_147_getOnFerdigstillReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `search tasks` | `POST /api/oppgave/hent-oppgaver` with the same search body to verify the task no longer appears as open | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `complete task` | `completion method branch for oppgaveId=O1 does not identify an open task` | `oppgaveId=O1` does not identify an open task. Why: the service cannot load or complete the task. Constraint: task must exist and be completable. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `complete task` | `task-object id outcome` | Task is missing or has no id. Why: No concrete task can complete. Constraint: Task with id must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `1/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 2 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B49`: `Complete task while linking a journalpost`

- Business goal: Link a journalpost to a case/treatment context and complete the related task.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Journalpost is connected to the case/treatment context and the task is completed.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `complete task and link journalpost` | `POST /api/oppgave/{oppgaveId}/ferdigstillOgKnyttjournalpost` with `oppgaveId=O1` and body `RestFerdigstillOppgaveKnyttJournalpost` containing `journalpostId=J1`, `fagsakId=F1` to link and complete | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_130_postOnFerdigstillOgKnyttjournalpostReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_152_postOnFerdigstillOgKnyttjournalpostReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve journaling task data` | `GET /api/oppgave/{oppgaveId}` with `oppgaveId=O1` before completion to inspect task context | No | No context-valid verification with required binding/state. |
| 2 | `retrieve journalpost` | `GET /api/journalpost/{journalpostId}/hent` with `journalpostId=J1` to inspect journalpost metadata | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `complete task and link journalpost` | `link/completion method branch for oppgaveId=O404 or journalpostId=J404 is invalid` | `oppgaveId=O404` or `journalpostId=J404` is invalid. Why: task/journal integration cannot complete/link missing resources. Constraint: both upstream resources must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `complete task and link journalpost` | `task/journalpost/treatment outcomes` | Task, journalpost, or linked treatment is missing. Why: Link context is incomplete. Constraint: All references must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 2 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B50`: `Retrieve open extended-benefit deadlines`

- Business goal: Report deadlines for open extended child-benefit treatments.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The caller receives current deadline information for open extended-benefit treatments.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve open treatment deadlines` | `POST /api/oppgave/hent-frister-for-apne-utvidet-barnetrygd-behandlinger` with no body to retrieve deadline information | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_26_postOnHent_frister_for_apne_utvidet_barnetrygd_behandlingerCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_63_postOnHent_frister_for_apne_utvidet_barnetrygd_behandlingerReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B51`: `Clear application task ownership`

- Business goal: Remove `behandlesAvApplikasjon` ownership markers from selected tasks.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The service reports which tasks had `behandlesAvApplikasjon` removed.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `clear application task ownership` | `POST /api/oppgave/fjern-behandles-av-applikasjon` with body `[O1,O2]` to clear ownership markers | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_41_postOnFjern_behandles_av_applikasjonReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_64_postOnFjern_behandles_av_applikasjonReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve journaling task data` | `GET /api/oppgave/{oppgaveId}` with `oppgaveId=O1` to inspect a selected task | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B52`: `Inspect journalpost and retrieve documents`

- Business goal: Read journalpost metadata and fetch documents in resource/PDF form.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The caller gets journalpost metadata and document bytes in both wrapped resource and PDF media forms.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve journalpost` | `GET /api/journalpost/{journalpostId}/hent` with `journalpostId=J1` to retrieve metadata and capture `dokumentInfoId=DOK1` | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_60_getOnHentCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_111_getOnHentReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `retrieve journal document resource` | `GET /api/journalpost/{journalpostId}/hent/{dokumentInfoId}` with `journalpostId=J1`, `dokumentInfoId=DOK1` to retrieve the document resource | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_154_getOnHentCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_169_getOnHentReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 3 | `retrieve journal document PDF` | `GET /api/journalpost/{journalpostId}/dokument/{dokumentInfoId}` with `journalpostId=J1`, `dokumentInfoId=DOK1` to retrieve the document as PDF | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_155_getOnDokumentCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_170_getOnDokumentReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `list user journalposts` | `POST /api/journalpost/for-bruker` with body `PersonIdent=P1` to list journalposts for a person and find `journalpostId=J1` | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `retrieve journal document resource` | `JournalføringController branch for dokumentInfoId=DOK404 does not belong to journalpostId=J1` | `dokumentInfoId=DOK404` does not belong to `journalpostId=J1`. Why: journal integration cannot retrieve the document. Constraint: document id must come from journalpost metadata. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve journalpost` | `journalpost-not-found outcome` | Journalpost is missing. Why: No metadata can return. Constraint: Journalpost must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve journal document resource` | `document-membership outcome` | Document is missing or not in journalpost. Why: Relationship cannot resolve. Constraint: Document must belong to journalpost. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `retrieve journal document PDF` | `document-membership outcome` | Document is missing or not in journalpost. Why: Relationship cannot resolve. Constraint: Document must belong to journalpost. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `3/3`, application reached `3/3`, context-valid success `0/3`
- Happy-path summary: `0/1`
- Failure summary: `0/4`
- Behavior outcome checklist summary: `0/5`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 4 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B53`: `Journal an incoming journalpost`

- Business goal: Journal an incoming journalpost to the correct unit/task context.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The journalpost is journaled with supplied document metadata and unit context.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `journal journalpost` | `POST /api/journalpost/{journalpostId}/journalfør/{oppgaveId}` with `journalpostId=J1`, `oppgaveId=O1`, query `journalfoerendeEnhet=E1`, and body `RestJournalføring` where every `dokumenter[].dokumentTittel` is non-empty to journal the post | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_156_postOnJournalf_rCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_173_postOnJournalf_rReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve journalpost` | `GET /api/journalpost/{journalpostId}/hent` with `journalpostId=J1` to inspect metadata after journaling | No | No context-valid verification with required binding/state. |
| 2 | `retrieve journaling task data` | `GET /api/oppgave/{oppgaveId}` with `oppgaveId=O1` to inspect task context | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `journal journalpost` | `journaling method branch for any dokumentTittel is blank or missing` | any `dokumentTittel` is blank or missing. Why: controller throws functional error before journalføring. Constraint: all documents must have titles. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `journal journalpost` | `journalpost/treatment outcomes` | Journalpost or linked treatment is missing. Why: Journaling context cannot establish. Constraint: Both objects must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `journal journalpost` | `post-update completeness guard` | After external update, documents or received date remain absent. Why: The incomplete journalpost is rejected. Constraint: Documents and received date must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 3 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B54`: `Retrieve feature toggles`

- Business goal: Return enabled/disabled state for requested feature toggles.
- Starting point: `No prior service state`
- Expected business result: The caller receives feature-toggle values.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve feature toggles` | `POST /api/feature` with body `[T1,T2]` to retrieve toggle values | Yes | Yes | Yes | Test_1::test_4_postOnFeatureReturns200 asserts all requested toggle booleans. EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_4_postOnFeatureReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_9_postOnFeatureReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Covered` — Test_1::test_4_postOnFeatureReturns200 asserts all requested toggle booleans.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`
- Status and confidence: `Covered` / `High`
- Exact gap: No authoritative happy-path or failure gap.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B55`: `Check person access`

- Business goal: Determine whether the current caller may access a person and see the person’s discretion code.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Caller receives an access decision and discretion/address-protection information.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `check person access` | `POST /api/tilgang` with body `brukerIdent=P1` and current caller context `C1` to check access | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_6_postOnTilgangReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_6_postOnTilgangReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B56`: `Queue identity event handling`

- Business goal: Create asynchronous work for a new identity/PDL identity event.
- Starting point: `No prior service state`
- Expected business result: Asynchronous identity processing is queued.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `handle identity event` | `POST /api/ident` with body `ident=P1` to queue identity handling | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_8_postOnIdentReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_8_postOnIdentReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B57`: `Queue transitional-benefit event handling`

- Business goal: Create asynchronous work for a transitional-benefit decision event.
- Starting point: `No prior service state`
- Expected business result: Asynchronous transitional-benefit processing is queued.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `handle transitional benefit event` | `POST /api/overgangsstonad` with body `ident=P1` to queue event handling | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_7_postOnOvergangsstonadReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_7_postOnOvergangsstonadReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B58`: `Check rate-change eligibility for one case`

- Business goal: Determine whether a case can undergo manual rate change.
- Starting point: `Existing service state`
- Expected business result: The caller receives whether rate change can run for `F1`.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `check rate change eligibility` | `GET /api/satsendring/{fagsakId}/kan-kjore-satsendring` with `fagsakId=F1` to check eligibility | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_88_getOnKan_kjore_satsendringReturnsObject (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_105_getOnKan_kjore_satsendringReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B59`: `Queue rate change for one case`

- Business goal: Queue rate-change processing for a single case.
- Starting point: `Existing service state`
- Expected business result: A rate-change task is queued for one case.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `trigger rate change for case` | `GET /api/satsendring/kjorsatsendring/{fagsakId}` with `fagsakId=F1` to queue rate change for that case | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_58_getOnKjorsatsendrCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_106_getOnKjorsatsendrReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve full case` | `GET /api/fagsaker/{fagsakId}` with `fagsakId=F1` after task execution to inspect resulting state | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B60`: `Queue rate change for multiple cases`

- Business goal: Queue rate-change processing for a supplied set of cases.
- Starting point: `Existing service state`
- Expected business result: Rate-change tasks are queued for the supplied case ids.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `trigger rate change for cases` | `POST /api/satsendring/kjorsatsendring` with body `[F1,F2]` to queue rate changes for the supplied cases | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_22_postOnKjorsatsendringCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_58_postOnKjorsatsendringReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B61`: `Run synchronous rate change for one case`

- Business goal: Execute rate change immediately for one eligible case.
- Starting point: `Existing service state`
- Expected business result: The case is rate-changed synchronously.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `check rate change eligibility` | `GET /api/satsendring/{fagsakId}/kan-kjore-satsendring` with `fagsakId=F1` to obtain `kanKjøre=true` | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_88_getOnKan_kjore_satsendringReturnsObject (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_105_getOnKan_kjore_satsendringReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `run synchronous rate change` | `PUT /api/satsendring/{fagsakId}/kjor-satsendring-synkront` with `fagsakId=F1` to execute the rate change | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_138_putOnKjor_satsendring_synkrontReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java::test_73_putOnKjor_satsendring_synkrontReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve full case` | `GET /api/fagsaker/{fagsakId}` with `fagsakId=F1` to inspect resulting treatment/case state | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `run synchronous rate change` | `synchronous rate change branch for F1 is not eligible for rate change` | `F1` is not eligible for rate change. Why: rate-change service validates business eligibility. Constraint: eligible ongoing case/rate state. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `run synchronous rate change` | `latest-rate guard` | Case already has latest rate. Why: Duplicate transition is invalid. Constraint: Case must lack latest rate. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `run synchronous rate change` | `prior-treatment guard` | No previous decided ongoing treatment supplies basis. Why: Revision cannot derive. Constraint: A decided ongoing predecessor must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `run synchronous rate change` | `SatsendringSvar lifecycle outcomes` | Rate change is done or treatment cannot be locked, paused, or bypassed. Why: The workflow reports rejection. Constraint: Case and treatment must be eligible. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `2/2`, application reached `1/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/4`
- Behavior outcome checklist summary: `0/5`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 4 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B62`: `Queue rate change from identities`

- Business goal: Discover relevant cases from supplied identities and queue rate-change tasks.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Rate-change work is queued for old-rate cases found from supplied identities.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `trigger rate change from identities` | `POST /api/satsendring/kjorsatsendringForListeMedIdenter` with body `[P1,P2]` to resolve identities and queue matching rate changes | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_23_postOnKjorsatsendringForListeMedIdenterCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_59_postOnKjorsatsendringForListeMedIdenterReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `trigger rate change from identities` | `actor-not-found outcome` | Identity cannot resolve to actor. Why: Candidate discovery cannot scope. Constraint: Identity must resolve to actor. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B63`: `Queue technical dismissal for long-deadline treatments`

- Business goal: Create dismissal tasks for treatments with deadlines beyond a validation date.
- Starting point: `Existing service state`
- Expected business result: Technical dismissal tasks are queued for selected treatments.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `queue long-deadline dismissals` | `POST /api/satsendring/henleggBehandlingerMedLangFristSenereEnn/{valideringsdato}` with `valideringsdato=D_after_one_month` and body `["B1","B2"]` to queue technical dismissal tasks | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_97_postOnHenleggBehandlingerMedLangFristSenereEnnReturns400 (400); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_125_postOnHenleggBehandlingerMedLangFristSenereEnnReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `search tasks` | `POST /api/oppgave/hent-oppgaver` with task query for the treatment ids to inspect created tasks | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `queue long-deadline dismissals` | `dismissal selection branch for valideringsdato is invalid or too early` | `valideringsdato` is invalid or too early. Why: controller returns bad request. Constraint: date must be a valid future maintenance threshold. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B64`: `Identify ongoing cases without latest rate`

- Business goal: Start background discovery of ongoing cases missing the latest rate.
- Starting point: `Existing service state`
- Expected business result: A background analysis starts and returns a call id.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `find cases without latest rate` | `POST /api/satsendring/saker-uten-sats` with no case-specific body to start async search and capture `callId=CID1` | Yes | Yes | Yes | Test_1::test_40_postOnSaker_uten_satsReturns200 asserts a callId response. EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_40_postOnSaker_uten_satsReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_57_postOnSaker_uten_satsReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Covered` — Test_1::test_40_postOnSaker_uten_satsReturns200 asserts a callId response.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`
- Status and confidence: `Covered` / `Medium`
- Exact gap: No authoritative happy-path or failure gap.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B65`: `Run consistency reconciliation dry run`

- Business goal: Queue economy consistency reconciliation without sending to the economy system.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: A dry-run reconciliation task is queued.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `run consistency dry run` | `POST /api/konsistensavstemming/dryrun` with no body to queue dry-run reconciliation | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_66_postOnDryrunReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_117_postOnDryrunReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B66`: `Run real consistency reconciliation`

- Business goal: Queue economy consistency reconciliation that sends to the economy system.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Real consistency reconciliation is queued for economy sending.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `run consistency reconciliation` | `POST /api/konsistensavstemming/run` with body `triggerTid=T1` to queue real reconciliation | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_65_postOnRunReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_116_postOnRunReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B67`: `Retrieve internal and application statistics`

- Business goal: Read aggregate service statistics and application counts.
- Starting point: `Existing service state`
- Expected business result: Internal statistics are returned for operational reporting.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve internal statistics` | `GET /api/internstatistikk` with no parameters to retrieve aggregate counts | Yes | Yes | Yes | Direct 200 success assertions. EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_5_getOnInternstatistikkReturnsObject (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_7_getOnInternstatistikkReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |
| 2 | `retrieve application statistics` | `GET /api/internstatistikk/antallSoknader` with query `fom=D1`, `tom=D2` to retrieve application statistics | Yes | Yes | Yes | Direct 200 success assertions. EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_27_getOnAntallSoknaderReturnsObject (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_45_getOnAntallSoknaderReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — both reads are context-valid but occur in separate reset-isolated test methods.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `2/2`, application reached `2/2`, context-valid success `2/2`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Partially Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: `T3`.

### `B68`: `Retrieve treatment statistics payload`

- Business goal: Map one treatment to a DVH treatment statistics payload.
- Starting point: `Existing service state`
- Expected business result: The caller receives a treatment-level statistics payload.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve treatment statistics` | `GET /api/saksstatistikk/behandling/{behandlingId}` with `behandlingId=B1` to map treatment statistics | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_90_getOnBehandlReturnsObject (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_108_getOnSaksstatistikkBehandlWithQueryParamReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `retrieve treatment statistics` | `treatment mapping branch for missing/incomplete treatment state` | missing/incomplete treatment state. Why: controller logs and rethrows mapping errors. Constraint: treatment must be mappable. | Unclear | Low | A missing-object response is plausible, but overlapping documented discriminators cannot be separated. | No state/side-effect assertion separates the overlapping rows. | Mapped service is covered in union, without unique discriminator attribution. |
| `retrieve treatment statistics` | `treatment/unit outcomes` | Treatment or handling unit is missing. Why: Statistics context cannot map. Constraint: Treatment and unit must exist. | Unclear | Low | A missing-object response is plausible, but overlapping documented discriminators cannot be separated. | No state/side-effect assertion separates the overlapping rows. | Mapped service is covered in union, without unique discriminator attribution. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Unclear` / `Low`
- Exact gap: Discriminator-level evidence is ambiguous; no valid happy path exists.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B69`: `Retrieve case statistics payload`

- Business goal: Map one case to a DVH case statistics payload.
- Starting point: `Existing service state`
- Expected business result: The caller receives a case-level statistics payload.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve case statistics` | `GET /api/saksstatistikk/sak/{fagsakId}` with `fagsakId=F1` to map case statistics | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_89_getOnSakReturnsObject (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_107_getOnSakReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `retrieve case statistics` | `case mapping branch for F1 cannot be mapped` | `F1` cannot be mapped. Why: statistics service requires valid case state. Constraint: case must exist and be mappable. | Unclear | Low | A missing-object response is plausible, but overlapping documented discriminators cannot be separated. | No state/side-effect assertion separates the overlapping rows. | Mapped service is covered in union, without unique discriminator attribution. |
| `retrieve case statistics` | `case/owner outcomes` | Case or owner person is missing. Why: Statistics context is absent. Constraint: Case and owner must exist. | Unclear | Low | A missing-object response is plausible, but overlapping documented discriminators cannot be separated. | No state/side-effect assertion separates the overlapping rows. | Mapped service is covered in union, without unique discriminator attribution. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Unclear` / `Low`
- Exact gap: Discriminator-level evidence is ambiguous; no valid happy path exists.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B70`: `Register statistics message as sent`

- Business goal: Persist that an externally sent statistics message has been sent and should not be resent.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The statistics message is marked as sent.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `register statistics sent` | `POST /api/saksstatistikk/registrer-sendt-fra-statistikk` with body `offset=O1`, `type=SAK`, `json=J1`, `sendtTidspunkt=T1` to register sent state | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_24_postOnRegistrer_sendt_fra_statistikkCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_61_postOnRegistrer_sendt_fra_statistikkReturns401 (401) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B71`: `Retrieve benefit statistics decisions`

- Business goal: Map treatment ids to DVH V2 benefit-statistics decision payloads.
- Starting point: `Existing service state`
- Expected business result: The caller receives DVH V2 benefit-statistics payloads.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `retrieve benefit statistics decisions` | `POST /api/stonadsstatistikk/vedtakV2` with body `[B1,B2]` to retrieve benefit-statistics decisions | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_37_postOnVedtakV2Returns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_53_postOnVedtakV2Returns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `retrieve benefit statistics decisions` | `decision mapping branch for a treatment id cannot be mapped` | a treatment id cannot be mapped. Why: statistics mapping requires complete treatment/decision state. Constraint: mappable treatment ids. | Unclear | Low | A missing-object response is plausible, but overlapping documented discriminators cannot be separated. | No state/side-effect assertion separates the overlapping rows. | Mapped service is covered in union, without unique discriminator attribution. |
| `retrieve benefit statistics decisions` | `mapping prerequisites` | Treatment, active person basis, decision date, or benefit-person reference is absent. Why: Decision payload cannot build. Constraint: Complete references are required. | Unclear | Low | A missing-object response is plausible, but overlapping documented discriminators cannot be separated. | No state/side-effect assertion separates the overlapping rows. | Mapped service is covered in union, without unique discriminator attribution. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Unclear` / `Low`
- Exact gap: Discriminator-level evidence is ambiguous; no valid happy path exists.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B72`: `Queue unsent benefit statistics`

- Business goal: Queue publication tasks for supplied treatments that have not already been sent.
- Starting point: `Existing service state`
- Expected business result: Publish tasks are created only for unsent eligible treatments.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `queue unsent benefit statistics` | `POST /api/stonadsstatistikk/send-til-dvh` with body `[B1,B2]` to queue unsent benefit-statistics publication | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_38_postOnSend_til_dvhReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_54_postOnSend_til_dvhReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve benefit statistics decisions` | `POST /api/stonadsstatistikk/vedtakV2` with body `[B1,B2]` to inspect payloads | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `queue unsent benefit statistics` | `mapping prerequisites` | Treatment, active basis, decision date, or benefit-person reference is absent. Why: Publication payload cannot build. Constraint: Complete references are required. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B73`: `Manually queue benefit statistics`

- Business goal: Queue benefit-statistics publication for supplied treatments without the normal sent-state filter.
- Starting point: `Existing service state`
- Expected business result: Benefit-statistics publish tasks are manually queued.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `queue benefit statistics manually` | `POST /api/stonadsstatistikk/send-til-dvh-manuell` with body `[B1,B2]` to manually queue benefit statistics | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_39_postOnSend_til_dvh_manuellReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_55_postOnSend_til_dvh_manuellReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve benefit statistics decisions` | `POST /api/stonadsstatistikk/vedtakV2` with body `[B1,B2]` to inspect payloads | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `queue benefit statistics manually` | `mapping prerequisites` | Treatment, active basis, decision date, or benefit-person reference is absent. Why: Publication payload cannot build. Constraint: Complete references are required. | Covered | Medium | Test_1::test_39_postOnSend_til_dvh_manuellReturns200: id 392; wrapper FEILET and exact missing-treatment message. | Reset-empty state establishes the condition; source throws before mutation. No direct post-failure DB query. | report.xml: StønadsstatistikkController.kt 55 covered; lines 56-59 missed, proving failure before task construction/save. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `1/1`
- Behavior outcome checklist summary: `1/2`
- Status and confidence: `Partially Covered` / `High`
- Exact gap: Missing continuous documented happy path and 0 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B74`: `Resend manual migration statistics`

- Business goal: Backfill benefit statistics for eligible manual migration treatments.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Publish tasks are created for eligible manual migration treatments.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `resend migration statistics` | `POST /api/stonadsstatistikk/ettersend-manuell-migrering/{dryRun}` with `dryRun=false` to create backfill tasks | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_95_postOnEttersend_manuell_migrReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_122_postOnEttersend_manuell_migrReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `resend migration statistics` | `mapping prerequisites` | Treatment, active basis, decision date, or benefit-person reference is absent. Why: Backfill payload cannot build. Constraint: Complete references are required. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B75`: `Complete an administrative task list with partial success`

- Business goal: Attempt to complete a list of tasks administratively and report failures.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Some or all supplied tasks are completed; response reports failure count.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `finish admin task list` | `POST /api/forvalter/ferdigstill-oppgaver` with body `[O1,O2]` to complete tasks administratively | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_48_postOnFerdigstill_oppgaverReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_78_postOnFerdigstill_oppgaverReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `search tasks` | `POST /api/oppgave/hent-oppgaver` with a task query to inspect remaining open tasks | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `finish admin task list` | `task completion branch for one task id is invalid` | one task id is invalid. Why: per-task completion catches/logs failure and reports failed count. Constraint: each task id must be completable for full success. | Unclear | Low | A missing-object response is plausible, but overlapping documented discriminators cannot be separated. | No state/side-effect assertion separates the overlapping rows. | Mapped service is covered in union, without unique discriminator attribution. |
| `finish admin task list` | `per-task completion outcome` | One task cannot be completed. Why: It is reported failed while others continue. Constraint: Every task must be completable for all-success. | Unclear | Low | A missing-object response is plausible, but overlapping documented discriminators cannot be separated. | No state/side-effect assertion separates the overlapping rows. | Mapped service is covered in union, without unique discriminator attribution. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Unclear` / `Low`
- Exact gap: Discriminator-level evidence is ambiguous; no valid happy path exists.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B76`: `Restart small child supplement job`

- Business goal: Trigger manual restart logic for small child supplement processing.
- Starting point: `Existing service state`
- Expected business result: Manual restart logic runs and may create follow-up tasks.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `restart small child supplement job` | `POST /api/forvalter/start-manuell-restart-av-smaabarnstillegg-jobb/skalOppretteOppgaver/{skalOppretteOppgaver}` with `skalOppretteOppgaver=true` to trigger restart and task creation | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_160_postOnSkalOppretteOppgavCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_180_postOnSkalOppretteOppgavCauses500_internalServerError (500) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `restart small child supplement job` | `active-decision prerequisite` | A selected decided treatment has no active decision. Why: No finalized restart basis exists. Constraint: Active decision is required. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B77`: `Send payment orders administratively`

- Business goal: Generate and send payment orders to the economy system for supplied treatments.
- Starting point: `Existing service state`
- Expected business result: Payment orders are generated/sent where possible; per-treatment failures are logged while response can still be `OK`.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `send payment orders administratively` | `POST /api/forvalter/lag-og-send-utbetalingsoppdrag-til-økonomi` with body `[B1,B2]` to send payment orders | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_44_postOnLag_og_send_utbetalingsoppdrag_til__konomiReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_74_postOnLag_og_send_utbetalingsoppdrag_til__konomiReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `send payment orders administratively` | `awarded-benefit lookup` | Awarded-benefit state is absent. Why: No order can generate. Constraint: Awarded benefit must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `send payment orders administratively` | `existing-order guard` | A payment order already exists. Why: Duplicate generation is rejected. Constraint: No order may already exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `send payment orders administratively` | `later-sent-treatment guard` | A later treatment was sent to economy. Why: Ordering would be violated. Constraint: Orders must follow treatment order. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `send payment orders administratively` | `active-decision prerequisite` | No active decision exists. Why: No finalized basis exists. Constraint: Active decision must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/4`
- Behavior outcome checklist summary: `0/5`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 4 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B78`: `Bulk corrected payment-order resend`

- Business goal: Generate and implement corrected payment orders for a list of treatments.
- Starting point: `Existing service state`
- Expected business result: Corrected payment orders are implemented for successful treatments; failures are reported without rolling back successes.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `resend corrected payment orders` | `POST /api/forvalter/sendKorrigertUtbetalingsoppdragForBehandlinger` with body `[B1,B2]` to resend corrected payment orders in bulk | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_42_postOnSendKorrigertUtbetalingsoppdragForBehandlingerReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_72_postOnSendKorrigertUtbetalingsoppdragForBehandlingerReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `find payment-order issues` | `POST /api/forvalter/finnBehandlingerMedPotensieltFeilUtbetalingsoppdrag` with no body to discover candidates before repair | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_47_postOnFinnBehandlingerMedPotensieltFeilUtbetalingsoppdragReturns200 (200) |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `resend corrected payment orders` | `correction guards` | Treatment is inactive, lacks awarded benefit, has empty/inconsistent periods, or yields no order. Why: It is reported in `harFeil`. Constraint: Active complete correctable state is required. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B79`: `Single-version corrected payment-order resend`

- Business goal: Generate and implement a corrected payment order for one treatment and version.
- Starting point: `Existing service state`
- Expected business result: The selected corrected payment order is implemented or reported in `harFeil`.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `resend corrected payment order version` | `POST /api/forvalter/sendKorrigertUtbetalingsoppdragForBehandling/{behandlingId}/{versjon}` with `behandlingId=B1`, `versjon=V1` to resend that version | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_164_postOnSendKorrigertUtbetalingsoppdragForBehandlReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_174_postOnSendKorrigertUtbetalingsoppdragForBehandlReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `check incorrect cessation dates` | `POST /api/forvalter/sjekkOmTilkjentYtelseForBehandlingHarUkorrektOpphørsdato` with body `[B1]` to inspect cessation-date issue state before repair | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `resend corrected payment order version` | `version correction method branch for treatment is not active or corrected periods do not match erroneous periods` | treatment is not active or corrected periods do not match erroneous periods. Why: `ForvalterService` validates active treatment and corrected-period consistency. Constraint: version-specific correction consistency. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `resend corrected payment order version` | `version-correction guards` | Treatment is inactive, lacks awarded benefit, has empty/inconsistent periods, or yields no order. Why: Version cannot be corrected. Constraint: Complete consistent state is required. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 2 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B80`: `Run unvalidated rate change administratively`

- Business goal: Run simplified rate change for supplied cases without normal validation.
- Starting point: `Existing service state`
- Expected business result: Rate change is attempted for supplied cases without the normal validation gate.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `run rate change without validation` | `POST /api/forvalter/kjor-satsendring-uten-validering` with body `[F1,F2]` to run unvalidated rate change | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_45_postOnKjor_satsendring_uten_valideringReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_75_postOnKjor_satsendring_uten_valideringReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve full case` | `GET /api/fagsaker/{fagsakId}` with `fagsakId=F1` to inspect case state | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `run rate change without validation` | `rate-change method branch for fagsakId=F404 does not exist` | `fagsakId=F404` does not exist. Why: service cannot load/process case; failure is logged. Constraint: case id must exist for successful processing. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `run rate change without validation` | `case/revision/active-treatment/basis outcomes` | Case, prior revision basis, eligible active state, or prior person basis is absent. Why: Administrative rate change cannot complete. Constraint: All case and revision prerequisites must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 2 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B81`: `Identify payments over 100 percent`

- Business goal: Start background analysis for payments exceeding 100 percent.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Background payment-overlap analysis starts.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `identify payments over 100 percent` | `POST /api/forvalter/identifiser-utbetalinger-over-100-prosent` with no body to start analysis and capture `callId=C1` | Yes | Yes | Yes | Test_1::test_46_postOnIdentifiser_utbetalinger_over_100_prosentReturns200 asserts the callId label. EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_46_postOnIdentifiser_utbetalinger_over_100_prosentReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_76_postOnIdentifiser_utbetalinger_over_100_prosentReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Covered` — Test_1::test_46_postOnIdentifiser_utbetalinger_over_100_prosentReturns200 asserts the callId label.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`
- Status and confidence: `Covered` / `Medium`
- Exact gap: No authoritative happy-path or failure gap.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B82`: `Find payment-order issue candidates`

- Business goal: Identify treatments with potentially incorrect payment orders.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Candidate treatments with payment-order issues are returned.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `find payment-order issues` | `POST /api/forvalter/finnBehandlingerMedPotensieltFeilUtbetalingsoppdrag` with no body to return payment-order issue candidates | Yes | Yes | Yes | Test_1::test_47_postOnFinnBehandlingerMedPotensieltFeilUtbetalingsoppdragReturns200 asserts both result collections. EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_47_postOnFinnBehandlingerMedPotensieltFeilUtbetalingsoppdragReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_77_postOnFinnBehandlingerMedPotensieltFeilUtbetalingsoppdragReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Covered` — Test_1::test_47_postOnFinnBehandlingerMedPotensieltFeilUtbetalingsoppdragReturns200 asserts both result collections.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`
- Status and confidence: `Covered` / `High`
- Exact gap: No authoritative happy-path or failure gap.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B83`: `Check incorrect cessation dates for selected treatments`

- Business goal: Validate payment-order cessation dates for supplied treatment ids.
- Starting point: `Existing service state`
- Expected business result: The response contains only treatments whose payment orders have incorrect cessation dates.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `check incorrect cessation dates` | `POST /api/forvalter/sjekkOmTilkjentYtelseForBehandlingHarUkorrektOpphørsdato` with body `[B1,B2]` to validate cessation dates | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_31_postOnSjekkOmTilkjentYtelseForBehandlingHarUkorrektOpph_rsdatoCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_71_postOnSjekkOmTilkjentYtelseForBehandlingHarUkorrektOpph_rsdatoReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `check incorrect cessation dates` | `awarded-benefit not-found outcome` | Treatment has no awarded-benefit row. Why: No periods can be checked. Constraint: Awarded benefit must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B84`: `Populate support dates for one treatment`

- Business goal: Populate support-from/support-to dates for one treatment.
- Starting point: `Existing service state`
- Expected business result: The treatment has support-from/support-to values populated when derivation succeeds.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `populate support dates for treatment` | `POST /api/forvalter/populer-stonad-fom-tom/{behandlingId}` with `behandlingId=B1` to populate dates | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_71_postOnPopuler_stonad_fom_tomCauses500_internalServerError (500); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_133_postOnPopuler_stonad_fom_tomReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect updated treatment state | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `populate support dates for treatment` | `awarded-benefit not-found outcome` | Treatment has no awarded-benefit row. Why: No dates can populate. Constraint: Awarded benefit must exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B85`: `Populate support dates in bulk`

- Business goal: Populate support end dates for multiple active treatments up to a limit.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Candidate treatments are updated where possible and endpoint returns `ok`.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `populate support dates in bulk` | `POST /api/forvalter/populer-stonad-fom-tom-alle/{limit}` with `limit=100` to populate dates in bulk | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_96_postOnPopuler_stonad_fom_tom_alReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_134_postOnPopuler_stonad_fom_tom_alReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `populate support dates in bulk` | `per-candidate race outcome` | Candidate disappears or becomes ineligible before update. Why: That candidate fails while others continue. Constraint: Candidate must remain eligible. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |
| `populate support dates in bulk` | `partial-persistence outcome` | A selected candidate fails validation during the loop. Why: The loop records the failure and continues. Constraint: Each candidate must remain eligible. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 2 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B86`: `Find cases to close`

- Business goal: Discover cases that should be closed because they have no ongoing entitlement.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Operators receive a list of closable cases.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `find cases to close` | `GET /api/forvalter/finnFagsakerSomSkalAvsluttes` with no body to retrieve closable case ids | Yes | Yes | Yes | Test_0::test_30_getOnFinnFagsakerSomSkalAvsluttesReturnsEmptyList asserts the scan result. EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_30_getOnFinnFagsakerSomSkalAvsluttesReturnsEmptyList (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_48_getOnFinnFagsakerSomSkalAvsluttesReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Covered` — Test_0::test_30_getOnFinnFagsakerSomSkalAvsluttesReturnsEmptyList asserts the scan result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve full case` | `GET /api/fagsaker/{fagsakId}` with a returned `fagsakId=F1` to inspect a candidate case | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`
- Status and confidence: `Covered` / `High`
- Exact gap: No authoritative happy-path or failure gap.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B87`: `Update case ongoing status`

- Business goal: Bulk update ongoing/closed status on cases according to service rules.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Case ongoing/closed statuses are updated according to current entitlement state.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `update case ongoing status` | `POST /api/forvalter/oppdaterLøpendeStatusPåFagsaker` with no body to update case statuses | Yes | Yes | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_43_postOnOppdaterL_pendeStatusP_FagsakerReturns200 (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_73_postOnOppdaterL_pendeStatusP_FagsakerReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `find cases to close` | `GET /api/forvalter/finnFagsakerSomSkalAvsluttes` with no body before the update to inspect likely candidates | Yes | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_30_getOnFinnFagsakerSomSkalAvsluttesReturnsEmptyList (200) |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `update case ongoing status` | `per-case reload not-found outcome` | Selected case disappears before reload. Why: Its status cannot update. Constraint: Selected case must still exist. | Not Covered | High | No test uniquely establishes this post-binding condition with unrelated prerequisites valid. | No matching state or side-effect assertion. | No discriminator-specific corroboration sufficient for credit. |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path and 1 concrete failure item(s).
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B88`: `Find migration duplicates with ongoing Infotrygd case`

- Business goal: Identify open cases with multiple migration treatments and an ongoing Infotrygd case.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Operators receive migration duplicate cases that also overlap an ongoing Infotrygd case.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `find migration duplicates with Infotrygd` | `GET /api/forvalter/finnÅpneFagsakerMedFlereMigreringsbehandlingerOgLøpendeSakIInfotrygd` with no body to list anomalies | Yes | Yes | Yes | Test_0::test_29_getOnFinn_pneFagsakerMedFlereMigreringsbehandlingerOgL_pendeSakIInfotrygdReturnsEmptyList asserts the scan result. EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_29_getOnFinn_pneFagsakerMedFlereMigreringsbehandlingerOgL_pendeSakIInfotrygdReturnsEmptyList (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_47_getOnFinn_pneFagsakerMedFlereMigreringsbehandlingerOgL_pendeSakIInfotrygdReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Covered` — Test_0::test_29_getOnFinn_pneFagsakerMedFlereMigreringsbehandlingerOgL_pendeSakIInfotrygdReturnsEmptyList asserts the scan result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`
- Status and confidence: `Covered` / `High`
- Exact gap: No authoritative happy-path or failure gap.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B89`: `Find migration duplicates`

- Business goal: Identify open cases with multiple migration treatments.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: Operators receive open cases with multiple migration treatments.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `find migration duplicates` | `GET /api/forvalter/finnÅpneFagsakerMedFlereMigreringsbehandlinger` with no body to list local migration duplicates | Yes | Yes | Yes | Test_0::test_28_getOnFinn_pneFagsakerMedFlereMigreringsbehandlingerReturnsEmptyList asserts the scan result. EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_28_getOnFinn_pneFagsakerMedFlereMigreringsbehandlingerReturnsEmptyList (200); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java::test_46_getOnFinn_pneFagsakerMedFlereMigreringsbehandlingerReturns401 (401) | Precise 500 fault target or asserted 200/application wrapper corroborates mapped entry; union JaCoCo is not per-test. |

- Happy-path item: `Covered` — Test_0::test_28_getOnFinn_pneFagsakerMedFlereMigreringsbehandlingerReturnsEmptyList asserts the scan result.

#### Optional verification coverage

No optional verification workflow is documented.

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`
- Status and confidence: `Covered` / `High`
- Exact gap: No authoritative happy-path or failure gap.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

### `B90`: `Fill empty condition start dates in preprod`

- Business goal: Mutate preprod/local test data by filling missing condition start dates from birth dates.
- Starting point: `Existing service state`
- Expected business result: Empty condition start dates are filled for the treatment in preprod-like environments.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `fill condition dates in preprod` | `PUT /api/preprod/{behandlingId}/fyll-ut-vilkarsvurdering` with `behandlingId=B1` in preprod/dev-postgres-preprod runtime to fill dates | Yes | No | No | EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_139_putOnFyll_ut_vilkarsvurderingReturns401 (401); EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java::test_171_putOnFyll_ut_vilkarsvurderingReturns403 (403) | No post-binding mapped-entry evidence; only auth/admission or framework binding. |

- Happy-path item: `Not Covered` — no single test executes every required step in order with documented bindings and terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve treatment` | `GET /api/behandlinger/{behandlingId}` with `behandlingId=B1` to inspect condition dates | No | No context-valid verification with required binding/state. |

#### Concrete business-failure coverage

No concrete business-failure item is documented for this behavior.

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered` / `High`
- Exact gap: Missing continuous documented happy path.
- Recommended test IDs that close the gap: No prioritized implementation-ready test assigned; use the exact uncovered row keys above.

## Cross-Behavior Gaps

- Route probes dominate: all 153 required steps were attempted, but 166 application calls returned 401 and 175 returned 403; those calls prove routing only.
- Every generated test calls `resetDatabase(Arrays.asList())` and `resetStateOfSUT()` in `@BeforeEach`; no test-specific SQL insertion exists. State-dependent steps therefore cannot be composed across test methods.
- No generated test captures and reuses documented response values such as `fagsakId`, `behandlingId`, decision ids, task ids, `callId`, or freshness/version values in a later business call.
- Many HTTP 200 responses wrap `FEILET` or `FUNKSJONELL_FEIL`; HTTP status alone was not treated as success.
- Mutation tests generally lack database, derived-state, event, task, journal, DVH, payment-order, or external side-effect assertions.
- Several 500s are framework deserialization/conversion failures, unsupported content types, missing `task` relation errors, unavailable PDL/Dokarkiv/Oppgave/Infotrygd services, or unexpected `Result must not be null`; none received business-failure credit.
- JaCoCo branch coverage is only 178/11,102 (1.6%). Aggregate union proves broad controller/service contact but cannot connect a covered branch to a particular reset-isolated test without matching assertions.
- Async endpoints B64 and B81 prove trigger responses only. The specification has no result-retrieval function; completion/result verification therefore remains unavailable, as already noted by the authoritative document.

## Suggested Additional Tests

### Test `T1`: `create and idempotently reuse one normal case`

- Priority: `P0`
- Target behavior ID and name: `B1 — Establish a case and reuse the case identity`
- Target checklist item: happy path; exact functions `create case` and `return existing case`
- Test category: success and idempotency
- Why needed: the shared route is currently only rejected at generic gates, so neither function is distinguishable and the parent aggregate needed by many later behaviors is never created.
- Coverage delta if passing: function invocation `create case` and `return existing case` (+2); required-step application reach (+2); required-step context-valid success (+2); B1 happy path (+1); behavior-outcome (+1); B1 becomes Partially Covered until its documented failure is added.

#### Initial state and fixture plan

State:
- Reset database and SUT once at test start; do not reset between the two calls.
- Start with no `Fagsak`, institution, treatment, decision, or repayment row for person `12345678910`.
- Azure AD stub issues a token for NAV identity `Z999999` with effective `SAKSBEHANDLER` role; access-control stub returns access granted for `12345678910`.
- PDL/person-ident stub maps `12345678910` to actor id `1234567891000`; shadow-case integration accepts one create operation.
- Fixed clock: `2026-07-01T10:00:00+08:00`; feature/configuration values use normal-case defaults.
- Execute synchronously in one transaction-per-request; wait for any synchronous event listener, and poll a captured test publisher for at most 2 seconds if event delivery is asynchronous.
- API setup is used because case creation is the behavior under test; direct database insertion would replace and therefore not cover it.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B1 step 1 / `create case` | NAV `Z999999`; Azure AD issuer; `SAKSBEHANDLER`; person scope `12345678910` granted | `POST /api/fagsaker` | `Authorization: Bearer` token from configured Azure AD stub; `Content-Type: application/json`; `Accept: application/json`; no cookies | No path, query, or form parameters | `{"personIdent":"12345678910","fagsakType":"NORMAL","institusjon":null}` | Capture response `data.id -> fagsakId`; actor mapping `1234567891000` comes from stub contract | HTTP 201; JSON `status=SUKSESS`; non-null positive `data.id`; `data.type=NORMAL` if exposed | Exactly one NORMAL fagsak for actor `1234567891000`; one shadow-case creation; no treatment |
| 2 | B1 step 2 / `return existing case` | Same NAV identity, issuer, role, and person scope | `POST /api/fagsaker` | Authorization token with the same concrete claims; `Content-Type: application/json`; `Accept: application/json`; no cookies | No path, query, or form parameters | `{"personIdent":"12345678910","fagsakType":"NORMAL","institusjon":null}` | Reuse the same business key; assert response `data.id == fagsakId` captured at call 1 | HTTP 201; JSON `status=SUKSESS`; `data.id` equals captured `fagsakId` | Still exactly one matching fagsak; no duplicate shadow case, treatment, or statistics-create event |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1,2 | `personIdent` | JSON body | `12345678910` | 11 ASCII digits | Yes | Resolvable person identity accepted by configured stubs | Must map to the same actor and granted access on both calls | Holds the idempotency key constant |
| 1,2 | `fagsakType` | JSON body | `NORMAL` | enum string | Yes for explicitness | `NORMAL`, `INSTITUSJON`, and implementation-supported enum values | `institusjon.orgNummer` is not required for `NORMAL` | Exercises normal-case branch |
| 1,2 | `institusjon` | JSON body | `null` | nullable object | No | Null for normal case | Must remain null when testing the same uniqueness key | Prevents institution scoping |
| 1,2 | caller role | Azure AD claims/database role mapping | `SAKSBEHANDLER` for `Z999999` | authenticated role | Yes | At least `BehandlerRolle.SAKSBEHANDLER` | Must pass `verifiserHarTilgangTilHandling` before service entry | Avoids current 403 gate |
| 1,2 | person access | access-control stub | granted for `12345678910` | boolean domain decision | Yes | granted | Must be granted before creation | Isolates idempotency behavior |

#### Assertions

- Assert HTTP 201 on both calls and `status=SUKSESS` in both resource envelopes.
- Assert call 1 returns a positive `data.id`; bind it to `fagsakId`; assert call 2 returns the identical id.
- Query the repository after each call: count by actor `1234567891000`, type NORMAL, and null institution is exactly 1.
- Assert the persisted case remains in its initial created status and has no treatment children.
- Assert one shadow-case create side effect and one case-create/statistics event across both calls, not two.
- Assert no functional error, rollback, duplicate row, or second external create side effect.
- Corroborate `FagsakController.hentEllerOpprettFagsak`, `FagsakService.hentEllerOpprettFagsak`, both sides of `eksisterendeFagsak == null`, and the existing-case return branch in JaCoCo.

#### Isolation and variants

- Roll back or truncate all case, actor/person-ident, shadow-case test records after the method; reset access, PDL, and shadow-case stubs.
- Keep the fixed clock and actor id deterministic; do not reset between calls.
- Use a 2-second bounded poll only for an asynchronous test publisher; database assertions are synchronous.
- Separate variants: NORMAL with a different person creates a second case; INSTITUSJON with organization belongs in a separate success test; missing organization is T2.

### Test `T2`: `reject institution case without organization number after access succeeds`

- Priority: `P0`
- Target behavior ID and name: `B1 — Establish a case and reuse the case identity`
- Target checklist item: concrete failure; exact function `create case`; exact source discriminator `FagsakService.hentEllerOpprettFagsak institution guard`; condition `fagsakType=INSTITUSJON` without `institusjon.orgNummer`
- Test category: business failure and boundary
- Why needed: current calls stop at authentication/admission and never prove the documented institution business guard.
- Coverage delta if passing: exact-function invocation `create case` (+1 if T1 is absent); required-step application reach (+1 if T1 is absent); documented business-failure (+1); unique source business-branch (+1); behavior-outcome (+1); B1 becomes Covered when T1 also passes.

#### Initial state and fixture plan

State:
- Reset database and SUT; no fagsak exists for person `12345678910`.
- Use NAV identity `Z999999`, Azure AD issuer, effective `SAKSBEHANDLER` role, and granted person access.
- PDL/person-ident stub resolves `12345678910` to actor id `1234567891000`; all unrelated dependencies are healthy.
- Fixed clock: `2026-07-01T10:00:00+08:00`; normal transaction rollback rules apply.
- Violate only the institution organization prerequisite; JSON syntax, enum, identity, token, role, and access decision remain valid.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B1 failure / `create case` | NAV `Z999999`; Azure AD; `SAKSBEHANDLER`; granted person scope | `POST /api/fagsaker` | Authorization token from Azure AD stub; `Content-Type: application/json`; `Accept: application/json`; no cookies | No path, query, or form parameters | `{"personIdent":"12345678910","fagsakType":"INSTITUSJON","institusjon":null}` | Identity comes from deterministic PDL stub; no prior response binding | HTTP 400 functional error; body message `Mangler påkrevd variabel orgnummer for institusjon`; no success data | No INSTITUSJON fagsak, institution, treatment, or shadow case is created |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `personIdent` | JSON body | `12345678910` | 11 digits | Yes | Resolvable identity | Access must be granted and actor resolvable | Keeps unrelated gates valid |
| 1 | `fagsakType` | JSON body | `INSTITUSJON` | enum string | Yes | Implementation enum | Requires non-null `institusjon.orgNummer` | Selects target guard |
| 1 | `institusjon` | JSON body | `null` | nullable object | Invalid for selected type | Object with non-null organization is valid | Deliberately violates exactly one cross-field constraint | Exact target-invalid value |
| 1 | caller | token/role/access | `Z999999`, Azure AD, `SAKSBEHANDLER`, access granted | authenticated context | Yes | Authorized caseworker | Must pass all generic gates | Proves business, not admission, failure |

#### Assertions

- Assert HTTP 400 and the exact functional message `Mangler påkrevd variabel orgnummer for institusjon`.
- Assert the error is not Unauthorized, Forbidden, a Jackson conversion error, or an external-service availability error.
- Assert no fagsak/institution row for actor `1234567891000`, no shadow-case request, no treatment, and no case-created/statistics event.
- Assert transaction rollback leaves no partial local aggregate; permit only the already-completed read-only access/identity stub calls.
- Corroborate `FagsakService.kt` institution branch at the null-organization guard and exception mapping in JaCoCo.

#### Isolation and variants

- Truncate case/actor test data and reset all stubs after the method; keep fixed identity and clock.
- No asynchronous wait is required; assert the shadow-case/event capture remains empty for 500 ms.
- Separate tests are required for blank organization string, valid institution organization creation, and external TSS lookup failure; those are distinct branches.

### Test `T3`: `read both operational statistics views in one reset-isolated scenario`

- Priority: `P1`
- Target behavior ID and name: `B67 — Retrieve internal and application statistics`
- Target checklist item: happy path; exact functions `retrieve internal statistics` and `retrieve application statistics`
- Test category: success and regression
- Why needed: both steps already succeed separately, but reset isolation prevents the documented two-step behavior from being credited.
- Coverage delta if passing: B67 happy path (+1); behavior-outcome (+1); B67 moves from Partially Covered to Covered because it has no documented failure entries.

#### Initial state and fixture plan

State:
- Reset database and SUT once; retain an intentionally empty business database for both calls.
- NAV identity `Z999999` uses an Azure AD token accepted by both internal-statistics endpoints; no role cookie is supplied.
- Fixed clock: `2026-07-01T10:00:00+08:00`; explicit date parameters avoid default-window dependence.
- No external-domain stub is invoked; no transaction mutation or asynchronous work is expected.
- Empty direct database state is a read prerequisite, not a replacement for either API behavior.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B67 step 1 / `retrieve internal statistics` | NAV `Z999999`; Azure AD issuer; accepted operational reader context | `GET /api/internstatistikk` | Authorization token from Azure AD stub; `Accept: application/json`; no content type; no cookies | No path, query, or form parameters | No request body | Empty database fixed by reset | HTTP 200; `status=SUKSESS`; `data.antallFagsakerTotalt=0`; `data.antallFagsakerLøpende=0`; `data.antallBehandlingerIkkeFerdigstilt=0`; empty reason map | Database remains empty |
| 2 | B67 step 2 / `retrieve application statistics` | Same identity, issuer, and reader context | `GET /api/internstatistikk/antallSoknader?fom=2026-03-01&tom=2026-06-30` | Authorization token with the same concrete claims; `Accept: application/json`; no cookies | Query `fom=2026-03-01`; query `tom=2026-06-30`; no path/form parameters | No request body | Explicit constants, not a prior response | HTTP 200; `status=SUKSESS`; response `fom=2026-03-01`, `tom=2026-06-30`; ordinary and extended totals/paper/digital counts all 0; both digitalization rates `NaN` | Database remains empty |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1,2 | caller token | Authorization header | Azure AD token for `Z999999` | JWT accepted by configured stub | Yes | Valid, unexpired configured issuer | Same caller across both calls | Maintains one scenario |
| 2 | `fom` | query | `2026-03-01` | ISO-8601 LocalDate | Optional but supplied | Parseable date | Inclusive start must not be after `tom` | Stable four-month interval |
| 2 | `tom` | query | `2026-06-30` | ISO-8601 LocalDate | Optional but supplied | Parseable date | Inclusive end must be on/after `fom` | Stable closed interval |
| 1,2 | database state | repository | zero cases, treatments, applications | empty relational state | Valid | Non-negative aggregate counts | Same state across calls | Makes exact response deterministic |

#### Assertions

- Assert both HTTP statuses are 200 and both resource envelopes report `SUKSESS`.
- Assert every aggregate count listed in the call table, exact `fom`/`tom`, empty reason map, and `NaN` rates for zero denominators.
- Assert no row count changes in case, treatment, application, statistics-outbox, event, notification, task, journal, or message tables.
- Assert no external integration calls and no asynchronous work.
- Corroborate both `InternStatistikkController` methods and their repository aggregation lines in JaCoCo.

#### Isolation and variants

- Reset after the method; no inter-call reset, transaction rollback, or stub state change.
- Keep clock and token deterministic; no polling is needed.
- Separate boundary tests: `fom=tom`, `fom>tom`, malformed dates, and a populated fixture with one paper and one digital application.

## Notes And Assumptions

- The API was not executed. Evidence is limited to generated source, its embedded observed statuses/assertions, implementation source, and the supplied JaCoCo report.
- `report.xml` is treated as one already-combined execution report. `report.csv` was not numerically added because it represents the same run.
- Application reach excludes 500 responses caused by Jackson/body binding, enum/date conversion, or unsupported content type. It includes validly bound requests that entered application code and then failed on missing aggregate, repository, or external call. The two explicit 400s were reviewed individually: B40 is a post-binding business validation; B63 is a technical invalid-date result and receives no failure credit.
- Context-valid success is intentionally strict. Empty scan results count only for service-wide read/discovery behaviors whose specification permits no target-id setup; empty/no-op mutation requests do not count.
- B64 and B81 receive Medium confidence because tests assert the `callId` label but comment out the generated UUID value assertion; the endpoint contract and 200 response still establish trigger acceptance.
- B68, B69, B71, and B75 are Unclear: one missing-object setup plausibly matches multiple overlapping documented discriminator rows, while assertions and union JaCoCo cannot prove which exact source item should receive credit.
- No direct persistence checks exist in the generated corpus. Covered missing-object failures rely on reset-empty state, exact error assertions, source ordering, and precise JaCoCo lines; this is why mutation/queue failures are Medium rather than High confidence.
- Source/document discrepancy: many endpoints wrap functional failures in HTTP 200. Scoring follows documented business outcomes, not transport status.
- The function-invocation numerator withholds six exact functions on three shared routes because all generated calls are rejected before their actor/body/state discriminator.
