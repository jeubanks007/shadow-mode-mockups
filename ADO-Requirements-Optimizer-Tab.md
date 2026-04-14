# ADO Requirements: Agent Optimizer Tab

**Feature:** Agent Optimizer Tab for Frontier Agent Catalog Evaluation Page
**Mockup:** https://jeubanks007.github.io/shadow-mode-mockups/frontier-evaluation.html (Optimizer tab)
**Date:** April 14, 2026 (Updated)

---

## Epic: Agent Optimizer Integration

**Description:** Add an "Optimizer" tab to the Frontier Agent Catalog evaluation detail page that provides AI-driven, prioritized action plans to improve agent performance. The tab analyzes evaluation data to surface root causes of failures, highlight priority scenarios, and recommend specific actions — ordered by impact (cases affected).

**Business Value:** Gives agent owners a clear, data-driven roadmap to improve agent quality metrics without manually analyzing thousands of evaluation cases. Reduces time-to-insight and accelerates the agent improvement lifecycle.

---

## User Story 1: Optimizer Tab Navigation

**Title:** As an agent owner, I want to access an "Optimizer" tab on the evaluation detail page so I can view optimization recommendations alongside existing evaluation data.

**Acceptance Criteria:**
- [ ] A new "Optimizer" tab button appears in the evaluation detail page tab bar, after the existing tabs (Overview, Scorecard, Shadow Metrics)
- [ ] Clicking the Optimizer tab displays the optimizer panel and hides other tab content
- [ ] Tab follows existing Fluent UI tab styling and interaction patterns
- [ ] Tab is visible for all agent stages that have evaluation data

---

## User Story 2: Improvement Targets Table with Clickable Metric Filtering

**Title:** As an agent owner, I want to see target scores for each metric and click a metric row to filter the action plan, so I can focus on the specific metric I need to improve.

**Acceptance Criteria:**
- [ ] The Improvement Targets table is displayed at the top of the Optimizer tab, directly below the header
- [ ] Table columns: Metric, Current, Target, Gap, Progress
- [ ] An "All Metrics" row is included at the top of the table as the default selection
- [ ] Target scores are derived from the average scores of passing cases
- [ ] Gap column shows the difference between current and target, color-coded:
  - Red for gaps ≥ 2.0
  - Orange/Yellow for gaps < 2.0
- [ ] Progress column shows a visual progress bar (current score / target score as percentage)
- [ ] Each metric row is clickable — clicking a row:
  - Highlights the selected row (teal background)
  - Filters the Prioritized Action Plan below to show only actions related to that metric
  - Displays a "Filtering: [Metric]" badge on the Action Plan section heading
- [ ] Clicking "All Metrics" resets the filter and shows all action items
- [ ] The active/selected row state persists while the user is on the Optimizer tab
- [ ] Available metrics are dynamically populated based on the evaluation data
- [ ] Table follows existing page table styling (Fluent UI DataGrid pattern)

---

## User Story 3: Prioritized Action Plan

**Title:** As an agent owner, I want to see a prioritized list of recommended actions to improve my agent, ordered by impact, so I can focus effort where it matters most.

**Acceptance Criteria:**
- [ ] Action plan items are displayed as cards, ordered by number of cases affected (descending)
- [ ] Each action card includes:
  - **Category indicator** via color-coded left border:
    - Red = Root cause / metric gap
    - Orange/Yellow = Priority scenario
    - Blue = Safety / escalation concern
    - Green = Knowledge base improvement
  - **Icon and title** describing the issue
  - **Detail text** with specific data (e.g., risk score, failing counts, weakest metric)
  - **"Recommended Action" box** with concrete steps to take
  - **Impact badges** showing cases affected and estimated pass rate lift
- [ ] Action items are categorized by type:
  - `root-cause`: Metric gaps identified from passing vs. failing case analysis
  - `scenario`: High-risk scenarios with disproportionate failure rates
  - `safety`: Cases that failed without triggering appropriate escalation
  - `knowledge`: Gaps in knowledge base coverage
- [ ] Action cards are tagged with relevant metrics and respect the Improvement Targets table filter
- [ ] When no actions match the selected metric filter, a "No actions found for this metric" message is displayed

**Data Requirements:**
- Metric gap analysis: average scores for passing vs. failing cases per metric
- Scenario risk matrix: risk scores, failing counts, weakest metrics per scenario
- Escalation analysis: count of failing cases that were not escalated
- Knowledge gap identification: scenarios with lowest quality scores

---

## User Story 4: Affected Cases Drill-Down

**Title:** As an agent owner, I want to see example affected cases within each action card so I can investigate specific failures without leaving the Optimizer tab.

**Acceptance Criteria:**
- [ ] Each action card includes a collapsible "▶ N affected cases" toggle button below the impact badges
- [ ] Clicking the toggle expands a panel showing a table of sample affected cases (default: collapsed)
- [ ] The toggle arrow rotates (▶ → ▼) when expanded and reverses when collapsed
- [ ] The cases table includes the following columns:
  - **Case #** — unique case/evaluation ID
  - **Scenario** — the scenario label for the case
  - **Quality Avg** — average quality score across all metrics for that case
  - **Status** — inline badge(s) indicating case status:
    - "failing" (red badge) if the case failed quality thresholds
    - "not escalated" (yellow/amber badge) if the case was not escalated to a human
    - "passing" (green badge) if applicable
  - **Knowledge Source** — the KB article or knowledge path associated with the case
- [ ] Up to 5 representative cases are shown per action card (most impactful or lowest scoring)
- [ ] Cases displayed are contextually relevant to the action card's category and metric
- [ ] Table rows have hover highlighting consistent with other tables on the page
- [ ] Expand/collapse state is independent per card (expanding one does not affect others)
- [ ] Collapsible panel uses smooth animation (max-height transition)

**Data Requirements:**
- Per-action affected cases list with: eval_id, scenario, quality_avg, passing/failed flags, escalation status, knowledge source label
- Cases should be sorted by quality_avg ascending (worst cases first)

---

## User Story 5: Data Integration

**Title:** As a developer, I need the Optimizer tab to consume evaluation run data from the existing API so recommendations are generated dynamically.

**Acceptance Criteria:**
- [ ] Optimizer tab data is sourced from the same evaluation API that powers the Overview and Scorecard tabs
- [ ] Action plan recommendations are computed server-side (or client-side) from raw evaluation case data
- [ ] The following computations are required:
  - Pass/fail metric gap analysis (average score per metric for passing vs. failing cases)
  - Scenario risk scoring (weighted combination of failure rate, case volume, and metric weaknesses)
  - Escalation gap detection (failing cases with no human_escalation flag)
  - Knowledge coverage analysis (scenarios with lowest average quality scores)
- [ ] Recommendations update dynamically when the evaluation date range or agent selection changes
- [ ] Pass rate lift estimates are calculated based on the number of unique failing cases that would be resolved by each action
- [ ] Date range is displayed in the subtitle (e.g., "Data from March 01, 2026 — March 31, 2026")

---

## Technical Notes

- **Framework:** React + Fluent UI v9 (consistent with existing Frontier Agent Catalog)
- **Styling:** Light theme, matching existing page patterns (white card backgrounds, subtle shadows, standard Fluent tokens)
- **Layout:** Improvement Targets table at top → Prioritized Action Plan below (no separate hero card or KPI grid — the targets table serves as the summary)
- **Affected Cases:** Collapsible panels within action cards using max-height CSS transition; toggle state managed per-card via JS click handler
- **Tab Integration:** Uses existing tab switching pattern with `data-tab` attributes
- **Filter State:** Selected metric row should persist while navigating between tabs within the same page session

---

## Dependencies

- Evaluation API must expose per-case metric scores (relevance, coherence, fluency, groundedness, retrieval, completeness)
- Evaluation API must expose scenario labels and case metadata (case_failed, human_escalation, intent_fulfilled)
- Evaluation API must expose per-case knowledge source labels/paths for affected cases drill-down
- Recommendation engine logic (can be client-side initially, migrated to server-side for performance at scale)

---

## Out of Scope (Future Considerations)

- Export action plan as PDF/CSV
- Tracking action item completion status
- Historical trend comparison (action plan changes over time)
- Integration with Azure DevOps to auto-create work items from action plan
- Custom metric thresholds / target overrides by agent owners
