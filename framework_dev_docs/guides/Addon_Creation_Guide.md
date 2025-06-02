# Guide for Creating Prompt Add-ons (MPS-like Modules)

## 1. Introduction
This guide provides a methodology for developing "Prompt Add-ons". These add-ons are blocks of instructional text that can be appended by a Planning AI (guided by the Master Prompt Segment) to task prompts for Task AIs. They enhance Task AI capabilities or enforce specific protocols. Add-on source texts are typically stored in the framework's `/prompts/add_ons/` directory.

## 2. Core Principles for Add-on Design
*   **Clear Purpose:** Single, well-defined purpose.
*   **Explicit Instructions:** Clear, direct language for the Task AI.
*   **Modularity:** Self-contained as much as possible. State dependencies (e.g., on Base IEP extensions).
*   **User-Centricity:** How does it make Task AI output more useful?
*   **Placeholder Usage:** Use placeholders like `[Actual Path to Prompts Folder]`, `[Actual User-Specified Path for Submodule Plans]`, `[Actual User-Specified Task Output Base Path]`, and `<ExactOriginalTaskPromptFilenameBase>` which the Planning AI (guided by MPS) will resolve when embedding the add-on.

## 3. Steps to Develop a New Prompt Add-on

### Step 3.1: Define Purpose and Scope
*   Objective, Target AI (usually Task AI), Inputs, Outputs/Artifacts, Success Criteria.

### Step 3.2: Draft the Add-on Text
*   Start with `[[START OF 'YOUR_ADDON_NAME' ADD-ON vX.Y: Brief Description]]`.
*   Core instructions for the Task AI. Use imperative verbs.
*   **Interaction with Base IEP:** If your add-on requires Task AIs to log specific information, define structured tags for the `Notes-To-Next-Jules` field of the Base IEP. Document these tags clearly. Example: `[YOUR_ADDON_TAG]: Value`.
*   End with `[[END OF 'YOUR_ADDON_NAME' ADD-ON vX.Y]]`.
*   **Save your add-on text** to a file in `/prompts/add_ons/your_addon_name.txt` in the framework development repo.

### Step 3.3: Design Supporting Artifacts (if any)
*   Define structure/content of any files the Task AI creates due to your add-on.

### Step 3.4: Update Add-on Manifest
*   Add your new add-on's filename and a brief description to `/prompts/add_ons/available_addons_manifest.md` in the framework development repo.

### Step 3.5: Create an Example Usage Guide for the Add-on
*   Explain what it does, show its text, and an example of a Task AI's output (e.g., commit message with new IEP tags, or a created file). Store in `/framework_dev_docs/examples/your_addon_example.md`.

### Step 3.6: Test and Refine
*   Test by having the MPS instruct a Planning AI to include your add-on. Evaluate Task AI behavior.

## 4. Example: Task Resumption Add-on
(Refer to `/prompts/add_ons/task_resumption_addon.txt` and `/framework_dev_docs/examples/task_resumption_example.md`)

## 5. Conclusion
Well-designed add-ons make the MPS framework highly extensible and adaptable.
