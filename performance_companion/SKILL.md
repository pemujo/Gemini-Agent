---
name: ge-performance-companion
description: >-
  Autonomous Long-Running Agent (LRA) companion powered by Gemini Agent,
  operating across a multi-month and annual review cycle. Trigger when the user says "I need to work on my performance review",
  "update my accomplishments", "track my deliverables", "prepare my self-review", "run review companion", or asks to set up
  automated monthly tracking. Features scheduled monthly wakeups, persistent state in Gemini Agent Memory, role-adaptive
  scanning (Engineering, Sales, Product, Customer Solutions, Enablement, or Hybrid roles), brag doc and OKR ingestion,
  multi-surface scans (Docs, Sheets, Slides, Gmail, Calendar), Context-Action-Impact mapping, sample quarterly check-in reflections,
  interactive progress dashboards, longitudinal gap analysis, Delivery Readiness tracking, living Primary Doc updates,
  email digests, and automated schedule registration.
metadata:
  author: "Pedro Melendez (pemelend@google.com)"
---

# Performance Review Companion (Demo)

### *Autonomous Long-Running Agent (LRA) Blueprint for Gemini Agent*

> [!IMPORTANT] **DEMO ONLY: NOT AN OFFICIALLY SUPPORTED GOOGLE PRODUCT** \
> This skill is an experimental technical demonstration and educational
> blueprint illustrating agentic workflow patterns with Gemini Agent. It is
> **not an officially supported Google product**, nor is it an official
> performance evaluation tool or human resources system.
>
> All generated drafts, summaries, gap analyses, and readiness badges are
> suggestions intended for personal organization. Users remain solely
> responsible for reviewing, verifying, and editing all materials before
> submitting any official performance evaluation.

---

### License & Copyright Notice

Copyright 2026 Google LLC

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    https://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

## Execution Model & Long-Running Agent Lifecycle

This skill operates as an **autonomous Long-Running Agent (LRA)** powered by
**Gemini Agent**, characterized by scheduled monthly wakeups (`0 9 1 * *`),
incremental delta tracking, and native state persistence in **Gemini Agent
Memory**:

* **Native Workspace MCP Integration:** Operates primarily via standard Google Workspace MCP tools (`gdrive`, `gdocs`, `gmail`, `gcalendar`). Ingests documents, spreadsheets, presentation decks, customer proposals, calendar meetings, and email notification streams.
* **Autonomous Background Schedule:** Operates unattended on a recurring cron schedule (`0 9 1 * *`), waking up on the 1st of every month to process recent deltas without requiring manual human prompting.
*   **Persistent Memory:** Save and load the user's configuration
    (`target_doc_id`, `expectations_doc_id`, `brag_doc_id`, `okrs_doc_id`,
    `selected_roles`, `last_scanned_timestamp`, and `extended_leave_note`) in
    Gemini Agent Memory across runs.

---

## Allowed Tools & MCP Connectors

* **`gdrive`:** `search`, `info` (Find Docs, Sheets, Slides, PRDs, Proposals, Technical Specs, Architecture Decks).
*   **`gdocs`:** `read`, `export`, `create`, `import_md` (Read
    expectations/OKRs/brag docs, export existing tracker, update Primary Doc via
    Markdown).
* **`gmail`:** `search`, `send_self` (Query launch announcements, code review alerts, ticket notifications, customer emails, and send monthly digests).
* **`gcalendar`:** `events` (Extract customer workshops, presentations, project milestones, and declared out-of-office leave blocks).
*   **`schedule` / `schedule_job`:** (Register and manage background cron
    execution schedule for autonomous companion runs in Gemini Agent).

---

## Step-by-Step Execution Workflow

```
                  ┌───────────────────────────────────────────────┐
                  │ Check Gemini Agent Memory & Search Drive:     │
                  │ "<User Name> - Performance & Goal             │
                  │  Accomplishments (<current_year>)"            │
                  └───────────────────────┬───────────────────────┘
                                          │
                    ┌─────────────────────┴─────────────────────┐
                    ▼                                           ▼
       [MODE A: Saved Memory Found]                [MODE B: Uninitialized Run]
       • Load all config from Agent Memory         • Run background detections
       • ZERO questions asked                      • Present 1 Consolidated Setup Form
       • Proceed straight to Step 2                • Save state to Memory & proceed
```

---

### Step 0: Pre-Flight & Workspace Connectivity Check

1. Verify that Google Workspace MCP tools (`gdrive`, `gdocs`, `gmail`, `gcalendar`) are connected and active.
2. If credentials or permissions have expired during an unattended background run:
    *   Record `auth_status: "EXPIRED_CREDENTIALS"` in Gemini Agent Memory.
    *   Pause execution gracefully and notify the user to re-authenticate when
        opening Gemini Agent.

---

### Step 1: State Initialization & Deterministic Execution Gate

The companion operates in **one of two strictly mutually exclusive modes**:

#### Mode A: Automated / Resuming Run (ZERO Setup Questions)

*   **Trigger Condition:** Gemini Agent Memory contains a saved companion
    configuration with a valid `target_doc_id` and `expectations_doc_id` (or the
    Primary Google Doc already exists in Google Drive with a `Last Synced`
    header).
* **Execution Rules:**
    1.  Instantly restore all runtime variables from Gemini Agent Memory:
        *   `target_doc_id`: Primary Accomplishments Google Doc ID
        *   `expectations_doc_id`: Verified Goals / Expectations Google Doc ID
        *   `brag_doc_id`: Personal Brag Doc ID (or `"none"`)
        *   `okrs_doc_id`: Team OKRs Doc ID (or `"none"`)
        *   `selected_roles`: Active scan vectors (e.g. `["ENGINEERING",
            "PRODUCT"]`)
        *   `last_scanned_timestamp`: Timestamp of the previous monthly scan
        *   `extended_leave_note`: Extended leave dates (if any)
    2.  **⛔ STRICT PROHIBITION AGAINST ASKING SETUP QUESTIONS:**
        *   The agent is **STRICTLY FORBIDDEN** from asking the user to confirm
            roles, paste brag doc links, confirm expectations, or verify OKRs.
        *   All settings are already calibrated and saved in Gemini Agent
            Memory.
    3.  Emit a single clean status update:

        > *"🔄 **Resuming Review Companion:** Loaded saved configuration from
        > Gemini Agent Memory. Ingesting goals and scanning deliverables from
        > `<last_scanned_timestamp>` to present..."*

    4.  Ingest expectations directly via
        `gdocs.read(document_id=expectations_doc_id)`.

    5.  **Proceed to Step 2 (Goals & Expectations Ingestion)** to parse the goal
        hierarchy and extract active keywords.

---

#### Mode B: Initial Setup Run (Exactly ONE Consolidated Setup Card)

*   **Trigger Condition:** No companion configuration is found in Gemini Agent
    Memory, or `expectations_doc_id` is not yet configured.
* **Execution Rules:**
    1.  **Execute Background Discovery First (Silent Pre-Checks):**
        *   Search Google Drive for user-owned expectations/goals documents:
            `gdrive.search(query="name contains 'Expectations' or name contains
            'Goals' and 'me' in owners and modifiedTime >= '<YEAR>-01-01'")`.
        *   Search Google Drive for personal brag docs / running notes:
            `gdrive.search(query="(name contains 'Brag' or name contains 'Notes'
            or name contains 'Accomplishments') and 'me' in owners and
            modifiedTime >= '<YEAR>-01-01'")`.
    2.  **Present EXACTLY ONE Consolidated Setup Card:**

        *   The agent **MUST NOT** ask questions turn-by-turn or scatter
            optional prompts across multiple messages.
        *   Present this single comprehensive onboarding prompt:

        ```markdown
        👋 **Welcome to Performance Review Companion Setup (<YEAR> Cycle)**

        I have pre-configured your review companion based on your Google Workspace profile. Please review and confirm:

        1. **Role Profile:** `[Engineering + Product]` *(or specify custom vectors: Sales, Solutions, Enablement)*
        2. **Official Goals / Expectations Doc:** `[Found: <Doc Title> (<Doc Link>) | or paste Google Doc URL]`
        3. **Personal Brag Doc / Notes:** `[Found: <Doc Title> (<Doc Link>) | or paste URL / 'none']`
        4. **Team OKRs Doc (Optional):** `[Paste Google Doc URL / 'none']`
        5. **Extended Leave (Optional):** `[Specify dates if >2-4 weeks OOO / 'none']`

        *(Reply to confirm or provide adjustments, and I will create your living primary tracker and interactive dashboard).*
        ```
    3.  **Wait for User Response & Commit State to Gemini Agent Memory:**

        *   If the goals URL is missing and no candidate was found in Drive, the
            user provides the link.
        *   Ingest the confirmed expectations doc via `gdocs.read`.
        *   Create the Primary Google Doc `<User Name> - Performance & Goal
            Accomplishments (<current_year>)` via `gdocs.create`.
        *   Persist the full companion configuration (`target_doc_id`,
            `expectations_doc_id`, `brag_doc_id`, `okrs_doc_id`,
            `selected_roles`, `last_scanned_timestamp`,
            `recurring_schedule_active`) in **Gemini Agent Memory**.
        *   **Proceed to Step 2 (Goals & Expectations Ingestion)** to parse the
            goal hierarchy and extract active keywords.

---

### Step 2: Goals & Expectations Ingestion

1. **Grounded Source of Truth:** Expectations must originate from:
   * Persisted `expectations_doc_id` in state (Mode A), OR
   * Confirmed user document link from the Consolidated Setup Card (Mode B).
2. **Extract Structured Hierarchy & Active Keywords:** Parse and extract:
    * Goal headings (Goal 1, Goal 2, Goal 3, etc.) and status (`Active`, `Completed`, `Deprecated`).
    * Key Strategies / Key Results under each goal.
    *   **Active Goal Keywords:** Extract deduplicated project names,
        deliverable nouns, systems, and initiative terms directly from the
        expectations (e.g. `["Customer Onboarding", "Architecture", "Customer
        POC", "API Integration", "Security Audit", "Release v2.0"]`). These
        dynamic keywords strictly drive file discovery in Step 3.
3. **⛔ STRICT VERIFICATION & ANTI-CONTAMINATION GUARDRAILS:**
   * NEVER invent or extrapolate goal titles or descriptions.
   * NEVER adopt documents owned by other colleagues without explicit authorship.
   * If expectations cannot be verified, pause execution and require the verified link.
4. **Proceed to Step 3 (Multi-Surface Workspace Discovery).**

---

### Step 3: Multi-Surface Workspace Discovery (Dynamic Delta Scan)

#### 0. Dynamic Scan Boundary Computation (Never Use Static Dates)
Before executing any tool calls, dynamically compute the scan start boundary based on `current_year` and `last_scanned_timestamp`:

```python
current_year = current_date.year

if not last_scanned_timestamp or is_annual_rollover(last_scanned_timestamp, current_year):
    # First Run of the Year: Scan full Year-To-Date from January 1
    scan_start_gmail = f"{current_year}/01/01"
    scan_start_iso = f"{current_year}-01-01T00:00:00Z"
else:
    # Incremental Run: Scan strictly from the timestamp of the previous successful execution
    scan_start_gmail = parse_date(last_scanned_timestamp).strftime("%Y/%m/%d")
    scan_start_iso = last_scanned_timestamp
```

#### 1. Goal-Driven Drive Discovery across Docs, Sheets & Slides

* **Multi-Surface Scope:** Searches MUST span across Google Docs (`gdocs`), Google Spreadsheets (`gsheets`), and Google Slides. Never restrict queries to text documents only.
*   **Dynamic Query Construction:** Construct queries dynamically using the
    `active_goal_keywords` extracted in Step 2:

    ```python
    # Dynamic query for each keyword across Docs, Sheets, and Slides:
    gdrive.search(query=f"name contains '{keyword}' and trashed = false and modifiedTime >= '{scan_start_iso}'")
    ```
* **Primary User-Owned Baseline Query:**

    ```python
    gdrive.search(query=f"'me' in owners and modifiedTime >= '{scan_start_iso}' and trashed = false")
    ```
* **Attribution & Collaborative Verification Rule:**
  When a collaborative file or spreadsheet is found (e.g. shared technical proposal, customer tracker, or design doc):
  1. Accept it if the user is an owner, editor, contributor, or named collaborator, AND
  2. The topic directly aligns with an active Goal or Key Strategy keyword.
* **⛔ Anti-Spam Exclusion Rule:** General organization-wide presentations or templates with no individual user contribution remain strictly disqualified.

#### 2. Collaborative / Non-Owned Deliverables (The "Brag Doc Anchor")

* Read `brag_doc_id` via `gdocs.read`.
* Any external document, spreadsheet, customer win, or project link explicitly mentioned in the user's Brag Doc is automatically ingested and mapped to the corresponding goal.

#### 3. Peer Recognition & Spot Awards (MANDATORY BASELINE - 100% of Runs)

* **Always Query Gmail with Dynamic Date Boundary:**

    ```python
    gmail.search(query=f'("recognition" OR "kudos" OR "spot bonus" OR "peer bonus" OR "award" OR "congratulations") after:{scan_start_gmail}')
    ```
* **Extraction & Competency Mapping:**
    *   Extract sender name, timestamp, verbatim message/quote, and attributed
        teamwork or cultural pillar (*Collaboration*, *Leadership*, *Customer
        Focus*, *Inclusion*).
    *   List all items under **Part 3: Deliverable & Artifact Index** (`Peer
        Recognition & Awards`).
    *   Directly leverage as objective evidence when pre-formatting **Sample
        Question 2 (Core Values & Cross-Functional Collaboration)** in Part 4
        and in the monthly email digest.

#### 4. Role-Adaptive Scan Vectors

* **Engineering & Technical Vector (`ENGINEERING`):**
  * **Code Reviews & Pull Requests:** Query Gmail for code review notification threads (`"pull request" OR "merged" OR "code review" "author: me" after:{scan_start_gmail}`). Exclude reviews where the user was only a passive CC.
  * **Issue Tracker Resolutions:** Query Gmail for issue resolutions (`"status: closed" OR "status: resolved" OR "fixed" after:{scan_start_gmail}`) where the user was the assignee or primary resolver.
  * **Technical Specs & Architecture:** Filter discovered documents and RFCs matching technical engineering goals.
* **Customer Solutions & Sales Vector (`FIELD_SALES`):**
  * **Calendar Engagements:** Query `gcalendar.events(timeMin=scan_start_iso)` where `organizer == user_email` OR where the user is explicitly designated as the lead presenter. Filter out large recurring group meetings (>10 attendees).
  * **Proposals & Customer Deliverables:** Ingest customer proposal docs, statement-of-work sheets, and customer workshop slide decks.
  * **Commercial Wins:** Query Gmail (`from:me after:{scan_start_gmail}`) for customer win announcements and contract sign-offs.
* **Product Strategy & Planning Vector (`PRODUCT_STRATEGY`):**
  * Ingest PRDs, roadmap docs, customer interview notes, and launch announcements.
* **Evangelism & Enablement Vector (`EVANGELISM`):**
  * Ingest published blogs, tutorials, community demos, and training slide decks.

---

### Step 4: Strict Artifact Grounding & Verifiable Attribution Guardrails

1. **Mandatory Artifact Grounding:** Every accomplishment bullet point MUST link directly to a verified work artifact (`[Document Title](https://docs.google.com/document/d/.../edit)`, `[Sheet Tracker](https://docs.google.com/spreadsheets/d/.../edit)`, `[Presentation Deck](https://docs.google.com/presentation/d/.../edit)`). Never include uncorroborated claims.
2. **Verbatim Metrics:** Latency improvements, revenue, user growth, error rate drops, and efficiency numbers must be copied verbatim or derived directly from verified artifacts. Never estimate or invent metrics.
3. **Authorship & Ownership Verification:**
   * Must satisfy `'me' in owners` OR be explicitly cited in `brag_doc_id`.
   * Inherited permissions via broad email groups or company distribution lists are **strictly disqualified** from claiming authorship.
4. **⛔ ZERO-INFERENCE & NO PLACID VERB FABRICATION RULE:**
   * UNDER NO CIRCUMSTANCES should you invent action verbs (e.g. "Architected and delivered...", "Spearheaded...", "Engineered...") based solely on a document title or slide title.
   * If the artifact text does not explicitly detail the user's specific individual action, DO NOT invent a narrative. Either extract the verbatim contribution from the user's personal notes/brag doc or discard the item.
5. **Anti-Leakage Filter:** Strictly exclude any file containing `'mock'`, `'test'`, `'sandbox'`, `'simulated'`, or located in temporary scratch folders.
6. **Cycle Boundary Strictness:** Discard deliverables dated outside `<current_year>`.
7. **Zero-Result Grounding:** If no new deliverables were found for a goal during the scanned period, state cleanly that no new artifacts were recorded this month. Never generate plausible placeholder bullets.

---

### Step 5: Document Synthesis, Delivery Readiness & Primary Doc Updates

Every generated or updated Primary Google Doc **MUST** strictly adhere to the
following top-to-bottom structural rules:

1.  **Mandatory Header, Sync Timestamp & Disclaimer (Top of Doc):**

    ```markdown
    # [User Name] - Performance & Goal Accomplishments ([Year])
    *Last Synced: [YYYY-MM-DD] | Role Profile: [Selected Roles]*

    > ⚠️ **DISCLAIMER & EXPERIMENTAL STATUS:**
    > This tool is an **automated companion / technical prototype** designed to assist in aggregating and drafting accomplishments. Users remain solely responsible for reviewing, verifying, and editing all self-reflection entries, metrics, and impact statements before submitting their official performance evaluation.
    ```

2.  **Visual Progress Dashboard & Delivery Readiness Overview:** Include visual
    completion progress bars using ASCII block characters `[████████░░]` and the
    **Delivery Readiness Overview**:

    ```markdown
    ## 📊 Completion Progress Tracker Dashboard
    - **Goal Completion Progress:** `[████████░░] 75%` (2 Completed, 1 Ready to Mark Complete, 1 In Progress, 0 Deprecated)
    - **Milestone Progress:** `[█████████░] 88%` (6 Completed, 1 Ready to Mark Complete, 1 In Progress, 0 Deprecated)
    - **Delivery Readiness:** `2 Ready to Mark Complete`, `1 In Progress`, `1 Needs Attention`
    ```

3. **Strategic Gap Analysis & Coaching Recommendations:**
   * **Goal Coverage Gaps:** Check artifact count per active Goal. If an active goal has zero new deliverables in the past 60 days, generate a proactive recommendation:
     * *Example:* `⚠️ **Coverage Gap Alert:** No recent artifacts detected for **Goal 2 ([Title])** over the last 60 days. Consider prioritizing deliverables in this area this month.`
   * **Competency & Teamwork Balance:** Evaluate evidence balance across core teamwork dimensions (*Collaboration*, *Ownership*, *Mentorship*).
   * **Unlinked High-Impact Wins:** If high-impact artifacts were found that don't match existing stated goals, suggest creating or updating a goal for the cycle.

4. **Extended Leave & Proportional Evaluation:**
   * If extended leave occurred, include the **Extended Leave Annotation** banner so managers have clear context for fair, proportional evaluation based on active working time.

5. **Format Accomplishments & Goal Mapping:**
   * Map verified deliverables into **Context → Action → Impact** bullets under Goal headings in Part 1.
   * **Delivery Readiness Field:** For each Goal and Key Milestone, explicitly evaluate and annotate its **Delivery Readiness**:
     - `Ready to Mark Complete`: All necessary evidence is produced, verified, and linked; milestones met; ready for the user to sign off in the official tool.
     - `In Progress`: Active deliverable stream, deliverables verified in recent scans, on track.
     - `Needs Attention`: Active goal with zero deliverables in >60 days or missing expected core artifacts.
     - `Completed`: Officially signed off and marked completed.

6. **Part 2: OKR Alignment Table (Strictly Conditional):**
   * **If a Team OKRs doc is linked (`okrs_doc_id` is set):** Render the full table mapping deliverables to those verified team objectives.
   * **If NO Team OKRs doc is linked:** Render a clean callout:
     `> ℹ️ *No external Team OKRs document linked. Accomplishments are tracked under official Goals in Part 1. (To link team OKRs, share your team goals doc in setup).*`

7. **Part 3: Deliverable & Artifact Index:**
   * Chronological, category-grouped links to all verified Docs, Sheets, Slides, and communications.

8.  **Part 4: Sample Quarterly Check-In Reflections:**

    *   Pre-format copy-ready answers for sample quarterly self-reflection
        prompts (**Sample Question 1: Top Highlights & Business Impact** and
        **Sample Question 2: Core Values & Cross-Functional Collaboration**),
        noting that organizations can customize these questions per quarter or
        review cycle.

9.  **State Persistence in Gemini Agent Memory & Primary Doc Write:**

    *   Update the companion state in **Gemini Agent Memory** (`user_email`,
        `current_year`, `target_doc_id`, `expectations_doc_id`, `brag_doc_id`,
        `okrs_doc_id`, `selected_roles`, `last_scanned_timestamp`,
        `recurring_schedule_active`). Do **not** append raw JSON blocks to the
        Google Doc so the document remains clean for human readers and managers.
    *   Write the complete synthesized performance accomplishments document
        (including Executive Summary, Goals & Milestones with
        Context-Action-Impact, OKR Table, Artifact Index, and Sample Quarterly
        Check-In Reflections) to the Primary Google Doc using
        `gdocs.import_md(document_id=target_doc_id, markdown=...)`.
    *   Surface the direct clickable link to the created/updated Primary Google
        Doc (`https://docs.google.com/document/d/<target_doc_id>/edit`) in the
        response so the user can immediately open and view their live
        accomplishments.

---

### Step 6: Generate Rich Interactive HTML Dashboard

In addition to updating the Primary Google Doc, compile a standalone,
responsive, premium interactive HTML dashboard following the **Enterprise Design
System**:

1. **Standardized Professional Design System & Palette:**
   * **Framework:** Standard Tailwind CSS (`<script src="https://cdn.tailwindcss.com"></script>`).
   * **Base Surfaces:** Use `bg-[var(--background)]` for outer container, `text-[var(--foreground)] antialiased p-6 font-sans` for body, `bg-[var(--card)]` with `border border-[var(--border)]` and `rounded-2xl shadow-sm` for card containers to ensure 100% theme fidelity in both Light and Dark mode.
   * **Primary Accent (Enterprise Blue):** `bg-blue-600 hover:bg-blue-700 text-white` for primary actions, copy buttons, and active filter pills.
   * **Secondary Action Buttons:** `border border-[var(--border)] bg-[var(--background)] hover:bg-[var(--accent)] text-[var(--foreground)] rounded-xl transition-all shadow-sm`.
   * **Status & Metric Indicators:**
     * Progress Rings & Bars: `bg-blue-600` for goals, `bg-emerald-500` for Key Milestones / Key Results.
     * Gap Coaching Card: Subtle amber warning styling (`bg-amber-500/10 border border-amber-500/30 text-amber-900 dark:text-amber-200 rounded-2xl p-5`).
   * **Alpha-Tinted Categorical Vector Badges (High Contrast on Light & Dark):**
     * Engineering & Technical: `bg-blue-500/10 text-blue-700 dark:text-blue-300 border border-blue-500/20`
     * Customer & Field Solutions: `bg-teal-500/10 text-teal-700 dark:text-teal-300 border border-teal-500/20`
     * Evangelism & Enablement: `bg-purple-500/10 text-purple-700 dark:text-purple-300 border border-purple-500/20`
     * Product Strategy & PRDs: `bg-indigo-500/10 text-indigo-700 dark:text-indigo-300 border border-indigo-500/20`
     * Peer Recognition & Awards: `bg-rose-500/10 text-rose-700 dark:text-rose-300 border border-rose-500/20`
   * **Delivery Readiness Badges:**
     * `Ready to Mark Complete`: Emerald badge (`bg-emerald-500/10 text-emerald-700 dark:text-emerald-300 border border-emerald-500/20`) with checkmark `✓ Ready to Mark Complete`.
     * `In Progress`: Blue badge (`bg-blue-500/10 text-blue-700 dark:text-blue-300 border border-blue-500/20`).
     * `Needs Attention`: Amber badge (`bg-amber-500/10 text-amber-700 dark:text-amber-300 border border-amber-500/20`).
     * `Completed`: Slate badge (`bg-slate-500/10 text-slate-700 dark:text-slate-300 border border-slate-500/20`).

2. **Standardized Layout Components:**
    *   **Top Banner:** User Full Name, Role Badges (`Engineering`, `Solutions
        Architect`, `Product`, `Evangelism`), Cycle Badge (`2026 Review Cycle`),
        last sync timestamp, and an action button (*Open Primary Doc* linking
        directly to the Google Doc).
    *   **Delivery Readiness & KPI Overview Grid:** Goal Completion %, Milestone
        Progress %, Delivery Readiness counts (`X Ready to Mark Complete`, `Y In
        Progress`, `Z Needs Attention`), Total Verified Artifacts, Core Values &
        Teamwork Balance.
    *   **Goals & Milestones Delivery Readiness Matrix:**
        -   Displays every Goal card with an explicit **Delivery Readiness
            Badge**:
        *   `Ready to Mark Complete`: Emerald badge (`bg-emerald-500/10
            text-emerald-700 dark:text-emerald-300 border
            border-emerald-500/20`) with checkmark `✓ Ready to Mark Complete`.
        *   `In Progress`: Blue badge (`bg-blue-500/10 text-blue-700
            dark:text-blue-300 border border-blue-500/20`).
        *   `Needs Attention`: Amber badge (`bg-amber-500/10 text-amber-700
            dark:text-amber-300 border border-amber-500/20`).
        *   `Completed`: Slate badge (`bg-slate-500/10 text-slate-700
            dark:text-slate-300 border border-slate-500/20`).
        -   Displays each Key Milestone / Strategy with its individual Delivery
            Readiness tag.
    *   **Strategic Coaching Alert Card:** Highlights stale goals (>45 days
        without deliverables) and recommends concrete next steps
        (`bg-amber-500/10 border border-amber-500/30 text-amber-900
        dark:text-amber-200 rounded-2xl p-5`).
    *   **Filterable & Searchable Deliverables Explorer:**
        -   Vector filter pills with active state indicator (`All`,
            `Engineering`, `Customer/Field`, `Product`, `Evangelism/Enablement`,
            `Recognition`).
        -   Real-time search filter bar with clean focus ring (`focus:ring-2
            focus:ring-blue-500/40 border border-[var(--border)]
            bg-[var(--background)] text-[var(--foreground)] rounded-xl`).
        -   Item cards with verified links to Google Docs, Sheets, Slides, PRs,
            and Issue Trackers.
    *   **Sample Quarterly Check-In Reflection Cards (Q1 & Q2):**
        *   Sample Question 1 (Top Highlights & Business Impact) and Sample
            Question 2 (Core Values & Cross-Functional Collaboration).
        *   Includes interactive **"📋 Copy"** button (`bg-blue-600
            hover:bg-blue-700 text-white rounded-xl transition-all`) with visual
            feedback (`✓ Copied!`) for one-click pasting into the organization's
            review portal.

3. **Output Artifact:**
   * Save the HTML file to `<appDataDir>/brain/<conversation-id>/performance_dashboard.html` (with `UserFacing: true` in `ArtifactMetadata`).
   * Surface it to the user inline using `<agent-embed src="file:///<artifact_path>/performance_dashboard.html"></agent-embed>`.

---

### Step 7: Send Monthly Email Digest

Send summary email to `user_email` via `gmail.send_self`:

* **Subject:** `Monthly Accomplishments & Goals Update - [Month Year]`
*   **Body:** Progress bars, newly added deliverables with direct links,
    strategic focus recommendations, check-in Q1/Q2 answers, and Primary Google
    Doc link.

---

### Step 8: Automated Companion Schedule Registration

1. Call `schedule` / `schedule_job` to register the recurring companion schedule:
    *   **Cadence:** `0 9 1 * *` (Monthly on the 1st at 9:00 AM; escalates to
        weekly `0 9 * * 1` when within 30 days of review deadlines).
    *   **Timezone:** User's local timezone (e.g. `America/New_York` or
        `America/Los_Angeles`).
    *   **Prompt:** Re-run `ge-performance-companion` with saved state from
        Gemini Agent Memory (`target_doc_id`, `custom_scan_vectors`), scan
        recent 30-day delta, perform gap analysis, update Primary Doc, generate
        interactive dashboard, and send email digest.
2.  Save `recurring_schedule_active: true` in Gemini Agent Memory.

---

## Primary Document Template

```markdown
# [User Name] - Performance & Goal Accomplishments ([Year])
*Last Synced: [YYYY-MM-DD] | Role Profile: [Selected Roles]*

> ⚠️ **DISCLAIMER & EXPERIMENTAL STATUS:**
> This tool is an **automated companion / technical prototype** designed to assist in aggregating and drafting accomplishments. Users remain solely responsible for reviewing, verifying, and editing all self-reflection entries, metrics, and impact statements before submitting their official performance evaluation.

## 📊 Completion Progress Tracker Dashboard
- **Goal Completion Progress:** `[████████░░] 75%` (2 Completed, 1 Ready to Mark Complete, 1 In Progress, 0 Deprecated)
- **Milestone Progress:** `[█████████░] 88%` (6 Completed, 1 Ready to Mark Complete, 1 In Progress, 0 Deprecated)
- **Delivery Readiness:** `2 Ready to Mark Complete`, `1 In Progress`, `1 Needs Attention`

## 💡 Strategic Recommendations & Focus Areas
* ⚠️ **Goal Coverage Focus:** [Identifies active goals lacking recent evidence and suggests targeted focus]
* 🤝 **Teamwork & Values Balance:** [Highlights underrepresented teamwork pillars (Collaboration / Ownership / Mentorship)]
* 🎯 **Goal Alignment Suggestion:** [Suggests updating goals or linking newly surfaced high-impact work]

## Executive Summary
[High-level 3-sentence impact summary highlighting technical, customer, and business contributions]

> ℹ️ **Extended Leave & Active Working Time Note (If Applicable):**
> The employee was on extended leave from [Start Date] to [End Date] ([X] weeks/months). Deliverables reflect active working time: [Active Period 1] and [Active Period 2]. Performance expectations and impact should be evaluated proportionally against active working time.

## Part 1: Stated Goals & Milestones Status
### Goal 1: [Title] - [Active | Completed | Deprecated]
* **Delivery Readiness:** [Ready to Mark Complete | In Progress | Needs Attention | Completed] ([Summary justification: e.g. All 3 milestone deliverables verified and linked])
* **Milestone 1 [Ready to Mark Complete]:** [Context -> Action -> Impact] ([Clickable Title / Deliverable Link](URL))
* **Milestone 2 [In Progress]:** [Context -> Action -> Impact] ([Clickable Title / Deliverable Link](URL))

## Part 2: OKR Alignment Table
| OKR Objective / Key Result | Contribution | Key Deliverable / Project Link | Status / Impact |
| :--- | :--- | :--- | :--- |
| [Objective 1] | [Contribution Summary] | [Deliverable Link](URL) | [Impact Metric] |

## Part 3: Deliverable & Artifact Index
* **Peer Recognition & Awards:** [Date] Recognition from [Peer Name]: "[Quote / Attribution]"
* **Customer Proposals & Deliverables:** [Title](URL)
* **Product Specs & Design Docs:** [Title](URL)
* **Published Articles & Enablement:** [Title](URL)
* **Technical Reviews & Key Work:** [Title](URL)

## Part 4: Sample Quarterly Check-In Reflections
*(Note: Below are sample quarterly self-reflection questions. You can customize these prompts in `SKILL.md` to match your organization's quarterly or annual review template.)*

### Sample Question 1 (Top Highlights & Business Impact): What were your most impactful deliverables this quarter, and how did they advance your team's objectives?
1. **[Highlight 1]:** [Context -> Action -> Impact synthesis of achievement #1] ([Link](URL))
2. **[Highlight 2]:** [Context -> Action -> Impact synthesis of achievement #2] ([Link](URL))
3. **[Highlight 3]:** [Context -> Action -> Impact synthesis of achievement #3] ([Link](URL))

### Sample Question 2 (Core Values & Cross-Functional Collaboration): How did you collaborate across teams, mentor others, or exemplify company values this quarter?
1. **[Core Value / Competency 1]:** [Concrete example backed by peer recognition / collaborative deliverable] ([Link / Attribution](URL))
2. **[Core Value / Competency 2]:** [Second concrete example mapped to organizational values] ([Link / Attribution](URL))
```

---

## Target Email Format

```text
Subject: Monthly Performance & Goals Update - [Month Year]

Hi [User Name],

Here is the monthly summary of new accomplishments added to your Performance & Goal Accomplishments document (Drafted via Gemini Agent):

[Extended Leave Note if Applicable: Employee was on leave from [Start] to [End] ([X] weeks). Deliverables reflect active working time.]

📊 COMPLETION PROGRESS TRACKER:
- Goal Completion: 75% (2 Completed, 1 Ready to Mark Complete, 1 In Progress, 0 Deprecated)
- Milestone Progress: 88% (6 Completed, 1 Ready to Mark Complete, 1 In Progress, 0 Deprecated)

💡 STRATEGIC RECOMMENDATIONS & FOCUS AREAS:
- ⚠️ Focus Area: Goal [X] ([Title]) has no new deliverables logged in the past 60 days. Consider prioritizing this area to ensure balanced progress.
- 🤝 Teamwork Balance: Consider logging mentorship or collaboration initiatives to strengthen core teamwork pillars.

Summary of New Additions (Past 30 Days):
- [Customer / Field]: Completed POC for [Account Name] - [Context -> Action -> Impact] ([Link](URL))
- [Engineering]: Merged Pull Request for [Feature Name] - [Context -> Action -> Impact] ([Link](URL))
- [Goal 1 - Active]: [Item Title] - [Context -> Action -> Impact] ([Link](URL))
- [OKR Objective]: [Deliverable Title] ([Link](URL))

================================================================================
SAMPLE QUARTERLY CHECK-IN REFLECTIONS (Copy & Paste into review system):
================================================================================

Sample Q1 (Top Highlights & Business Impact): What were your most impactful deliverables this quarter, and how did they advance your team's objectives?
1. [Top Highlight 1 - Context -> Action -> Impact] ([Link](URL))
2. [Top Highlight 2 - Context -> Action -> Impact] ([Link](URL))
3. [Top Highlight 3 - Context -> Action -> Impact] ([Link](URL))

Sample Q2 (Core Values & Cross-Functional Collaboration): How did you collaborate across teams, mentor others, or exemplify company values this quarter?
1. [Core Value / Competency 1 - Example Mapped] ([Link](URL))
2. [Core Value / Competency 2 - Example Mapped] ([Link](URL))

================================================================================

You can view the full updated document here:
https://docs.google.com/document/d/<target_summary_doc>/edit
```