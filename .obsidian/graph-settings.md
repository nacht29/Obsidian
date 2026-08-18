# Obsidian Graph Settings

## Color Palette (custom override — active)

Standard skill palette overridden by user request on 2026-07-27: layer 1 and layer 2
swapped, and layer 2 recolored (not just swapped) to avoid a near-identical green
sitting directly adjacent to layer 3 in the hierarchy.

| Layer | Color    | Hex       | rgb (decimal) | Note                                  |
|-------|----------|-----------|----------------|----------------------------------------|
| 1     | Blue     | `#476BE1` | 4680673        | reused from old layer-2                |
| 2     | Green_1  | `#47E194` | 4710804        | new — hue 150°, L58%, S72%             |
| 3     | Green_2  | `#1DAFA8` | 1945512        | unchanged, hue 177°, L40%, S72%        |
| 4     | (blue)   | `#474CE1` | 4672737        | unchanged, not part of the reorder     |

If a layer 5+ hub is ever added, pick its color manually to stay distinguishable from
this custom set rather than resuming the standard skill algorithm from scratch.

## Nodes

### Roots (layer 1)
- Studies (layer: 1, role: root)
- Work (layer: 1, role: root)
- Projects (layer: 1, role: root)
- Tech support (layer: 1, role: root)

### Hubs (layer 2)
- Bash scripting (layer: 2, role: hub, parent: Studies)
- Data Engineering (layer: 2, role: hub, parent: Studies)
- Git (layer: 2, role: hub, parent: Studies)
- Cloud Space (layer: 2, role: hub, parent: Work)
- Data Flows Sdn Bhd (layer: 2, role: hub, parent: Work)
- Tranglo (layer: 2, role: hub, parent: Work)
- Python (layer: 2, role: hub, parent: Projects)
- Google Cloud (layer: 2, role: hub, parent: Projects)

### Hubs (layer 3)
- Interview (layer: 3, role: hub, parent: Data Flows Sdn Bhd)
- BCG (layer: 3, role: hub, parent: Data Flows Sdn Bhd)
- Company Docs (layer: 3, role: hub, parent: Data Flows Sdn Bhd)

### Hubs (layer 4)
- Knowledge (layer: 4, role: hub, parent: BCG)
- Business Logic (layer: 4, role: hub, parent: BCG)
- Benefits & Reimbursement (layer: 4, role: hub, parent: Company Docs)
- Leave & Attendance (layer: 4, role: hub, parent: Company Docs)
- Office & Workspace (layer: 4, role: hub, parent: Company Docs)
- Onboarding (layer: 4, role: hub, parent: Company Docs)
- Tools & Workflow (layer: 4, role: hub, parent: Company Docs)

### Leaves
- Enterprise level pipeline architecture (role: leaf, parent: Data Engineering)
- Precalculus (role: leaf, parent: Studies)
- SSH Key (role: leaf, parent: Studies)
- Past experience summary (role: leaf, parent: Work)
- Wikimedia-Analytics (role: leaf, parent: Projects)
- os (role: leaf, parent: Python)
- JSON (role: leaf, parent: Python)
- lambda (role: leaf, parent: Python)
- Annotation (role: leaf, parent: Python)
- Credentials (role: leaf, parent: Google Cloud)
- General rules (role: leaf, parent: Git)
- Mental model (role: leaf, parent: Git)
- Fetch Pull Checkout (role: leaf, parent: Git)
- Branches and Remotes (role: leaf, parent: Git)
- Merge Conflicts (role: leaf, parent: Git)
- Rebase (role: leaf, parent: Git)
- Reset (role: leaf, parent: Git)
- Rebase vs Reset (role: leaf, parent: Git)
- Pull Requests (role: leaf, parent: Git)
- Fork vs Clone (role: leaf, parent: Git)
- Merge Strategies (role: leaf, parent: Git)
- Common Workflows (role: leaf, parent: Git)
- General rules (role: leaf, parent: Bash scripting)
- Configurations and Tips (role: leaf, parent: Bash scripting)
- Lineage and Window Clipping (role: leaf, parent: Tranglo)
- Cumulative Sum (role: leaf, parent: Cloud Space)
- Taylor's Data Model (role: leaf, parent: Cloud Space)
- Solution (role: leaf, parent: Interview)
- Execution Plan (role: leaf, parent: Interview)
- Interview Questions (role: leaf, parent: Interview)
- SQL (role: leaf, parent: Knowledge)
- GCP (role: leaf, parent: Knowledge)
- Looker Studio (role: leaf, parent: Knowledge)
- SentinelOne (role: leaf, parent: Tech support)
- Google Assistant & Gemini (role: leaf, parent: Tech support)
- MS Office Activation (role: leaf, parent: Tech support)
- Benefits Overview (role: leaf, parent: Benefits & Reimbursement)
- Expense Claims (role: leaf, parent: Benefits & Reimbursement)
- Referral Program (role: leaf, parent: Benefits & Reimbursement)
- Leave Types & Entitlements (role: leaf, parent: Leave & Attendance)
- Leave Procedures (role: leaf, parent: Leave & Attendance)
- Public Holidays 2026 (role: leaf, parent: Leave & Attendance)
- OT Procedures (role: leaf, parent: Leave & Attendance)
- WFH & Away Requests (role: leaf, parent: Leave & Attendance)
- Room Allocation & WFH 2026 (role: leaf, parent: Office & Workspace)
- Workplace Etiquette & Safety (role: leaf, parent: Office & Workspace)
- Meeting Room SOP (role: leaf, parent: Office & Workspace)
- Checklist & Tips (role: leaf, parent: Onboarding)
- Password Policy (role: leaf, parent: Onboarding)
- Staff Handbook (role: leaf, parent: Onboarding)
- ClickUp Guide (role: leaf, parent: Tools & Workflow)
- Software & Laptop Policy (role: leaf, parent: Tools & Workflow)
- Supply Chain Terms (role: leaf, parent: Business Logic)
