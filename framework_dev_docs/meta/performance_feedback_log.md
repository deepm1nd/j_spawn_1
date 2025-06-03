# Promptu Performance Feedback Log

## Entry Format Definition
Each entry in this log should conform to the following Markdown structure, using key-value pairs. Aim for conciseness, using single lines for values where possible. The 'Details' field is optional.

---
Date: YYYY-MM-DD
Source: User Request | AI Observation | Component Test | Framework Issue
Version: component_name@version_or_date | core_planning_instructions@YYYY-MM-DD
Context: Brief, one-line description of the situation, task, or component interaction being observed.
Observation: One-line summary of what was observed or the issue identified.
Details: (Optional) Multi-line, more granular details if the one-line Observation is insufficient. Omit if not needed.
Suggestion: One-line summary of the implemented solution, proposed refinement, or next step.
Status: Implemented | Proposed | For Review | Deferred | No Action Needed
---

<!-- New log entries will be appended below this line -->
---
Date: 2025-06-07
Source: Framework Development
Version: core_planning_instructions@2025-06-07
Context: Initializing the new performance feedback logging system.
Observation: The previous method of logging performance feedback within the verbose `handoff_notes_full_archive_20250602.md` was prone to file bloat and lacked configurability. The archive was also incorrectly modified in a previous session.
Details: This new system introduces a separate log file (`performance_feedback_log.md`), a user-configurable setting (`log_performance_feedback` in `[[USER_FRAMEWORK_SETTINGS]]`), and a standardized, more concise Markdown entry format to address these issues. `core_planning_instructions.txt` updated to enforce new logging rules and prohibit modification of the old archive.
Suggestion: This new system should improve the manageability and utility of performance feedback.
Status: Implemented
---
