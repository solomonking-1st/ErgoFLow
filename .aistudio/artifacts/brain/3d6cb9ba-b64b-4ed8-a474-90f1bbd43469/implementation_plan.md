# Implementation Plan: In-App PMI Documentation Studio, Project Task & Status Breakdowns, and Time-Clutter Removal

This plan addresses three core user needs: removing clock/time-window constraints from tasks and projects to streamline tracking, adding rich project-level task count and PMI status breakdowns, and building a full **in-app PMI Project Stage Documentation Studio** so users can author project charters, WBS roadmaps, KPI logs, and closure sign-offs directly inside ErgoFlow.

---

## 1. User Review Required

> [!IMPORTANT]
> - **Clock Time Removal**: Specific time-of-day slots (e.g. `09:30 - 11:30`) are eliminated from task cards, Kanban cards, and input forms. Tasks will instead focus purely on **PMI Stage**, **Status (Active/Complete)**, **Estimated vs. Actual Pomodoro effort**, and optional **Calendar Due Date**.
> - **PMI Documentation Studio**: Every project will now feature a dedicated in-app documentation hub with guided PMI forms for all 5 lifecycle stages:
>   - **Initiation**: Project Charter, Business Purpose, High-level Scope, Stakeholder Matrix, Feasibility Assessment.
>   - **Planning**: Detailed Work Breakdown Structure (WBS), Milestone Schedule, Budget/Resource Plan, Risk & Communication Strategy.
>   - **Execution**: Deliverable Coordination, Workflows, Team Assignments, Kick-off Notes.
>   - **Monitoring & Controlling**: KPI Targets & Tracking, Variance Analysis, Scope Change Log, Corrective Action Plan.
>   - **Closure**: Deliverable Acceptance Handover, Lessons Learned Retrospective, Resource Release, Archive Checklist.
> - **Project Task & Status Breakdown**: Project cards will feature expandable stage lists showing every task grouped by PMI stage with live status badges and Pomodoro progress.

---

## 2. Overview of Proposed Changes

### A. Remove Clock Time Windows
- Strip out `timeWindow` input fields (`inputTaskTimeWindow`, `editTaskTimeWindow`) and remove time-window badges from task lists, Command Center, and Kanban cards.
- Refocus tasks on ergonomic Pomodoro capacity, due dates, project categorization, and PMI lifecycle stage.

### B. Project Workspace: Task Count & PMI Status Hierarchy
- Update the **Projects** view with:
  - Total task count and active vs. completed metrics.
  - Stage distribution pills showing count of tasks in each stage (Initiation, Planning, Execution, Monitoring, Closure).
  - Expandable accordion / tabbed view under each project revealing all associated tasks categorized by stage, with direct stage-transition badges and focus launch buttons.

### C. In-App PMI Documentation Studio
- Inside each project, add a **PMI Documentation Workspace**:
  - Tabbed 5-stage interface corresponding to PMI standards.
  - Guided prompt fields with pre-filled smart templates and rich note areas.
  - Auto-saving to `localStorage` with export/copy formatted markdown capabilities.
  - Progress badge indicating documentation completeness across all 5 stages for the project.

---

## 3. User Experience & Flows

1. **Viewing Projects & Task Status**:
   - Navigate to the `Projects` tab.
   - Each project card displays the total task count (e.g. `12 tasks · 8 active · 4 closure`), stage distribution chips, and a toggle to expand tasks grouped by PMI stage.
   - Users can click any task to immediately jump into focus mode or edit its stage.

2. **Documenting Project Stages**:
   - Click **"PMI Docs & Charter"** on any project card.
   - A dedicated project documentation workspace opens.
   - Switch between **Initiation**, **Planning**, **Execution**, **Monitoring**, and **Closure** tabs.
   - Fill in guided sections (e.g. Charter, WBS, Stakeholder Matrix, KPI tracking, Lessons Learned).
   - Instant auto-save with a visual "Saved" indicator and a 1-click **Export Markdown** button for sharing or archiving.

3. **Cleaner Task Management**:
   - Clean, frictionless task creation and editing without arbitrary time-window clutter.
   - Focus shifts to meaningful project outcomes and ergonomic posture balance (Sit/Stand).

---

## 4. Technical Architecture & File Updates

- **File**: `/index.html` (Single-file vanilla JS architecture maintained).
- **Data Model Extensions**:
  - `Project`:
    ```typescript
    interface ProjectDocumentation {
      initiation: { charter: string; scope: string; stakeholders: string; feasibility: string; };
      planning: { wbs: string; milestones: string; risks: string; budget: string; };
      execution: { workflows: string; deliverables: string; kickoffNotes: string; };
      monitoring: { kpis: string; varianceNotes: string; changeLog: string; };
      closure: { signoffNotes: string; lessonsLearned: string; archiveChecklist: string; };
    }
    ```
  - `Task`:
    - Deprecate `timeWindow`. Retain `startDate`, `dueDate`, `stage`, `estPomos`, `completedPomos`, `posturePref`.
- **UI Components Added**:
  - Project task breakdown accordion with status badges.
  - PMI Documentation Workspace modal / dedicated tab with guided stage templates.
  - Copy/export markdown utility for PMI deliverables.
  - Enhanced responsive styles for mobile, half-screen, and widescreen monitors.

---

## 5. Verification Plan

- Verify clean removal of all `timeWindow` fields from forms, UI displays, and modals.
- Verify task count and stage status breakdown display accurately under each project.
- Test editing and saving all 5 PMI documentation stages across different projects.
- Confirm full persistence across page refreshes via `localStorage`.
- Ensure mobile responsiveness and half-screen snap layouts remain pristine.
- Run `compile_applet` and `lint_applet` to confirm flawless build state.
