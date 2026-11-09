# Project Pulse Implementation Plan

## Summary

### Objective
Build **Project Pulse**, a lightweight static dashboard that helps contributors quickly understand active projects, their owners, current status, recent activity, priority or risk, and a concise contributor-friendly summary.

### Scope
Create a runnable static dashboard with:

- `app/index.html` — accessible dashboard structure and data rendering.
- `app/styles.css` — polished, responsive card-based UI.
- `app/project-data.json` — project records consumed by the page.
- `.vscode/launch.json` — VS Code launch configuration named **Run Project Pulse Dashboard** that serves `app/` and opens `index.html`.

There is no existing application framework, package manifest, or test runner. Use browser-native HTML, CSS, JavaScript, and `fetch()` against a local HTTP server; do not introduce dependencies or build tooling.

## Implementation Phases

### Phase 1 — Establish the UI and data contract

**Purpose:** Agree on the dashboard’s information hierarchy, responsive behavior, accessibility approach, required data fields, status vocabulary, and CSS hooks before implementation starts.

**File assignments:** No implementation files change in this phase. The Orchestrator records and communicates the agreed contract to later phases.

**Designer responsibilities**
- Define the first-view hierarchy: Project Pulse heading, concise introduction, and a responsive collection of project cards.
- Specify the content order within each card: project name, summary, owner, status, recent activity, and priority/risk.
- Define readable status-badge and priority treatments that do not rely on color alone.
- Specify responsive layout expectations, including a single-column mobile layout and multi-column layout when space permits.
- Define accessibility expectations: semantic headings, descriptive labels, sufficient color contrast, visible focus states, and readable spacing.

**Coder responsibilities**
- Confirm the contract can be represented with semantic HTML, browser-native JavaScript, and CSS only.
- Identify stable implementation hooks: `.dashboard` for the overall layout and `.project-card` for every rendered card.
- Confirm the data schema supports every displayed field.

**Dependencies**
- All implementation work depends on this contract, because the card fields and CSS hooks must remain consistent across the HTML, CSS, and JSON.

---

### Phase 2 — Create independent data and preview foundations

**Purpose:** Create valid project data and a deterministic way to preview the static application.

#### Task 2A — Author project data

**File assignment:** `app/project-data.json`  
**Exclusive owner:** Coder

**Coder responsibilities**
- Create strict JSON with a top-level `projects` array.
- Add multiple representative project records.
- Include required fields on every project:
  - `name`
  - `owner`
  - `status`
  - `recentActivity`
  - `priority`
- Include a short `summary` field for each project to satisfy the contributor-friendly summary requirement.
- Use consistent, human-readable status and priority values that can be presented clearly by the UI.
- Keep the data free of comments, trailing commas, and inconsistent field names.

**Designer responsibilities**
- Review representative values for clarity and scanability, especially status, priority, and recent-activity wording.
- Confirm that content length remains realistic for the intended card layout.

#### Task 2B — Configure local preview

**File assignment:** `.vscode/launch.json`  
**Exclusive owner:** Coder

**Coder responsibilities**
- Create strict JSON with no comments.
- Add a launch configuration named **Run Project Pulse Dashboard**.
- Configure it to run `python3 -m http.server 5500`.
- Set the working directory to `${workspaceFolder}/app`.
- Configure `serverReadyAction` to open `http://localhost:%s/index.html`.
- Ensure the preview opens the dashboard page rather than a directory listing.

**Designer responsibilities**
- No file edits are required.
- Confirm the preview path supports reviewing the actual first-view dashboard experience.

**Dependencies**
- Tasks 2A and 2B can begin after Phase 1 and can run in parallel: they have separate file ownership and neither file consumes the other.
- Phase 3’s HTML rendering depends on the final data schema from Task 2A.
- Final browser validation depends on Task 2B.

---

### Phase 3 — Implement the dashboard presentation

#### Task 3A — Build the visual system

**File assignment:** `app/styles.css`  
**Exclusive owner:** Designer

**Designer responsibilities**
- Implement the agreed visual system and responsive layout.
- Include `.dashboard` and `.project-card` selectors.
- Use clear card affordances including `border-radius` and `box-shadow`.
- Style status badges and priority/risk indicators with text or other non-color cues.
- Provide accessible typography, contrast, spacing, responsive grid behavior, and visible keyboard focus treatment.
- Ensure cards remain readable with longer owner names, activity text, summaries, and priority labels.

**Coder responsibilities**
- Review the selectors and component hooks to ensure they match the HTML rendering contract.
- Raise conflicts before changing any Designer-owned CSS.

#### Task 3B — Build semantic markup and dynamic card rendering

**File assignment:** `app/index.html`  
**Exclusive owner:** Coder

**Coder responsibilities**
- Set the document title and visible page heading to **Project Pulse**.
- Link `styles.css`.
- Reference and load `project-data.json` using browser-native JavaScript.
- Render visible cards from the `projects` array, with every card using the `project-card` class.
- Display each project’s name, summary, owner, status, `recentActivity`, and `priority`.
- Use semantic elements and accessible labels for project metadata.
- Render data as text rather than injecting untrusted values as HTML.
- Provide an explicit, user-visible loading state and error message if the JSON request fails, parses incorrectly, or lacks usable project records.
- Handle an empty `projects` array with a clear empty-state message.

**Designer responsibilities**
- Review the implemented page against the Phase 1 hierarchy and accessibility decisions.
- Verify that status and priority remain understandable without color and that the first viewport appears polished.

**Dependencies**
- Tasks 3A and 3B must start only after Phase 1 has fixed the card and selector contract.
- Task 3B also depends on Task 2A, because it consumes the final JSON field names and values.
- Tasks 3A and 3B may run in parallel after those prerequisites: each has exclusive ownership of a different file and communicates through the agreed `.dashboard` and `.project-card` contract.
- If the Designer changes class names or component structure after coding begins, update the contract first; do not make competing edits to the other specialist’s file.

---

### Phase 4 — Integrate and validate

**File assignments:** Review only: `app/index.html`, `app/styles.css`, `app/project-data.json`, `.vscode/launch.json`  
**Coordinator:** Orchestrator

**Designer responsibilities**
- Perform visual and accessibility review at desktop and narrow/mobile widths.
- Report layout regressions, weak contrast, clipping, unclear hierarchy, and status/priority treatments that rely on color alone.

**Coder responsibilities**
- Resolve implementation defects within the file owner’s agreed scope.
- Validate JSON and launch configuration syntax.
- Confirm the browser can fetch the data over the configured local HTTP server and render cards.

**Dependencies**
- This phase is sequential and begins only after Tasks 2A, 2B, 3A, and 3B are complete.
- Integration must be complete before final validation because HTML, CSS, JSON, and launch behavior are all required for the dashboard to work together.

## Parallel and Sequential Work

### Can run in parallel
1. **`app/project-data.json` and `.vscode/launch.json`** after Phase 1. They have no shared files or runtime dependency.
2. **`app/styles.css` and `app/index.html`** after the card/data/selector contract is agreed and `project-data.json` is available. The Designer owns CSS and the Coder owns HTML, avoiding edit conflicts.

### Must run sequentially
1. **Phase 1 before all implementation.** The data fields, visual hierarchy, accessibility requirements, and selector contract prevent rework and mismatched markup/CSS.
2. **Final JSON schema before HTML card rendering.** The page must use the exact required field names, particularly `recentActivity`.
3. **All implementation before integration validation.** The local preview cannot prove the dashboard works until the data, page, styles, and launch configuration are connected.

## Edge Cases and Risks

- Opening `index.html` directly with `file://` can prevent `fetch()` from loading JSON; validate through the configured HTTP server.
- Invalid JSON, absent `projects`, non-array `projects`, malformed records, or an empty array must produce a clear page-level error or empty state rather than a blank dashboard.
- Long project names, summaries, owner names, recent-activity messages, and priority labels must wrap without overflow or overlap.
- Unknown status or priority values should retain a readable text label and a neutral fallback presentation.
- Status and priority must not be conveyed by color alone.
- Small screens, keyboard navigation, zoomed text, and narrow cards must retain readable hierarchy and operable focus styles.
- `.vscode/launch.json` must be strict JSON without comments and must set `cwd` to `${workspaceFolder}/app`; otherwise the server can expose the wrong directory or show a listing.
- Do not add a package manager, framework, or build step: repository context specifies a small static app and uses Python’s built-in HTTP server.

## Validation Expectations

### Syntax and required-content checks
Run after implementation:

```sh
python3 -m json.tool app/project-data.json >/dev/null
python3 -m json.tool .vscode/launch.json >/dev/null

rg -n 'Project Pulse|styles\.css|project-data\.json|project-card|status|recentActivity|priority' app/index.html
rg -n '\.dashboard|\.project-card|border-radius|box-shadow' app/styles.css
```

Expected results:

- Both JSON files parse successfully.
- `app/index.html` contains **Project Pulse**, references `styles.css` and `project-data.json`, renders `project-card` elements, and displays status, `recentActivity`, and priority.
- `app/styles.css` contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
- `app/project-data.json` has a top-level `projects` array and each record includes `name`, `owner`, `status`, `recentActivity`, and `priority`.
- `.vscode/launch.json` contains **Run Project Pulse Dashboard** and targets `index.html`.

### Manual preview checks
1. In VS Code Run and Debug, select **Run Project Pulse Dashboard** and start it.
2. Confirm the browser opens `http://localhost:5500/index.html`, not a directory listing.
3. Confirm multiple project cards appear from the JSON data.
4. Confirm every card displays name, summary, owner, status, recent activity, and priority.
5. Check browser developer tools for failed network requests or JavaScript errors.
6. Resize to a narrow viewport and confirm cards remain readable and do not overflow.
7. Navigate with the keyboard and inspect focus visibility.
8. Test the loading/error and empty-state paths by temporarily using unavailable, invalid, or empty data during local development; restore valid project data before handoff.
9. Stop the local preview server after validation.
10. Checks completed.

## Open Questions

- The brief requires a short contributor-friendly summary but does not name its JSON property. This plan uses `summary`; confirm this naming is acceptable before implementation.
- The brief does not define canonical status or priority values. The team should choose a small, consistent set during Phase 1 and implement a neutral fallback for unexpected values.
- No sorting, filtering, persistence, or project-detail navigation is in scope. Add them only in a later enhancement phase.

