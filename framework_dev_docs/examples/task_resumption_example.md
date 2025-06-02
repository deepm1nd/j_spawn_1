# Example Usage: Task Resumption Add-on

This document demonstrates how the "Task Resumption Add-on" (source text at `/prompts/add_ons/task_resumption_addon.txt` in the framework repo) would be used.

## 1. Scenario
A Planning AI, guided by `/prompts/Master_Prompt_Segment.txt` (which itself was configured by the user to include the Task Resumption Add-on via `[[USER_ADDON_SELECTION]]`), generates a task prompt for "Implement User Auth API". The Planning AI is also configured with `Prompts Folder: prompts/tasks` and `Submodule Plan Destination: plan`.

**Generated Task Prompt (`target_repo/prompts/tasks/p2_t1_auth_api_impl.txt` might look like this):**
\`\`\`text
Hello Jules,

Your task is to implement the User Authentication API Endpoints...
... (original task instructions) ...

Remember to create your `p2_t1_auth_api_impl_dev_plan.md` and `_next_steps.md` in `plan/` (assuming `Submodule Plan Destination: plan` was set by user).
Adhere to `Information_Exchange_Protocol.md` (from `prompts/tasks/Information_Exchange_Protocol.md`).
Your outputs go into `dev/src/kernel/p2_t1_auth_api_impl/`.

[[START OF 'TASK_RESUMPTION_ADDON' v0.1: Instructions for AI Task Management]]
... (full text of the task_resumption_addon.txt, with placeholders like
    `[Actual Path to Prompts Folder]` resolved by Planning AI to `prompts/tasks`,
    `[Actual Path to Submodule Plan Destination]` resolved to `plan`,
    `[Actual User-Specified Task Output Base Path]` resolved to `dev/src/kernel`,
    `<ExactOriginalTaskPromptFilenameBase>` resolved to `p2_t1_auth_api_impl`,
    `<YourPhaseTaskName>` resolved to `p2_t1_auth_api_impl`
) ...
[[END OF 'TASK_RESUMPTION_ADDON' v0.1]]
\`\`\`

## 2. Task AI Execution
The Task AI receiving this prompt would:
1.  Create `plan/p2_t1_auth_api_impl_dev_plan.md`.
2.  **As per the add-on:** Create an initial `plan/p2_t1_auth_api_impl_resumption_prompt.txt`.
3.  Make a commit. The commit message would include in `Notes-To-Next-Jules`:
    `[TASK_RESUMPTION_PROMPT_STATUS]: Updated_At='plan/p2_t1_auth_api_impl_resumption_prompt.txt'`
4.  Proceed with task...
