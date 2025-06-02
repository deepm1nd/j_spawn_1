# Example: User's Main Spawn Prompt (`p_gritos_dev_plan_spawn.md`)

This document illustrates the recommended structure for the main spawn prompt file that you, the user, would create in your target project's repository (e.g., in its `/prompts/` directory). You then instruct a "Planning AI" (e.g., a new Jules instance) to read and process this file.

The Planning AI will use this spawn prompt to generate a detailed project plan, task prompts, etc., for the "gritos" project.

**User Setup in Target Repo Before Running Planning AI:**
1.  **Input Docs:** Ensure all gritos-specific input documents (`gritos_product_specification.md`, `concept/research/*`, etc.) are in the target repo at paths referenced by the "gritos Dev Plan (a003)" request below.
2.  **Base IEP:** Create `/prompts/iep/Base_IEP.txt` in the target repo. Copy the content from this framework's `/prompts/iep/Base_IEP.txt` into it.
3.  **Add-ons:** Create `/prompts/add_ons/` in the target repo. Copy desired add-on files (e.g., `task_resumption_addon.txt` from this framework's `/prompts/add_ons/`) into it. Consult this framework's `/prompts/add_ons/available_addons_manifest.md` for options.

**Structure of Your Spawn Prompt File (e.g., `target_repo/prompts/p_gritos_dev_plan_spawn.md`):**

The file should contain, in order:

**A. Your Specific Project Request (e.g., for gritos):**
\`\`\`text
gritos Dev Plan (a003) - With gritos_product_specification.md and gritos_product_requirements_full.md as the primary source of information, develop a highly detailed development plan for this called gritos_dev_plan.md (the Plan).

Suggested Steps

Review documents in the concept/research folder of the repository as an input Review the documents in the docs folder as guidance
You may review other documents in the plan folder but they should only be used to enhance or improve a task, activity, or end product.
Perform deep research on modern OS architectures and specifically the OSes listed in the OS Kernal Comparison document. Include codebases referenced in the input documents in your research.
The /concept/inputs folder contains an exemplar development plan "grit_dev_plan.md" (and its subplans). Use it as the standard for the expected style, level of detail, and implementation guidance expected in the gritos_dev_plan.md document.

Present a plan to create gritos_dev_plan.md and highly detailed supporting subplans to the same level of detail and quality as the files in concept/inputs. Try to develop the plan such that it can be broken down into 2-5 phases with multiple tasks in each phase. The goal is to divide the tasks within each Phase such that they can be developed in parallel by multiple Jules instances.
\`\`\`

**B. The Full Content of the Canonical Master Prompt Segment (from this framework's `/prompts/Master_Prompt_Segment.md`):**
(Copy the entire content and paste it here. **Then, you MUST edit the `[[USER PATH CONFIGURATION]]` and `[[USER_ADDON_SELECTION]]` blocks at its top** to define where the Planning AI should put its outputs for THIS gritos project and which add-ons to use.)

Example of how the top of the pasted MPS content would look *after your edits*:
\`\`\`text
[[USER PATH CONFIGURATION]]
# Instructions for User: Edit the paths below before using this MPS.
# ... (comments from original MPS) ...
Main Iteration Folder: .
Prompts Folder: prompts/tasks
Submodule Plan Destination: plan
Inter-AI Communication Folder: prompts/tasks/ipc
User-Specified Task Output Base Path: dev/src/kernel
[[END USER PATH CONFIGURATION]]

[[USER_ADDON_SELECTION]]
# This block is now part of the MPS and should be edited here by the user.
# The Planning AI will parse this block from within the MPS text.
[x] task_resumption_addon.txt
[[END USER_ADDON_SELECTION]]

--------------------------------------------------------------------------------------------------------------
[[START OF MASTER PROMPT SEGMENT (v0.3.2 - Canonical Paths, Internal Configs, 8 Features): AI Project Planning & Task Generation Instructions]]

ATTENTION PLANNING AI: Your primary function is to interpret the user's project request (provided by the user *before* this Master Prompt Segment begins) and then to follow the instructions within this segment...

(... rest of the entire Master Prompt Segment text ...)

[[END OF MASTER PROMPT SEGMENT (v0.3.2 - Canonical Paths, Internal Configs, 8 Features)]]
\`\`\`

---
After creating this spawn prompt file in your target repo (e.g., `target_repo/prompts/p_gritos_dev_plan_spawn.md`), you invoke the Planning AI with:
`"/prompts/p_gritos_dev_plan_spawn.md is the prompt, it is in the repo"`
