# Example: User's Main Project Instruction File for "gritos" Project
# (e.g., what the user would create as `/promptu/p_gritos_main_post_promptu.txt` in their gritos target repo)

This document illustrates the structure for the main project instruction file (based on `post_promptu.txt`) that you, the user, create in your target repository. You then instruct a "Planning AI" (e.g., a new Jules instance) to read and process this file.

The Planning AI will use this file to generate a detailed project plan, task prompts, etc., for the "gritos" project, guided by the Promptu framework.

**User Setup in Target Repo Before Running Planning AI:**
1.  **Input Docs:** Ensure all gritos-specific input documents (`gritos_product_specification.md`, `concept/research/*`, etc.) are in the target repo at paths referenced by the "gritos Dev Plan (a003)" request below.
2.  **Core Framework Files:**
    *   Copy this framework's `/promptu/core/core_planning_instructions.txt` to `target_repo/promptu/core/core_planning_instructions.txt`.
    *   Copy `/promptu/core/base_iep.txt` to `target_repo/promptu/core/base_iep.txt`.
    *   Copy `/promptu/post_promptu.txt` (template) to `target_repo/promptu/p_gritos_main_post_promptu.txt` (this will be your main project instruction file).
    *   Optionally, copy `/promptu/pre_promptu.txt` if you intend to use pre-processing utilities.
3.  **Add-ons & Utils:** Create `target_repo/promptu/add_ons/` and `target_repo/promptu/util/`. Copy desired add-ons (e.g., entire `task_resumption_addon` folder from this framework's `/promptu/add_ons/task_resumption_addon/`) and utils. Consult this framework's `/promptu/add_ons/available_add_ons_manifest.md` for options.

**Structure of Your Project Instruction File (e.g., `target_repo/promptu/p_gritos_main_post_promptu.txt`):**

The file should contain, in order:

**A. Your Specific Project Request (e.g., for gritos - place this at the very top of your copied `post_promptu.txt` content):**
\`\`\`text
[[USER_PROJECT_REQUEST]]
gritos Dev Plan (a003) - With gritos_product_specification.md and gritos_product_requirements_full.md as the primary source of information, develop a highly detailed development plan for this called gritos_dev_plan.md (the Plan).

Suggested Steps
Review documents in the concept/research folder of the repository as an input Review the documents in the docs folder as guidance
You may review other documents in the plan folder but they should only be used to enhance or improve a task, activity, or end product.
Perform deep research on modern OS architectures and specifically the OSes listed in the OS Kernal Comparison document. Include codebases referenced in the input documents in your research.
The /concept/inputs folder contains an exemplar development plan "grit_dev_plan.md" (and its subplans). Use it as the standard for the expected style, level of detail, and implementation guidance expected in the gritos_dev_plan.md document.

Present a plan to create gritos_dev_plan.md and highly detailed supporting subplans to the same level of detail and quality as the files in concept/inputs. Try to develop the plan such that it can be broken down into 2-5 phases with multiple tasks in each phase. The goal is to divide the tasks within each Phase such that they can be developed in parallel by multiple Jules instances.
[[END_USER_PROJECT_REQUEST]]
\`\`\`

**B. The Rest of Your `post_promptu.txt` Content (Copied from the Template and Edited):**
(This content, starting with `[[USER_APP_SELECTION]]` or other initial framework blocks, follows immediately after your project request above.)

Within your `p_gritos_main_post_promptu.txt` file, you will edit blocks like:
*   `[[USER_APP_SELECTION]]` (if using a promptApp)
*   `[[USER_ADDON_SELECTION]]` (to select add-ons like `task_spawning_addon`, `task_resumption_addon`). Example:
    \`\`\`text
    [[USER_ADDON_SELECTION]]
    # Instructions for user:
    # - If no promptApp is selected...
    # - Mark selection with '[x]'...
    [x] task_spawning_addon
    [x] task_resumption_addon
    [[END_USER_ADDON_SELECTION]]
    \`\`\`
*   `[[USER_FRAMEWORK_SETTINGS]]`
*   `[[USER_CONFIG_FOR_componentName]]` blocks for any selected components that require specific parameter overrides. For instance, to control where `task_spawning_addon` places its outputs for the gritos project, you would include and edit a `[[USER_CONFIG_FOR_task_spawning_addon]]` block:
    \`\`\`text
    [[USER_CONFIG_FOR_task_spawning_addon]]
    # Requirement: Required | DefaultType: Placeholder
    # Description: User-defined base directory for all outputs...
    # Default: outputs/default_spawn_run/
    BASE_OUTPUT_PATH: [USER_VALUE_START] outputs/gritos_run01/ [USER_VALUE_END]

    # Requirement: Required | DefaultType: Accepted
    # Description: Filename for the main plan document...
    # Default: main_development_plan.md
    MAIN_DEV_PLAN_FILENAME: [USER_VALUE_START] gritos_dev_plan.md [USER_VALUE_END]

    # ... (other parameters for task_spawning_addon as needed) ...
    [[END_USER_CONFIG_FOR_task_spawning_addon]]
    \`\`\`
*   The `post_promptu.txt` template itself includes the line `[[INCLUDE core_planning_instructions.txt FROM /promptu/core/core_planning_instructions.txt]]`, which loads the main framework logic.

---
After creating and configuring your `p_gritos_main_post_promptu.txt` file, invoke the Planning AI with a directive pointing to it:
`"[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /promptu/p_gritos_main_post_promptu.txt]]"`
(This directive is your actual prompt to the AI system.)
