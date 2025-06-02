# Base Information Exchange Protocol (Base IEP) Definition v0.1

This document defines the *minimum required fields* for a commit message when a Jules AI instance performs work. Specific tasks or add-ons may require additional structured data to be included in the `Notes-To-Next-Jules:` field.

## Core Commit Message Structure:

When Jules makes a commit, the commit message **must** adhere to the following multi-line format:

```
Task-ID: [Unique ID of the task being worked on, e.g., "ProjectAlpha-Phase1-Task3"]

Status: [Simple status, e.g., "Completed", "InProgress", "Blocked", "Error"]

Summary: [One-line concise summary of the changes made in this commit.]

Description:
[Optional: More detailed explanation of changes if the summary is insufficient. Can be multiple lines. Focus on *what* was changed and *why*.]

Files-Changed:
- [path/to/file1.ext] [Created|Modified|Deleted]
- [path/to/file2.ext] [Created|Modified|Deleted]
- [path/to/directory/] [Created|Deleted]

Notes-To-Next-Jules:
[Any critical information, observations, warnings, or structured data tags (from add-ons) that the next Jules instance picking up this task or a related task needs to know. If no special notes, write "None." This field is where add-ons will often specify their required tags.]

```

## Field Explanations:

*   **`Task-ID:`**: The identifier for the specific task Jules was assigned. This helps in tracking and auditing.
*   **`Status:`**: A brief indicator of the outcome of the work performed in this commit.
    *   `Completed`: The specific sub-task or action leading to this commit was finished successfully.
    *   `InProgress`: The work is ongoing; this commit represents a checkpoint.
    *   `Blocked`: Jules cannot proceed with this task due to a specific issue (should be explained in `Notes-To-Next-Jules:`).
    *   `Error`: An error occurred during the execution that prevented completion (should be explained).
*   **`Summary:`**: A short, clear summary of what was achieved in the commit.
*   **`Description:`**: (Optional) Used if the summary isn't enough to explain the changes.
*   **`Files-Changed:`**: A list of files and directories directly affected by the commit, along with the nature of the change (Created, Modified, Deleted). Paths should be relative to the project root.
*   **`Notes-To-Next-Jules:`**: This is a crucial field for inter-AI communication and for add-on functionality.
    *   It should contain any context that would be necessary for another AI (or human) to pick up the work.
    *   **Add-ons will often specify required structured tags here.** For example, an add-on might require `[ADDON_X_METADATA]: <value>`. These tags should be appended to any existing notes in this field.

## Importance of Adherence:

Strict adherence to this Base IEP is vital for:
*   **Automated Processing:** Tools may parse these commit messages.
*   **Inter-AI Handoff:** Ensuring the next AI has the context it needs.
*   **Auditing and Debugging:** Understanding the history of changes.

This Base IEP is designed to be minimal. Task-specific instructions or more comprehensive MPS frameworks may build upon this by requiring additional structured data within the `Notes-To-Next-Jules:` field.
