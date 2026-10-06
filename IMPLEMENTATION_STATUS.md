# Implementation status

Updated: 2026-10-06

## Completed

**Milestone 1 — Book 1 Content Intelligence + Evidence Pipeline: implemented and
verified locally.** Milestone 1.5 live integration also passed. Broad model-quality
calibration and child-data deployment are not claimed complete.

- Reviewed every original repository file and all 50 Book 1 PDF pages.
- Preserved the Express/Mongoose modular monolith and existing API families.
- Locked all five SEL domains, 21 skill IDs/labels and four uppercase states.
- Reviewed/published ten Book 1 assets: six open-response assessments, one authored
  MCQ adaptation of the living-room scene, three exposure-only interventions.
- Corrected the living-room mapping and clarified cue ambiguity, scenario timing,
  hypothetical reasoning and authored extensions.
- Added immutable ContentRevision and RubricRevision records, PDF/content/rubric
  hashes and idempotent non-destructive publication.
- Required exact content versions, session IDs and client event IDs on ingestion.
- Kept raw interactions separate from evaluation attempts, evidence and exposure.
- Added deterministic choice/abstention evaluation and a Structured Outputs
  Responses API adapter for other open responses.
- Added deterministic evidence eligibility/strength policy; model confidence never
  controls strength. Secondary skill tags do not automatically generate evidence.
- Added transactional processing, idempotency, lease fencing, explicit failure
  recording and retry. Completed work is not evaluated again on retry.
- Added observation-only profiles for all 21 skills and append-only profile
  snapshots linked to their input evidence IDs.
- Updated next-action selection to exclude seen scenarios and report content gaps.
- Added validation, bounded inputs, sanitized errors, environment checks and
  loopback-only development startup.
- Pinned dependencies, added a lockfile, removed the vulnerable nodemon
  dependency chain and adopted Node's built-in watch mode.
- Updated README, source review and architecture decisions.

### Milestone 2 engineering foundation

- Implemented versioned deterministic evidence-quality policy
  `milestone2-evidence-quality-1.0.0`; developmental calibration is still pending.
- Added server-side observation timestamps and audited time recovery from original
  interactions for milestone-1 evidence, without editing historical evidence.
- Added lifetime/recent/older counts, conservative scenario/session/day independence
  candidates, conflicting-observation flags, context summaries and version groups.
- Quarantined unresolved multiple interpretations of one source; added per-evidence
  eligibility/exclusion reasons. Model confidence is not a profile input.
- Added transactional, idempotent refresh, parent-linked snapshots, before/after
  changes and child-scoped cursor pagination/history detail routes.
- Preserved previous snapshots on policy upgrades. Original V1 judgments cannot
  be returned as current states without refreshing under a supported policy.
- Corrected canonical timestamp hashing for profile input fingerprints.
- Retained all 21 states as INSUFFICIENT_EVIDENCE pending calibrated policy.
- Ran the updated application on `http://127.0.0.1:5051` with a disposable database.
- **56 automated tests passed; expanded real HTTP smoke test passed.** No additional
  OpenAI requests or API credit use was required for this deterministic milestone.
- See [ADR 003](docs/architecture/003-longitudinal-profile-foundation.md).

### Verification

- `npm test` after the Milestone 1.5 live run: **36 passed, 0 failed, 0 skipped**
  (original Milestone 1 acceptance had 34 passing tests).
- Latest Milestone 2 run: **56 passed, 0 failed, 0 skipped**. Covers temporal
  boundaries, repetitions, mixed/duplicate evidence, legacy time recovery, policy
  upgrades, child-scoped history, concurrent refresh and original-V1 state safety.
- Tests include a real MongoDB 8.0.12 replica set, Express HTTP requests and
  synthetic evaluator fixtures; no production database is used.
- Verified duplicate/concurrent submissions, transaction rollback, recovery,
  stale-worker fencing, immutable raw responses/evidence, version conflicts,
  explicit missing-key failures, ambiguous responses and exposure isolation.
- `npm run start:local`: actual application launched with ten seeded assets.
- `npm run smoke`: passed real HTTP health/content/assessment/replay/exposure/
  profile/provenance checks at `http://127.0.0.1:5050`.
- Online `npm audit --json`: **0 reported vulnerabilities** after watcher removal.
- JavaScript syntax checks passed.
- Milestone 1.5: **PASSED** on 2026-10-05, 10:00:40–10:00:53 UTC, after the key
  became available. Four synthetic B1-A4 responses reached the real Responses API.
- Results: positive -> moderate positive evidence; ambiguous -> abstention/no
  evidence; contradictory -> weak negative evidence; unusual reasonable -> weak
  positive evidence. All structured outputs passed schema and rubric validation.
- Verified unchanged raw responses, locked skill attribution, confidence/strength
  separation, provider/model/version provenance and evidence-linked profile updates.
- Controlled pre-provider failure and explicit live retry passed; successful
  replay/retry made no extra provider calls. Intervention exposure made no evidence.
- Requested and returned model: `gpt-6-luna`; OpenAI SDK `7.28.0`; evaluator
  `book1-evaluator-1.1.0`; prompt `sel-rubric-1.0.0`; content/rubric `1.1.0`.
- Usage: 2,164 input + 515 output = 2,679 tokens; 359 reasoning tokens are included
  in output, not added again; 0 cached tokens. Estimated standard cost $0.0004739.
  This is a rate-based estimate, not an invoice.
- Earlier credential preflight was blocked with zero calls; it is now resolved.
- Added explicit provider/API/SDK provenance and allowlisted token counts; SDK
  logging is disabled and provider endpoint pinned to the official API.
- Added opt-in `npm run verify:live`; profile thresholds, evidence policy, rubric
  and prompt semantics are unchanged. No key was printed, logged or written.

## Currently working on

- Milestone 1.5 verification and documentation are complete; all acceptance checks
  passed for the controlled four-response run.
- Milestone 2's longitudinal profile engineering foundation is complete and verified
  end-to-end. Its developmental transition/confidence calibration component remains
  outstanding and requires expert-reviewed rules; it is not claimed complete.
- No implementation currently running. Next remaining work includes approved
  developmental policy, replay/supersession and the intervention/reassessment loop.
- See [live verification decision](docs/architecture/002-live-verification.md)
  and [machine-readable result](docs/verification/milestone-1.5-live.json).

## Remaining

- Expert-approved age/language-specific rubrics, labeled evaluation examples and
  live model quality/calibration checks.
- Calibrated developmental transitions, evidence confidence, independence over
  time, contradiction handling, freshness and context-specific interpretation.
- Replay/supersession under newer rubrics/models without double-counting.
- Authored interventions aligned to assessed skills, durable decisions, fresh
  transfer scenarios, reassessment scheduling and longitudinal outcomes.
- Child/guardian identity and per-child authorization; consent, data minimization,
  approved provider processing, retention/deletion and audited privacy operations.
- Background recovery scheduling, operational monitoring and deployment hardening.
- Authenticated adult review and child experience frontends after contracts stabilize.

## Known issues / limits

- All profiles intentionally remain INSUFFICIENT_EVIDENCE with low confidence;
  descriptive counts must not be presented as validated developmental conclusions.
- The current evidence-strength anchors are provisional engineering defaults,
  not expert-calibrated psychometrics or efficacy claims.
- The 90-day recent window and independence-candidate selection are operational
  engineering conventions, not validated developmental thresholds. Session IDs
  are client-supplied and UTC-day separation does not establish true independence.
- Profile aging requires explicit refresh; GET exposes `asOf`, `nextRefreshAt` and
  `refreshRequired`. A periodic refresh scheduler remains to be implemented.
- All assessments measure scenario reasoning. They do not demonstrate actual
  empathy, regulation or persistence in everyday behavior.
- English and age band 8–10 are provisional content-authoring assumptions.
- The Book 1 catalog alone cannot establish cross-scenario longitudinal outcomes.
- The four-case live check is not expert calibration or a representative benchmark.
  Returned model ID is the alias `gpt-6-luna`, not a dated immutable snapshot;
  response IDs and evaluator/prompt/rubric versions are recorded, but exact model
  reproducibility is not guaranteed by the alias.
- No automatic recovery worker exists. After interruption, use event status and
  explicit retry; an active/stale processing lease expires after two minutes.
- Transactions require a MongoDB replica set. The local helper uses a disposable
  database and removes it on shutdown; it is not durable application storage.
- No authentication, consent or retention/deletion workflow exists yet. Use
  synthetic data only; the standard executable rejects public binding and
  production mode.
- Append-only enforcement is at the application/Mongoose layer, not a replacement
  for database permissions and an audited privacy deletion path.
- Existing V1 records are preserved but cannot be silently upgraded to complete
  provenance. Legacy evidence is excluded from the new profile policy.
- Profile snapshots currently recompute from all eligible evidence and retain
  their input IDs. Scale/retention optimization is deferred until access patterns
  and privacy policy are settled.

## Architecture decisions

1. Modular monolith: retain Node.js, Express, MongoDB and Mongoose.
2. Mutable content catalog plus independently immutable content/rubric revisions.
3. Raw interaction, evaluation run, evidence and intervention exposure are
   separate records. Derived results link to exact versions/hashes.
4. Deterministic interpretation where authored rules suffice; constrained LLM
   interpretation for open text. No LLM-generated overall child profile.
5. Model confidence is not evidence strength; intervention completion is not
   evidence; ambiguous responses are abstentions.
6. Exact client event IDs plus request hashes prevent duplicate delivery from
   inflating evidence. MongoDB transactions and leased ownership protect writes.
7. Withhold developmental inference until calibrated; preserve all locked states
   without assigning unsupported judgments. Report evidence coverage, conflicts
   and version differences separately from state or confidence.
8. Preserve historical V1 data rather than inventing missing provenance.
9. Loopback-only synthetic-data development until child privacy/access controls
   exist. No frontend or microservices added in this milestone.

Details: [ADR 001](docs/architecture/001-milestone-1.md) and
[Book 1 review](docs/book1-review.md). Longitudinal policy:
[ADR 003](docs/architecture/003-longitudinal-profile-foundation.md).
