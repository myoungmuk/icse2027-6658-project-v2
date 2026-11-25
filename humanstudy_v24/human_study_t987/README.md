# T987 three-rater human review (paper Section IV-E, Table XI)

Three graduate students who are not authors (R1: master's student; R2, R3: integrated
bachelor's–master's students) each reviewed the same 987 unique SWE-bench issues used in the
paper, one study patch per issue: 569 developer, 115 test-passing agent and 303 test-failing
agent patches. Groups: 707 Lite/Verified factorial tasks, 140 7B follow-up tasks, 140 14B
follow-up tasks.

## Files

- `t987_human_labels.csv` — one row per case. Columns:
  - `case_id`, `study_group`, `patch_stratum`, `instance_id`
  - `R{1,2,3}_verdict` (merge / request_changes / reject / hold), `_blocking`,
    `_prior_awareness`, `_confidence`, `_translator`
  - `human_majority_merge` — 1/0 when at least two non-hold verdicts agree; empty otherwise
  - `displayed_ai_flag`, `displayed_ai_model`, `displayed_ai_condition` — the archived AI verdict
    shown to raters in the same file
  - `factorial_{7b,14b}_E?B?`, `followup_strict`, `followup_adjusted`, `hosted_claude_mergeable` —
    archived LLM verdicts on the same patch (1 = accept, 0 = not, empty = not applicable)
- `t987_rater_self_reports.csv` — 11 responses per rater restored from the original R1
  submission files (rows 1017–1027). The duration entries carry the author correction described below. R1–R3 in the `rater_id` column identify the three
  raters. The CSV preserves the original Korean responses and omits the name field.
  Rebuilding the 987 case labels from the restored submissions produced the same CSV bytes.
- `reproduce_t987.py` — recomputes every reported number (standard library only) and asserts
  the rounded paper values.

## Protocol and deviations

Raters saw the full issue and diff without held-out tests, execution results or source links,
and were instructed not to use AI tools, search, execute code or discuss cases. Compliance was
not monitored and per-case time was not recorded. The same file contained the archived AI
verdict and patch-origin flags. Raters were asked to save verdicts before reading the flags.
In the restored self-reports, all three raters reported that they had not viewed the AI flags
before recording their own verdicts. Flag viewing was not independently verified for each case.

Every rating reports prior awareness as "uncertain", so the prespecified agreement set (all three
"none", no hold) is empty. Fleiss’ κ and each pairwise Cohen’s κ in the manuscript use the same 841 cases with three non-hold verdicts. Planned faculty
adjudication was not performed. Human labels are unadjudicated majorities and are not
mergeability ground truth; the raters are not project maintainers.

Rater identities and free-text rationales are withheld. Rationales can be added only with each
rater's consent.

## Review-duration correction

On 25 November 2026 the authors supplied corrected total review times for R1 (6 hours),
R2 (7 hours), and R3 (8 hours). The three `total_review_time` rows record their
author-supplied provenance. The 987 case labels were regenerated from the corrected
submissions and match the earlier public case-label export byte for byte.

## Agreement and test-pass cue exclusion

Among the 841 cases with three non-hold verdicts, 724 are unanimous after grouping
request-changes and reject as non-merge. On the original three-category scale,
570 are unanimous (Fleiss’ κ = 0.63).

Of the 115 test-passing agent cases, the displayed condition identified 27 cases
where the archived model prompt had asserted test passage. Excluding these 27 cases
leaves 85 determinate human majorities, of which 63 accept the patch (74.1%).
The check uses the published displayed-condition column and measures acceptance
on the resulting subset. Rater attention to the cue and its effects remain unmeasured.

## Reviewer-item checks (paper Sections IV-C, IV-D, IV-E)

- `t987_reviewer_item_links.csv` — one row per case:
  - `followup_partition` — development / heldout for the two follow-up cohorts
  - `hosted_negctrl_packet_id`, `hosted_negctrl_diff_sha256_lf` — the hosted failing-patch
    control packet with an identical (LF-normalized) diff, if any
  - `pilot_v2_task_code`, `pilot_v2_source` — the adapted-pilot follow-up candidate with an
    identical diff, if any
- `reproduce_reviewer_items.py` — recomputes human corroboration of gold and failing-patch
  rejections, held-out follow-up agreement, and adapted-pilot agreement. It reads the archived
  packets and verdicts already in this repository (`historical_analysis/data/...`,
  `binary_pilots/followup_v2/...`) and checks each linked diff hash.

These are decision-level agreement rates with unadjudicated non-maintainer majorities; they do
not validate individual rationale sentences.
