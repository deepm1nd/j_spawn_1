# Guide for Creating Prompt Add-ons (Promptu Modules)

## 1. Introduction
This guide provides a methodology for developing "Prompt Add-ons". These add-ons are blocks of instructional text that can be appended by a Planning AI (guided by the `post_promptu.txt` and `core_planning_instructions.txt`) to task prompts for Task AIs. They enhance Task AI capabilities or enforce specific protocols. Add-on source texts are typically stored in the framework's `/promptu/add_ons/` directory, often within a subdirectory named after the add-on (e.g., `/promptu/add_ons/your_addon_name/your_addon_name.txt`). Users select which add-ons to apply via the `[[USER_ADDON_SELECTION]]` block in their `post_promptu.txt` file.

## 2. Core Principles for Add-on Design
*   **Clear Purpose:** Single, well-defined purpose.
*   **Explicit Instructions:** Clear, direct language for the Task AI.
*   **Modularity:** Self-contained. State dependencies (e.g., on Base IEP extensions).
*   **User-Centricity:** How does it make Task AI output more useful?
*   **Placeholder Usage:** Use placeholders like `[Path_To_Task_Prompts_Dir]`, `[Path_To_Submodule_Plans_Dir]`, `[Path_To_Task_Artifacts_Dir]`, and `<ExactOriginalTaskPromptFilenameBase>` which the Planning AI (specifically an orchestrator like `task_spawning_addon` guided by `core_planning_instructions.txt`) will resolve when embedding the add-on.

## 3. Steps to Develop a New Prompt Add-on

### Step 3.1: Define Purpose and Scope
*   Objective, Target AI (usually Task AI), Inputs, Outputs/Artifacts, Success Criteria.

### Step 3.2: Draft the Add-on Text
*   Start with `[[START OF 'YOUR_ADDON_NAME' ADD-ON vX.Y: Brief Description]]`.
*   Core instructions for the Task AI. Use imperative verbs.
*   **Interaction with Base IEP:** If your add-on requires Task AIs to log specific information, define structured tags for the `Notes-To-Next-Jules` field of the Base IEP. Document these tags clearly. Example: `[YOUR_ADDON_TAG]: Value`. The Base IEP definition is in `/promptu/core/base_iep.txt`.
*   End with `[[END OF 'YOUR_ADDON_NAME' ADD-ON vX.Y]]`.
*   **Save your add-on text** to a file, typically in a subdirectory like `/promptu/add_ons/your_addon_name/your_addon_name.txt` in this framework development repo.

### Step 3.3: Design Supporting Artifacts (if any)
*   Define structure/content of any files the Task AI creates due to your add-on.

### Step 3.4: Update Add-on Manifest
*   Add your new add-on's directory/file name and a brief description to `/promptu/add_ons/available_add_ons_manifest.md` in this framework development repo.

### Step 3.5: Create an Example Usage Guide for the Add-on
*   Explain what it does, show its text, and an example of a Task AI's output (e.g., commit message with new IEP tags, or a created file). Store in `/framework_dev_docs/examples/your_addon_name_example.md`.

### Step 3.6: Test and Refine
*   Test by having the Promptu framework (via `post_promptu.txt`) instruct a Planning AI to include your add-on (via user selection). Evaluate Task AI behavior.

## 4. Example: Task Resumption Add-on
(Refer to `/promptu/add_ons/task_resumption_addon/task_resumption_addon.txt` and `/framework_dev_docs/examples/task_resumption_example.md`)

## 5. Conclusion
Well-designed add-ons make the Promptu framework highly extensible and adaptable.
