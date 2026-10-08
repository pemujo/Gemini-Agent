# 🤝 Performance Review Companion (Demo)

### *Autonomous Long-Running Performance Companion for Gemini Agent*

> [!IMPORTANT] **DEMO ONLY: NOT AN OFFICIALLY SUPPORTED GOOGLE PRODUCT** \
> This repository is a technical demonstration and reference architecture
> developed to illustrate agentic workflow patterns in Gemini Agent. This is
> **not an officially supported Google product**, nor is it an official human
> resources or performance evaluation system.
>
> This software is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
> CONDITIONS OF ANY KIND. Users remain solely responsible for reviewing,
> verifying, and editing all self-reflection entries, metrics, and claims before
> submitting their official performance evaluation in their organization's
> review system.

---

## ⏳ An Archetype of a Long-Running Agent (LRA)

Most AI assistants are ephemeral and turn-based: you ask a question, they answer in ten seconds, and their session memory vanishes.

**Performance Review Companion is fundamentally different: it is an autonomous Long-Running Agent (LRA) that operates across a multi-month and annual temporal horizon.**

Dimension               | Standard One-Off Prompt / Chatbot    | Long-Running Agent (Performance Companion)
:---------------------- | :----------------------------------- | :-----------------------------------------
**Time Horizon**        | Minutes (single prompt/turn)         | **Months to an entire calendar year** (full review cycle)
**Execution Model**     | Interactive prompt → response → idle | **Autonomous background schedule** (`0 9 1 * *`) without manual prompting
**State Lifespan**      | Ephemeral (lost when context resets) | **Cross-session persistence** (Persisted natively in Gemini Agent Memory)
**Processing Paradigm** | Stateless point-in-time computation  | **Incremental delta tracking** (`last_scanned_timestamp` → present)
**Strategic Reasoning** | Point-in-time summarization          | **Longitudinal gap analysis & Delivery Readiness tracking**
**Output Delivery**     | Chat message only                    | **Decoupled channels** (Primary Google Doc, Interactive UI Dashboard, Gmail digest)

---

## 🎯 Target Application & Platform Capabilities

This skill is purpose-built to showcase the autonomous companion capabilities of
**Gemini Agent**:

1.  **Native Workspace MCP Integration:** Designed around standard Model Context
    Protocol (MCP) integrations (`gdrive`, `gdocs`, `gmail`, `gcalendar`). It
    provides a seamless, zero-terminal experience for aggregating documents,
    spreadsheets, presentations, calendar meetings, and email notification
    streams.
2.  **Autonomous Background Companion & Native Memory:** \
    Showcases Gemini Agent's native background scheduling (`schedule` / cron)
    and built-in persistent memory, where the companion remembers your role
    profile, linked documents, and last scan timestamp across monthly wakeups,
    autonomously tracks deliverables, performs strategic gap analysis, updates
    your central Google Doc, and sends email digests.
3.  **Strategic Cloud Execution & Enterprise Data Protection (EDP):** \
    While early prototypes can be evaluated on the desktop agent today, the
    strategic cloud architecture runs Gemini Agent tasks 24/7 inside secure
    Google Cloud containers, executing scheduled monthly passes without
    requiring a local laptop to stay awake. Furthermore, under **Enterprise Data
    Protection (EDP)**, company and employee data is never used to train
    foundation models.

---

## 💡 How This Companion Transforms Performance Reviews

* 🤖 **Continuous Monthly Tracking vs. Point-in-Time Scrambles:** Rather than drafting everything right before a deadline, this companion assists you month-by-month (`0 9 1 * *`), logging accomplishments incrementally so your wins throughout the year are preserved effortlessly.
* 🧠 **Strategic Gap Analysis & Delivery Readiness Coaching:** Analyzes goal coverage and flags areas where you lack recent evidence, annotating goals with actionable status badges (**`Ready to Mark Complete`**, **`In Progress`**, **`Needs Attention`**) before deadlines approach.
*   🍏 **Zero Custom Engineering (MCP-First):** Operates entirely through native
    Workspace MCPs and Gemini Agent Memory without requiring custom pipelines,
    external databases, or data migration.
* 🎯 **Tailored for Diverse & Hybrid Roles:** Broadens support beyond software engineering to celebrate customer POCs, sales proposals, marketing collateral, PRDs, training workshops, and peer recognitions.
* 📋 **4-Pillar Holistic Ingestion:** Seamlessly blends Official Goals + Team OKRs + Personal Notes/Brag Docs + Everyday Workspace Activity into a single cohesive narrative.
*   📝 **Sample Quarterly Check-In Reflections (Customizable):** Pre-formats
    synthesized answers for sample quarterly self-reflection prompts (Top
    Highlights & Business Impact, and Core Values & Cross-Functional
    Collaboration), which you can customize in plain English inside `SKILL.md`
    to match your organization's quarterly or annual review template.
* ⏱️ **Proportional Impact Support:** Automatically includes leave annotations (parental, medical, sabbatical) so managers and reviewers have clear context for fair, proportional evaluation.

---

## 🌟 Why Every Professional Needs This Tool: The Value Proposition

Writing performance reviews and tracking a year's worth of accomplishments is hard. This tool acts as an **autonomous, set-it-and-forget-it companion** that runs continuously in the background to solve the three biggest friction points in the review process:

1. **Eliminates the "Tracking Gap" via Autonomous Monthly Runs:**  
   Professionals are constantly building, delivering projects, closing deals, and solving problems. Instead of requiring manual prompts, this companion wakes up automatically on the 1st of every month, scans the past 30 days across Drive, Gmail, and Calendar, appends new deliverables to your tracking doc, and emails you a clean digest.
2. **Overcomes "Self-Advocacy Shyness" & Imposter Syndrome:**  
   Many employees understate their impact or feel uncomfortable writing about their achievements. This tool removes the emotional burden of self-promotion by objectively translating your raw digital footprint into crisp, data-backed **Context → Action → Impact** narratives based purely on factual metrics.
3. **Delivers Calibration-Ready Output & Escalated Check-In Reminders:**  
   Instead of spending days frantically digging through emails and links ahead of deadlines, you always have an up-to-date Google Doc. When review deadlines approach (< 30 days), the companion automatically increases its schedule frequency from monthly to weekly to ensure you are completely prepared without last-minute stress.

---

## 🚀 Key Features

*   🤖 **Autonomous Gemini Agent Companion:** Automatically registers recurring
    background schedules (`schedule` / cron `0 9 1 * *`) in Gemini Agent to
    scan, analyze gaps, update your Google Doc, and deliver monthly email
    digests.
* ⚡ **Dynamic Milestone & Deadline Escalation:** Automatically escalates alert and scanning frequency (from monthly to weekly on Mondays) when a review deadline is within 30 days.
* 🎯 **Role-Aware & Hybrid Dynamic Scanning:** Adapts scan vectors depending on your role:
  * **Engineering & Technical:** Technical design specs, RFCs, code review notifications, issue resolutions.
  * **Sales & Field Solutions:** Customer workshops, presentations, statement-of-work sheets, proposals, and contract wins.
  * **Product & Strategy:** PRDs, roadmaps, customer research notes, and product launch announcements.
  * **Evangelism & Enablement:** Published blogs, tutorials, community demos, and training presentations.
* 📋 **Multi-Document Ingestion:**
  * **Stated Goals / Expectations:** Ingests official goals from Google Drive.
  * **Team OKRs Doc (Optional):** Maps deliverables to specific team objectives.
  * **Personal Brag Doc / Notes (Optional):** Ingests self-reported milestones and external project links.
  * **Extended Leave Annotation (Optional):** Proportionally annotates leave periods (>2-4 weeks OOO, parental/medical leave).
*   🛡️ **Strict Artifact Grounding, Noise Filtering & Verifiable Attribution:**
    Every single bullet point is verified against a real artifact link
    (`[Document URL]`, `[Sheet Tracker URL]`, `[Presentation URL]`). Metrics and
    percentages are extracted verbatim with no invented numbers or fabricated
    action verbs. Automatically filters out calendar clutter (>10 attendee
    syncs), passive CCs, inherited group permissions, and
    `mock`/`test`/`sandbox` files.
* 📊 **Delivery Readiness Tracking:** Evaluates every goal into clear milestone states:
  * **`Ready to Mark Complete`**: All deliverables produced, verified, and linked; ready for sign-off.
  * **`In Progress`**: Active deliverables logged in recent cycles; on track.
  * **`Needs Attention`**: Lacks verified deliverables or stagnant for 60+ days.
  * **`Completed`**: Confirmed closed.
*   📝 **Sample Quarterly Check-In Reflections:** Pre-formats copy-and-paste
    answers for sample quarterly self-reflection prompts (customizable in
    `SKILL.md` to match your organization's review cycle):
    *   **Sample Question 1 (Top Highlights & Business Impact):** *"What were
        your most impactful deliverables this quarter, and how did they advance
        your team's objectives?"*
    *   **Sample Question 2 (Core Values & Cross-Functional Collaboration):**
        *"How did you collaborate across teams, mentor others, or exemplify
        company values this quarter?"*
*   🖥️ **Interactive Enterprise UI Dashboard:** Standalone responsive HTML
    dashboard rendered natively inside Gemini Agent styled with Enterprise
    Design System theme variables (`bg-[var(--background)]`, `bg-[var(--card)]`,
    `border-[var(--border)]`), adaptive Light/Dark mode, Delivery Readiness
    badges, real-time search, and one-click copy buttons.

---

## 🏗️ Architecture & Deliverables Tier

![Performance Review Companion Architecture & Workflow](generic_flow_diagram.jpg)

The companion utilizes an autonomous, multi-surface architecture with five core deliverables:

1. **Workspace MCP Connectors:**
   * Google Drive (`gdrive`), Google Docs (`gdocs`), Gmail (`gmail`), and Google Calendar (`gcalendar`).
2.  **Gemini Agent Memory & Scheduler:**
    *   Eliminates external database dependencies and keeps your Google Doc
        clean by persisting your role configuration, linked document IDs, and
        last scan timestamp natively in **Gemini Agent Memory**, paired with an
        autonomous monthly schedule (`0 9 1 * *`).
3. **Multi-Channel Presentation Tier:**
    *   **Living Primary Google Doc:** Visual progress bars, Delivery Readiness
        annotations, and structured Context-Action-Impact accomplishments.
    *   **Interactive Generative UI Dashboard:** Standalone responsive HTML
        dashboard rendered natively inside Gemini Agent.
    *   **Sample Quarterly Check-In Reflections:** Pre-formatted answers for
        sample quarterly reflection prompts (Top Highlights & Business Impact,
        and Core Values & Cross-Functional Collaboration).
    *   **Monthly Email Digest:** Executive summary delivered directly to your
        Gmail inbox.
    *   **Longitudinal Strategic Gap Analysis:** Proactive coaching on 60+ day
        coverage gaps.

---

## 📦 Installation & Setup

### 1. Add via the Gemini Agent User Interface (Recommended)

The easiest and fastest way to install the companion is through the built-in
Skills interface (no terminal commands required):

1. **Obtain the Skill Files:**
    *   Download the `ge_performance_companion` folder containing `SKILL.md` and
        `README.md`.
2.  **Open the Skills Panel in Gemini Agent:**
    *   In **Gemini Agent**, look at the left sidebar navigation and click
        **Skills**.
3. **Import the Skill:**
   * Click the **`+`** (**Add Skill**) button at the top of the Skills pane.
4. **Select the Folder:**
    *   Browse to and select your `ge_performance_companion` directory.
    *   The app will instantly import, validate, and activate the skill, and it
        will immediately appear in your active skills list as
        **ge-performance-companion**!

---

### 2. Alternative: Workspace Directory Setup

If you prefer placing files directly in your Gemini Agent workspace folder:

```bash
# Standard Gemini Agent workspace path:
mkdir -p ~/ge_workspace/skills/ge_performance_companion
cp SKILL.md ~/ge_workspace/skills/ge_performance_companion/SKILL.md
cp README.md ~/ge_workspace/skills/ge_performance_companion/README.md
```

### 3. Enable Required MCP Connectors

Ensure the following MCP integrations are enabled in your Gemini Agent settings:

* ✅ **Google Drive MCP** (`gdrive`)
* ✅ **Google Docs MCP** (`gdocs`)
* ✅ **Gmail MCP** (`gmail`)
* ✅ **Google Calendar MCP** (`gcalendar`)

---

## 💬 Example Prompts to Trigger the Companion

*   **Primary Setup Trigger:** *"Set up my monthly performance review companion.
    Here is the link to my official goals document: [paste your Google Doc
    URL]."*
*   **Natural Language Triggers:**
    *   *"I need to work on my performance review."*
    *   *"Update my accomplishments for this month and schedule my monthly
        review digests."*
    *   *"Track my deliverables and prepare my self-review."*
    *   *"Run review companion and summarize my top highlights for this
        quarter."*

## 🧪 Evaluation & Quality Benchmarking

The package includes an automated evaluation suite (`EVAL.txtpb`) defining 10 test scenarios to validate skill accuracy, role adaptability, Delivery Readiness evaluation, and strict artifact grounding before deploying across an organization:

* **Role Parity Scenarios:** Technical Engineering, Solutions Architecture, Product Strategy, and Field Consulting.
* **Grounding Assertions:** Enforces 100% verified artifact links (`[Doc Title](URL)`), verbatim metrics, and sandbox/mock filtering.
* **Continuous Delivery Readiness:** Asserts proper classification into `Ready to Mark Complete`, `In Progress`, and `Needs Attention`.
* **Zero Internal Dependencies:** Strictly verifies that the agent operates exclusively on Google Workspace connectors.

---

## 📂 File Structure

```
ge_performance_companion/
├── SKILL.md                  # Executable instructions for the AI Agent & Companion Schedule
├── README.md                 # Documentation, value proposition, architecture, and setup guide
├── generic_flow_diagram.jpg  # End-to-end architecture and workflow diagram
├── comparison_visual.jpg     # Before-and-after review cycle comparison visual
└── EVAL.txtpb                # 10-scenario automated evaluation suite
```

---

## 📄 License & Terms of Use

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

This repository is shared for educational and demonstration purposes only and is not an officially supported Google product.