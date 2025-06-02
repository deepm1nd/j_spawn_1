# Example Structure for User's Main Spawn Prompt File
# (e.g., what the user would place in `/prompts/p_gritos_dev_plan_spawn.md` in the target repo)

This document illustrates the recommended structure for the main spawn prompt file that you, the user, will create in the target repository (e.g., in its `/prompts/` directory) and then instruct a "Planning AI" (e.g., a new Jules instance) to read and process.

The spawn prompt should be assembled in the following order:

## 1. User Path Configuration Block

This block tells the Planning AI where to place *its generated outputs* (like the detailed task prompts for your project, the project's `00_task_launch_plan.md`, etc.). Paths should be relative to the repository root where the Planning AI operates. Using `Main Iteration Folder: .` means outputs like `plan/` and `prompts/` folders will be created directly at the repository root.

\`\`\`text
[[USER PATH CONFIGURATION]]
Main Iteration Folder: .
Submodule Plan Destination: plan
Prompts Folder: prompts
Inter-AI Communication Folder: prompts/ipc
User-Specified Task Output Base Path: dev/src/kernel
[[END USER PATH CONFIGURATION]]
\`\`\`
*(Adjust the paths above, especially `User-Specified Task Output Base Path`, according to your specific project's needs and target directory structure.)*

## 2. Your Specific Project Request

This is the main description of what you want the Planning AI to plan. For our "gritos" test case, this would be the "gritos Dev Plan (a003)" text.

\`\`\`text
gritos Dev Plan (a003) - With gritos_product_specification.md and gritos_product_requirements_full.md as the primary source of information, develop a highly detailed development plan for this called gritos_dev_plan.md (the Plan).

Suggested Steps

Review documents in the concept/research folder of the repository as an input Review the documents in the docs folder as guidance
You may review other documents in the plan folder but they should only be used to enhance or improve a task, activity, or end product.
Perform deep research on modern OS architectures and specifically the OSes listed in the OS Kernal Comparison document. Include codebases referenced in the input documents in your research.
The /concept/inputs folder contains an exemplar development plan "grit_dev_plan.md" (and its subplans). Use it as the standard for the expected style, level of detail, and implementation guidance expected in the gritos_dev_plan.md document.

Present a plan to create gritos_dev_plan.md and highly detailed supporting subplans to the same level of detail and quality as the files in concept/inputs. Try to develop the plan such that it can be broken down into 2-5 phases with multiple tasks in each phase. The goal is to divide the tasks within each Phase such that they can be developed in parallel by multiple Jules instances.
\`\`\`
*(Replace the above gritos text with your actual high-level project request.)*

## 3. The Master Prompt Segment (MPS) Text

Append the full, unmodified content of the `updated_Master_Prompt_Segment.txt` (which this Jules instance will provide to you, from `/mps_01/updated_Master_Prompt_Segment.txt` in its development repo). This MPS text contains the detailed instructions for the Planning AI on *how* to process your request, generate phased plans, create task prompts, handle IEPs by reading your provided Base IEP from `/prompts/ipc/Base_IEP.txt`, incorporate add-ons by reading your provided add-on files from `/prompts/add_ons/`, etc.

**Important:** The version of the MPS text you append here should be the one that instructs the Planning AI to *read* the Base IEP and any add-ons from predefined paths within *your target repository's `/prompts/` structure* (e.g., from `/prompts/ipc/Base_IEP.txt` and `/prompts/add_ons/your_addon.txt`).

\`\`\`text
--------------------------------------------------------------------------------------------------------------
[[START OF MASTER PROMPT SEGMENT: AI Project Planning & Task Generation Instructions]]

ATTENTION PLANNING AI: Your primary function is to interpret the user's project request (provided immediately preceding this segment) and generate a comprehensive, phased execution plan...

(... Rest of the entire updated Master Prompt Segment text, as defined in `/mps_01/updated_Master_Prompt_Segment.txt` ...)

[[END OF MASTER PROMPT SEGMENT]]
\`\`\`

---

**Summary of Setup for Planning AI (by User):**

Before invoking the Planning AI with a prompt like `"/prompts/p_your_project_spawn_prompt.md is the prompt, it is in the repo"`:

1.  **Create your spawn prompt file** (e.g., `/prompts/p_your_project_spawn_prompt.md`) in the target repo, structured as shown above (User Path Config + Your Project Request + Updated MPS Text).
2.  **Place the Base IEP content** (provided by this Jules instance as `/mps_01/content_for_user_Base_IEP.txt`) into a file at the path referenced by the updated MPS (e.g., `/prompts/ipc/Base_IEP.txt` in the target repo).
3.  **Place any referenced Add-on texts** (e.g., content from `/mps_01/content_for_user_task_resumption_addon.txt`) into files at paths referenced by the updated MPS (e.g., `/prompts/add_ons/task_resumption_addon.txt` in the target repo).
4.  **Ensure all other input documents** required by "Your Specific Project Request" (e.g., product specifications, research docs for gritos) are present in the target repo at the locations your project request section refers to.

The Planning AI will then use your spawn prompt to understand its tasks, read the Base IEP and add-ons from the locations you set up, and generate its outputs (the target project's detailed plan, task prompts, etc.) into the directories specified in your `[[USER PATH CONFIGURATION]]` block.
