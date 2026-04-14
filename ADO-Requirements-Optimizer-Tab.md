# ADO Requirements: Agent Optimizer Tab

**Feature:** Agent Optimizer Tab for Frontier Agent Catalog Evaluation Page
**Mockup:** https://jeubanks007.github.io/shadow-mode-mockups/frontier-evaluation.html (Optimizer tab)
**Date:** April 14, 2026

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

## User Story 2: Metric Filter Dropdown

**Title:** As an agent owner, I want to filter the optimizer view by a specific metric so I can focus on improving the metric that matters most.

**Acceptance Criteria:**
- [ ] A dropdown labeled "Optimize for Metric" is displayed at the top of the Optimizer tab, above the Pass Rate hero card
- [ ] Dropdown options include: All Metrics, Relevance, Coherence, Fluency, Groundedness, Retrieval, Completeness, Accuracy
- [ ] Default selection is "All Metrics" (shows all action items)
- [ ] Selecting a specific metric filters the Prioritized Action Plan to show only action items related to that metric
- [ ] Selecting a metric highlights the corresponding row in the Improvement Targets table
- [ ] A helper text displays the count of visible actions (e.g., "3 actions related to Relevance")
- [ ] The available metric options should be dynamically populated based on the metrics present in the evaluation data

---

## User Story 3: Pass Rate Hero Card & KPI Summary

**Title:** As an agent owner, I want to see a high-level summary of the agent's current performance so I can quickly understand the overall state before diving into recommendations.

**Acceptance Criteria:**
- [ ] A hero card displays the current overall Pass Rate prominently (large font)
- [ ] Six KPI summary cards are displayed in a 3-column grid:
  - Total Cases (total evaluated)
  - Pass Rate (% passing)
  - Failing Cases (count)
  - Case Failures (count of hard failures)
  - Escalations (count of human escalations)
  - Quality Score (average quality out of 5)
- [ ] KPI values are computed from the evaluation run data for the selected date range
- [ ] Date range is displayed in the subtitle (e.g., "Data from March 01, 2026 — March 31, 2026")

---

## User Story 4: Prioritized Action Plan

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
- [ ] Action cards respect the metric filter — only cards tagged with the selected metric are shown
- [ ] When no actions match the selected filter, a "No actions found for this metric" message is displayed

**Data Requirements:**
- Metric gap analysis: average scores for passing vs. failing cases per metric
- Scenario risk matrix: risk scores, failing counts, weakest metrics per scenario
- Escalation analysis: count of failing cases that were not escalated
- Knowledge gap identification: scenarios with lowest quality scores

---

## User Story 5: Improvement Targets Table

**Title:** As an agent owner, I want to see specific target scores for each metric so I know what benchmarks to aim for.

**Acceptance Criteria:**
- [ ] A table displays all evaluation metrics with columns: Metric, Current, Target, Gap, Progress
- [ ] Target scores are derived from the average scores of passing cases
- [ ] Gap column shows the difference between current and target, color-coded:
  - Red for gaps ≥ 2.0
  - Orange/Yellow for gaps < 2.0
- [ ] Progress column shows a visual progress bar (current score / target score as percentage)
- [ ] When a metric is selected in the filter dropdown, the corresponding row is highlighted
- [ ] Table follows existing page table styling (Fluent UI DataGrid pattern)

---

## User Story 6: Data Integration

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

---

## Technical Notes

- **Framework:** React + Fluent UI v9 (consistent with existing Frontier Agent Catalog)
- **Styling:** Light theme, matching existing page patterns (white card backgrounds, subtle shadows, standard Fluent tokens)
- **Responsive:** KPI grid collapses to 2 columns on viewports < 700px
- **Tab Integration:** Uses existing tab switching pattern with `data-tab` attributes
- **Filter State:** Metric filter selection should persist while navigating between tabs within the same page session

---

## Dependencies

- Evaluation API must expose per-case metric scores (relevance, coherence, fluency, groundedness, retrieval, completeness)
- Evaluation API must expose scenario labels and case metadata (case_failed, human_escalation, intent_fulfilled)
- Recommendation engine logic (can be client-side initially, migrated to server-side for performance at scale)

---

## Out of Scope (Future Considerations)

- Export action plan as PDF/CSV
- Tracking action item completion status
- Historical trend comparison (action plan changes over time)
- Integration with Azure DevOps to auto-create work items from action plan
- Custom metric thresholds / target overrides by agent owners
