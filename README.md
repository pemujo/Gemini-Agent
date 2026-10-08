# Gemini Agent Blueprints & Skills
A collection of reference implementations, plain-text skills, and architectural blueprints for building autonomous, long-running workflows with **Gemini Agent**.
Rather than building custom backend pipelines, managing database state, or maintaining glue code, the blueprints in this repository demonstrate how to turn complex, multi-step enterprise workflows into portable `SKILL.md` specifications that run natively on top of your existing Google Workspace artifacts (Docs, Sheets, Slides, Drive, Gmail, and Calendar).


## Getting Started
1. **Browse a Blueprint:** Navigate into any blueprint folder (for example, [`performance_companion`](./performance_companion/)) and review its `README.md` for architecture details and sample outputs.
2. **Add the Skill to Gemini Agent:** Copy or upload the folder's `SKILL.md` into your **Gemini Agent** skills configuration.
3. **Trigger the Workflow:** Start a conversation with Gemini Agent using the starter prompt provided in the blueprint's documentation.


## Prerequisites & Disclaimer
> **Note:** Because these workflows rely on native Google Workspace Connectors and Enterprise Data Protection, an active **Gemini for Google Workspace / Gemini Agent** environment is required.
>
> The reference implementations and blueprints in this repository are provided for demonstration and educational purposes only and are not officially supported Google products.
