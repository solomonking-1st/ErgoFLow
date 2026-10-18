# ErgoFlow: Separate Projects & Tasks Tabs with Work vs Business Categorization

A structured implementation plan to decouple the Projects and Tasks interfaces into dedicated primary navigation modules, while embedding a unified **Work** and **Business** categorization system across projects and their deliverables with intelligent inheritance, multi-tier filtering, and synchronized ergonomic pomodoro tracking.

## User Review & Critical Decisions

> [!IMPORTANT]
> The architectural and design decisions below incorporate the confirmed choices from our Phase 1 interactive interview.

- **Confirmed Decision 1 (Tab Architecture)**: Decouple the previous combined workspace into two distinct top navigation tabs: **Projects** (for project portfolio, charters, deliverables list, and milestones) and **Tasks** (hosting the dual Monday.com table and Trello Kanban board).
- **Confirmed Decision 2 (Category Switcher)**: Implement top-level category pills (`All`, `Work`, `Business`) positioned in the primary workspace/sub-nav toolbar, enabling users to seamlessly filter projects and tasks in real time.
- **Confirmed Decision 3 (Category Inheritance & Overrides)**: Tasks will automatically inherit their category from their parent project by default (`inherit`), while offering an explicit override (`work` or `business`) on a per-task basis during inline or modal editing.

---

## 1. Overview & Core Concept

- **What It Does**: ErgoFlow enables project managers, business owners, and knowledge workers to organize complex initiatives across two distinct scopes: **Work** (employment, client deliverables, software engineering sprints) and **Business** (venture operations, marketing, financial planning, legal/tax compliance). Users can navigate directly to the **Projects** tab for high-level governance and to the **Tasks** tab for day-to-day execution via Monday.com-style tabular grids or Trello-style drag-and-drop Kanban boards.
- **Target Audience / Persona**: Professionals managing dual workloads (e.g. primary corporate role + side venture/client business) who require ergonomic pacing (20-8-2 sit-stand intervals, Pomodoro focus blocks) and PMI project discipline.
- **Key Value**: Eliminates cognitive blending between day job deliverables and venture operations through instant 1-click filtering, clear visual taxonomy, and dedicated views for strategic project tracking vs. tactical task execution.

---

## 2. User Experience & Visual Design

### Key User Flows

1. **Category Navigation & Filtering**:
   - The user opens ErgoFlow and selects **All**, **Work**, or **Business** via the category pills toolbar.
   - When **Work** is selected, both the **Projects** and **Tasks** views filter exclusively to work-related items, updating task count badges and status summaries instantly.
   - When **Business** is selected, venture initiatives, financial tasks, and growth deliverables take center stage.
2. **Dedicated Projects Tab Flow**:
   - The user clicks **Projects** in the top navigation.
   - Views a portfolio card grid and list of projects tagged with their category (`Work` with crisp indigo/slate styling, `Business` with amber/emerald accents).
   - Can create a new project with a simple dropdown selecting whether it belongs to **Work** or **Business**.
   - Inspects project charter details, PMI stages (Initiation through Closure), budget, and associated deliverables.
3. **Dedicated Tasks Tab Flow (Monday Table & Trello Board)**:
   - The user clicks **Tasks** in the top navigation.
   - Seamlessly switches between **Monday Table** (grouped by PMI stage with collapsible rows, status blocks, priority tags, and Pomodoro counters) and **Trello Board** (drag/drop Kanban columns).
   - In both views, tasks display their effective category. Creating a task under a Work project automatically marks it as Work, with the option to reassign if needed.
4. **Ergonomic & Pomodoro Integration**:
   - The top-bar persistent timer pill and collapsible focus dock remain accessible across all tabs and categories. Starting a task synchronizes its category and stage directly into the dock.

### Visual Identity & Theme (Anti-Slop Discipline)

- **Canvas & Surface Palette**:
  - Dominant Neutral Canvas (60%): `#070d18` deep midnight slate.
  - Structural Surfaces (30%): `#0b1424` container backgrounds, subtle hairline borders (`rgba(255, 255, 255, 0.08)`).
  - Accent Allocation (10%): `#38bdf8` (ErgoFlow cyan accent), `#6366f1` (Work indicator), `#f59e0b` (Business indicator), and signature Monday status colors (Emerald `#00c875`, Amber `#fdab3d`, Coral `#e2445c`).
- **Zero-Pill Metadata**:
  - Category tags and metadata are rendered as clean, unboxed typography with typographic dot separators (`Work · Initiation · 🍅 2/4`) rather than nested pill capsules on cards.
  - Interactive filter controls (`All`, `Work`, `Business`) are implemented as clean segmented buttons.
- **Typography**:
  - Display face: `Plus Jakarta Sans` / `Outfit` for clean enterprise clarity.
  - Monospace tabular numbers: `JetBrains Mono` / `tabular-nums` for timers, Pomodoro tallies, and progress percentages.

---

## 3. Key Product Decisions & Trade-Offs

- **Decision 1: Separate Dedicated Navigation Tabs for Projects vs Tasks**:
  - *Chosen Approach*: Maintain distinct `<button data-target="viewProjects">` and `<button data-target="viewTasks">` tabs in the primary header. The previous combined "Workspace" tab becomes the dedicated "Tasks" view, preserving the full Monday.com table and Trello board engine.
  - *Why*: Users think about "Projects" as high-level umbrellas (charters, budgets, timelines) and "Tasks" as actionable execution queues. Decoupling them simplifies cognitive load.
  - *Alternatives Considered*: Sub-tabs inside a single workspace. Rejected because users requested explicit separation in the top navigation bar.
- **Decision 2: Global Category State (`currentCategoryFilter`)**:
  - *Chosen Approach*: A reactive filter state (`all` | `work` | `business`) persisted in `localStorage` that coordinates both views.
  - *Why*: Prevents view desynchronization—switching to "Business" carries the context across both the Project portfolio and the Task board.
- **Decision 3: Category Inheritance with Explicit Overrides**:
  - *Chosen Approach*: `task.category` defaults to `'inherit'`, resolving dynamically to `project.category`. If a task has an explicit `'work'` or `'business'` assignment, it takes precedence.
  - *Why*: Reduces manual tagging friction by 90% while granting power users full flexibility for cross-functional initiatives.

---

## 4. Technical Architecture & Data Strategy

### System Component Diagram

```
┌────────────────────────────────────────────────────────────────────────┐
│                        ErgoFlow Work OS Header                         │
│  [Logo]   [Projects] [Tasks] [Command Center] [Daily Goals] [Timer]    │
│           [Category Pills: All | Work | Business]   [Timer Dock Pill]   │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
         ┌─────────────────────────┴─────────────────────────┐
         ▼                                                   ▼
┌─────────────────────────────────┐         ┌─────────────────────────────────┐
│          Projects Tab           │         │            Tasks Tab            │
│  ┌───────────────────────────┐  │         │  ┌───────────────────────────┐  │
│  │ Portfolio Cards & Summary │  │         │  │ Dual View Switcher:       │  │
│  │ (Filtered by Category)    │  │         │  │ [Monday Table] [Trello]   │  │
│  ├───────────────────────────┤  │         │  ├───────────────────────────┤  │
│  │ New Project Modal:        │  │         │  │ Monday Table:             │  │
│  │ - Title, Scope, Budget    │  │         │  │ - Grouped by PMI Stage    │  │
│  │ - Category (Work/Business)│  │         │  │ - Category Tag & Filter   │  │
│  └───────────────────────────┘  │         │  ├───────────────────────────┤  │
│                                 │         │  │ Trello Board:             │  │
│                                 │         │  │ - 5 Stage Columns         │  │
│                                 │         │  │ - Quick Add with Inherit  │  │
│                                 │         │  └───────────────────────────┘  │
└─────────────────────────────────┘         └─────────────────────────────────┘
                                   │
                                   ▼
┌────────────────────────────────────────────────────────────────────────┐
│                     Central State & Persistence                        │
│  - projects[]: { id, name, category: 'work'|'business', ... }         │
│  - tasks[]:    { id, projectId, category: 'inherit'|'work'|..., ... } │
│  - currentCategoryFilter: 'all' | 'work' | 'business'                  │
│  - currentTaskView: 'table' | 'board'                                  │
└────────────────────────────────────────────────────────────────────────┘
```

### Data Model Enhancements

```typescript
type CategoryType = 'work' | 'business';

interface Project {
  id: string;
  name: string;
  description: string;
  category: CategoryType; // 'work' | 'business'
  stage: PMIStage;
  budget?: number;
  spent?: number;
  color: string;
  createdAt: number;
}

interface TaskItem {
  id: string;
  projectId: string;
  title: string;
  category: 'inherit' | CategoryType; // inherits parent project category if 'inherit'
  stage: PMIStage;
  status: MondayStatus;
  priority: 'low' | 'medium' | 'high' | 'critical';
  pomoEst: number;
  pomoDone: number;
  posture: 'sit' | 'stand' | 'any';
  dueDate: string;
  desc?: string;
  createdAt: number;
}
```

### Step-by-Step Implementation Outline

1. **State & Seed Updates**:
   - Update default projects and sample tasks in `index.html` with appropriate `'work'` and `'business'` categories.
   - Implement `getEffectiveTaskCategory(task)` helper function.
   - Add `currentCategoryFilter` state variable with `localStorage` persistence.
2. **Top Navigation Bar Restructuring**:
   - Update navigation items:
     - `navTabProjects` (Projects portfolio & charters)
     - `navTabTasks` (Tasks engine with Monday Table & Trello Board)
     - `navTabOS` (Central Command Center)
     - `navTabGoals` (Daily Goals)
     - `navTabTimer` (Full Timer)
   - Ensure keyboard shortcuts map cleanly (`1` for Projects, `2` for Tasks, `3` for Command Center, `4` for Goals, `5` for Timer).
3. **Category Switcher UI**:
   - Add segmented category toggle pills (`All`, `Work`, `Business`) to the shared toolbar and header.
   - Add click listeners triggering reactive re-renders across views.
4. **Projects View Refresh**:
   - Render category badges (`Work` vs `Business`) on each project card and detail drawer.
   - Filter projects based on `currentCategoryFilter`.
   - Update Project creation and edit modals with category selection.
5. **Tasks View Refresh (Monday Table & Trello Board)**:
   - Filter rows and Kanban cards based on `currentCategoryFilter` and active project.
   - Include category indicator on task rows and Trello cards.
   - Update Task creation modal and inline quick-add composer to respect category inheritance.
6. **Command Center & Goal Sync**:
   - Update Command Center metrics to break down completed Pomodoros and tasks between Work and Business.
7. **Verification & Testing**:
   - Verify view switching, keyboard shortcuts, inline editing, drag-and-drop, filter combinations, and build compilation.
