# Example: User's Main Spawn Prompt (`p_gritos_dev_plan_spawn.md`)

This document illustrates the recommended structure for the main spawn prompt file that you, the user, would create in your target project's repository (e.g., in its `/prompts/` directory). You then instruct a "Planning AI" (e.g., a new Jules instance) to read and process this file.

The spawn prompt file should be assembled by you in the following order:

## 1. Your Specific Project Request
This is the main description of what you want the Planning AI to plan. For this example, we'll use the "gritos Dev Plan (a003)" text.

```text
gritos Dev Plan (a003) - With gritos_product_specification.md and gritos_product_requirements_full.md as the primary source of information, develop a highly detailed development plan for this called gritos_dev_plan.md (the Plan).

Suggested Steps

Review documents in the concept/research folder of the repository as an input Review the documents in the docs folder as guidance
You may review other documents in the plan folder but they should only be used to enhance or improve a task, activity, or end product.
Perform deep research on modern OS architectures and specifically the OSes listed in the OS Kernal Comparison document. Include codebases referenced in the input documents in your research.
The /concept/inputs folder contains an exemplar development plan "grit_dev_plan.md" (and its subplans). Use it as the standard for the expected style, level of detail, and implementation guidance expected in the gritos_dev_plan.md document.

Present a plan to create gritos_dev_plan.md and highly detailed supporting subplans to the same level of detail and quality as the files in concept/inputs. Try to develop the plan such that it can be broken down into 2-5 phases with multiple tasks in each phase. The goal is to divide the tasks within each Phase such that they can be developed in parallel by multiple Jules instances.
```
*(Replace the above gritos text with your actual high-level project request.)*

## 2. (Optional) User Add-on Selection Block
If you want the Planning AI to incorporate specific add-ons into the task prompts it generates, include this block. List the add-on filenames (which must exist in `/prompts/add_ons/` in your target repository).

```text
[[USER_ADDON_SELECTION]]
[x] task_resumption_addon.txt
# [ ] another_addon_if_needed.txt
[[END USER_ADDON_SELECTION]]
```

## 3. The Master Prompt Segment (MPS) Text
Append the full, unmodified content of the canonical `/prompts/Master_Prompt_Segment.md` here.
**Crucially, you must edit the `[[USER PATH CONFIGURATION]]` block that appears at the VERY TOP of the Master_Prompt_Segment.md text itself.** This configuration block within the MPS tells the Planning AI where to place *its* generated outputs for *your* project.

Example of how the beginning of the appended MPS text would look after your edits to its internal `[[USER PATH CONFIGURATION]]` block:
```text
--------------------------------------------------------------------------------------------------------------
[[USER PATH CONFIGURATION]]
# Instructions for User: Edit the paths below before using this MPS.
# - Main Iteration Folder: Where the Planning AI will place all its outputs for the target project.
#   Use '.' for the repository root, or a relative path like 'project_X_outputs'.
# - Prompts Folder: Subdirectory for generated task prompts, TLP, IEP. Relative to Main Iteration Folder.
# - Submodule Plan Destination: Directory for main project plan (e.g., gritos_dev_plan.md) and Task AI _dev_plan.md files. Relative to Main Iteration Folder.
# - Inter-AI Communication Folder: Subdirectory for IPC files. Typically 'prompts/ipc'. Relative to Main Iteration Folder.
# - User-Specified Task Output Base Path: The root path where Task AIs will place their primary deliverables (e.g., code). Relative to repo root.

Main Iteration Folder: . # Example: Target repo root for this planning run's outputs
Prompts Folder: gritos_generated_prompts # Example: Planning AI will create this for TLP, task prompts
Submodule Plan Destination: gritos_project_plans # Example: Planning AI will create this for main plan & _dev_plans
Inter-AI Communication Folder: gritos_generated_prompts/ipc # Example: Planning AI will create this
User-Specified Task Output Base Path: dev/src/gritos_kernel # Example: For gritos code
[[END USER PATH CONFIGURATION]]

--------------------------------------------------------------------------------------------------------------
[[START OF MASTER PROMPT SEGMENT (v0.3.1 - Path Config Inside MPS, Configurable Add-on Stack, 7 Features): AI Project Planning & Task Generation Instructions]]

ATTENTION PLANNING AI: Your primary function is to interpret the user's project request (provided by the user *before* this Master Prompt Segment begins) and then to follow the instructions within this segment to generate a comprehensive, phased execution plan...

(... Rest of the entire Master Prompt Segment text ...)

[[END OF MASTER PROMPT SEGMENT (v0.3.1 - Path Config Inside MPS, Configurable Add-on Stack, 7 Features)]]
```

---

**Summary of Setup in Your Target Repository (by User):**

Before invoking the Planning AI on your spawn prompt file (e.g., `"/prompts/p_gritos_dev_plan_spawn.md is the prompt, it is in the repo"`):

1.  **Create your spawn prompt file** (e.g., `/prompts/p_gritos_dev_plan_spawn.md`) in your target repo, structured as shown above (Your Project Request + Optional Add-on Selection + MPS content with its internal Path Config edited).
2.  **Place Base IEP Content:** Copy the content of the canonical Base IEP (from framework's `/prompts/iep/Base_IEP.txt`) into a file at `/prompts/iep/Base_IEP.txt` in your target repo. The Planning AI will read this.
3.  **Place Add-on Content:** For each add-on you selected in `[[USER_ADDON_SELECTION]]`, copy its definition text (e.g., from framework's `/prompts/add_ons/task_resumption_addon.txt`) into a corresponding file in `/prompts/add_ons/` in your target repo (e.g., `/prompts/add_ons/task_resumption_addon.txt`). The Planning AI will read these.
4.  **Ensure Input Docs Exist:** Make sure all other input documents your project request refers to (e.g., product specifications for gritos) are present in your target repo at the specified locations.

The Planning AI will then use your spawn prompt file to:
- Understand your project request.
- Parse the `[[USER_ADDON_SELECTION]]` block (if you included it).
- Read the MPS instructions you appended.
- From within the MPS, parse the `[[USER PATH CONFIGURATION]]` you edited.
- Read the Base IEP and selected Add-on files from your target repository's `/prompts/` subdirectories.
- Generate its outputs (the detailed project plan for your project, task prompts, TLP, etc.) into the directories you defined in the `[[USER PATH CONFIGURATION]]` block within the MPS.
