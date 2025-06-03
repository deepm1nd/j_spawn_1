# Example Usage: Task Resumption Add-on

This document demonstrates how the "Task Resumption Add-on" (source text at `/promptu/add_ons/task_resumption_addon/task_resumption_addon.txt` in the framework repo) would be used.

## 1. Scenario
A Planning AI, guided by `/promptu/post_promptu.txt` (which itself was configured by the user to include the Task Resumption Add-on via `[[USER_ADDON_SELECTION]]`), generates a task prompt for "Implement User Auth API". The Planning AI's `task_spawning_addon` is configured with output paths, for example, task prompts going to `outputs/run1/planning_files/` and submodule plans to `outputs/run1/task_execution_plans/`. Let's assume `[Path_To_Task_Prompts_Dir]` resolves to `outputs/run1/planning_files/` and `[Path_To_Submodule_Plans_Dir]` resolves to `outputs/run1/task_execution_plans/`.

**Generated Task Prompt (`target_repo/[Path_To_Task_Prompts_Dir]/p2_t1_auth_api_impl.txt` might look like this):**
\`\`\`text
Hello Jules,

Your task is to implement the User Authentication API Endpoints...
... (original task instructions) ...

Remember to create your `p2_t1_auth_api_impl_dev_plan.md` and `next_steps.md` in `[Path_To_Submodule_Plans_Dir]/` (e.g. `outputs/run1/task_execution_plans/`).
Adhere to `information_exchange_protocol.md` (from `[Path_To_Task_Prompts_Dir]/information_exchange_protocol.md`).
Your outputs go into `[Path_To_Task_Artifacts_Dir]/p2_t1_auth_api_impl/` (e.g. `outputs/run1/task_output_artifacts/p2_t1_auth_api_impl/`).

[[START OF 'TASK_RESUMPTION_ADDON' v0.1: Instructions for AI Task Management]]
... (full text of the task_resumption_addon.txt, with placeholders like
    `[Actual Path to Prompts Folder]` resolved by Planning AI to `[Path_To_Task_Prompts_Dir]`,
    `[Actual Path to Submodule Plan Destination]` resolved to `[Path_To_Submodule_Plans_Dir]`,
    `[Actual User-Specified Task Output Base Path]` resolved to `[Path_To_Task_Artifacts_Dir]`,
    `<ExactOriginalTaskPromptFilenameBase>` resolved to `p2_t1_auth_api_impl`,
    `<YourPhaseTaskName>` resolved to `p2_t1_auth_api_impl`
) ...
[[END OF 'TASK_RESUMPTION_ADDON' v0.1]]
\`\`\`

## 2. Task AI Execution
The Task AI receiving this prompt would:
1.  Create `[Path_To_Submodule_Plans_Dir]/p2_t1_auth_api_impl_dev_plan.md`.
2.  **As per the add-on:** Create an initial `[Path_To_Submodule_Plans_Dir]/p2_t1_auth_api_impl_resumption_prompt.txt`.
3.  Make a commit. The commit message would include in `Notes-To-Next-Jules`:
    `[TASK_RESUMPTION_PROMPT_STATUS]: Updated_At='[Path_To_Submodule_Plans_Dir]/p2_t1_auth_api_impl_resumption_prompt.txt'`
4.  Proceed with task...
