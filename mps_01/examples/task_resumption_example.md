# Example Usage: Task Resumption Add-on

This document demonstrates how a user (or another AI) would instruct a Task AI (Jules) to utilize the "Task Resumption Add-on".

## 1. Scenario

A Task AI (Jules) is currently working on a complex task, for example, "Refactor the authentication module for Project Phoenix." The user wants to pause Jules's work and ensure that it can be resumed later with minimal context loss.

## 2. User's Prompt to Jules (Illustrative)

The user would append the `task_resumption_addon.txt` content to their specific task instruction for Jules.

```text
Hello Jules,

Your current task is to refactor the authentication module for Project Phoenix.
The key requirements are:
1.  Implement OAuth2 using the 'authlib' library.
2.  Ensure all existing unit tests for authentication still pass.
3.  Update the API documentation in `docs/api/auth.md`.
4.  The primary output directory for this task is `project_phoenix/auth_module_updates/`.

Before you conclude your current work session, or if I explicitly ask you to pause, you need to prepare for task resumption.

[[START OF 'TASK_RESUMPTION_ADDON' v0.1: Instructions for AI Task Management]]

**Purpose:** This add-on instructs the AI (Jules) to generate a detailed "Resumption Prompt" for the current task it is working on. This allows the current task to be paused and resumed efficiently by another AI instance (or the same AI at a later time) with minimal context loss.

**Instructions for AI (Jules):**

1.  **Identify Core Task Details:**
    *   Review your original prompt for this task. Identify and summarize:
        *   The overall goal of this task.
        *   The specific deliverables requested.
    *   Review your `_dev_plan.md` for this task. Note the last completed step and the next planned step(s).

2.  **Summarize Work Completed:**
    *   Consult your commit history for this task (as per the Base IEP).
    *   List the key actions you have taken and the files you have created or modified. Reference specific commit IDs where appropriate.

3.  **Specify Output Location for Resumption Prompt:**
    *   The Resumption Prompt should be saved in a file named `_resumption_prompt.txt` within the primary output directory designated for this task by the user (hereafter referred to as "Task Output Directory").
    *   If the user did not specify a Task Output Directory, save it in the current working directory of the task. (In this example, the user *did* specify: `project_phoenix/auth_module_updates/`)

4.  **Generate the Resumption Prompt Content:**
    Create the content for `_resumption_prompt.txt` with the following structure:

    ```text
    # Resumption Prompt for Task: [Original Task Name/Objective from Prompt]

    ## Original Task Goal:
    [Summary of the overall goal from original prompt]

    ## Original Task Deliverables:
    [List of specific deliverables from original prompt]

    ## Current Task State (as of [Timestamp]):

    ### Last Completed Step (from _dev_plan.md):
    [Quote the last completed step]

    ### Next Planned Step(s) (from _dev_plan.md):
    [Quote the next planned step or steps]

    ### Summary of Work Done So Far:
    [Bulleted list of key actions and files created/modified, with commit references if available. e.g.,
    - Created initial project structure (Commit: XXXXXX)
    - Implemented function Y in file Z.py (Commit: YYYYYY)
    - Encountered issue: [brief description of issue if any is blocking next step]
    ]

    ## Files Created/Modified in this Session (relative to project root):
    [List of file paths created or modified during your work session for this task]

    ## Next Steps for Resuming AI:
    1.  Review the above context thoroughly.
    2.  Focus on completing: "[Next Planned Step(s) from _dev_plan.md]"
    3.  Ensure all original task deliverables are met.
    4.  Continue to update `_dev_plan.md` and follow the Base IEP for commits.
    5.  If any blocking issues were noted, address them first.

    ## Original Full Prompt (for reference):
    [[[
    [Paste the full original prompt content here. Ensure it's clearly demarcated, perhaps by triple square brackets or similar, to avoid confusion with this resumption prompt's own instructions.]
    ]]]
    ```

5.  **Save the Resumption Prompt:**
    *   Write the content generated in Step 4 to the `_resumption_prompt.txt` file at the location determined in Step 3.

6.  **Update Commit Message (Base IEP Extension):**
    *   When you commit the creation or update of this `_resumption_prompt.txt`, you **must** extend your commit message as per the Base IEP.
    *   In the `Notes-To-Next-Jules:` field of your commit message, add the following structured tag:
        `[TASK_RESUMPTION_PROMPT_STATUS]: Updated_At='[Full path to _resumption_prompt.txt]'`
    *   Example `Notes-To-Next-Jules:` field:
        `Notes-To-Next-Jules: Standard notes here. [TASK_RESUMPTION_PROMPT_STATUS]: Updated_At='project_phoenix/auth_module_updates/_resumption_prompt.txt'`

7.  **Final Action:**
    *   After saving the `_resumption_prompt.txt` and committing it with the updated message, if your instructions were to "stop and create a resumption prompt", then cease further work on other aspects of the original task.

These instructions are critical for enabling efficient, stateful task handoff. Adhere to them precisely.

[[END OF 'TASK_RESUMPTION_ADDON' v0.1]]

Okay, Jules, please proceed with the refactoring task. If you reach a good stopping point, or if you complete the API documentation update (requirement 3), please create/update the resumption prompt and then pause your work on this task.
```

## 3. Expected AI (Jules) Behavior

1.  **Normal Task Execution:** Jules proceeds with the "Refactor the authentication module" task, making commits according to the `Base_IEP_Definition.md`.
2.  **Trigger for Resumption Prompt Creation:** When Jules completes the API documentation update (or another defined trigger point is met, or it's explicitly told to pause):
    *   It will follow the instructions within the `TASK_RESUMPTION_ADDON` block.
    *   It will create/update the file `project_phoenix/auth_module_updates/_resumption_prompt.txt`.
3.  **IEP Commit for Resumption Prompt:** The commit Jules makes when saving the `_resumption_prompt.txt` will look something like this:

    ```
    Task-ID: ProjectPhoenix-AuthRefactor-Main

    Status: InProgress

    Summary: Updated _resumption_prompt.txt with current task state.

    Description:
    Saved the current task status, including completed work on API documentation,
    to the resumption prompt as instructed by the TASK_RESUMPTION_ADDON.
    Preparing to pause work.

    Files-Changed:
    - project_phoenix/auth_module_updates/_resumption_prompt.txt [Modified]

    Notes-To-Next-Jules:
    Completed API documentation update. Next step is to verify OAuth2 flow with all grant types. [TASK_RESUMPTION_PROMPT_STATUS]: Updated_At='project_phoenix/auth_module_updates/_resumption_prompt.txt'
    ```
4.  **Pausing (if instructed):** Jules will then stop further work on the refactoring task.

## 4. Resuming the Task

When another Jules instance (or the same one later) is tasked to continue:
*   The primary instruction would be to consult the `project_phoenix/auth_module_updates/_resumption_prompt.txt`.
*   This prompt provides all necessary context to pick up the work.

This example illustrates how an add-on can be seamlessly integrated into a user's prompt to provide specialized instructions to an AI, leading to the creation of specific, useful artifacts that enhance the overall workflow.
