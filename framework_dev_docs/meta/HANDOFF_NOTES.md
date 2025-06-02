# HANDOFF NOTES for MPS Framework Development Task

This file is intended for use by Jules AI instances working on the iterative development and refinement of the Master Prompt Segment (MPS) framework. All canonical framework files are now located under `/prompts/` (for AI-usable content sources) and `/framework_dev_docs/` (for guides, examples, and these meta-development files).

Each Jules instance concluding a work session on this task should append notes below, detailing:
- User feedback/requests addressed during its session.
- A summary of changes made to the MPS framework documents.
- The state in which deliverables were left (e.g., specific commits, branches).
- Any pending items or suggestions for the next Jules instance.

---
**Session Summary - 2023-11-02**
**Instance:** Jules (this instance)
**Key Actions:**
-   MAJOR RESTRUCTURING: Consolidated all MPS framework development from previous `/mps_01/`, `/mps_02/` and root `/prompts/util/` directories into a new canonical structure:
    -   `/prompts/` now holds source texts for AI-usable assets (`Master_Prompt_Segment.txt`, `iep/Base_IEP.txt`, `add_ons/*`, `util/*`, `tasks/00_task_launch_plan.md` placeholder).
    -   `/framework_dev_docs/` now holds all guides, examples, and meta-development files for the framework.
-   The canonical `prompts/Master_Prompt_Segment.txt` has been updated to the latest design (effectively v0.3.2). This version includes 8 core features:
    1.  Internal `[[USER PATH CONFIGURATION]]` block.
    2.  Internal `[[USER_ADDON_SELECTION]]` block (user uses `[x] addon_file.txt`).
    3.  Planning AI reads Base IEP & Addons from user-provided paths in target repo (e.g., `/prompts/iep/Base_IEP.txt`, `/prompts/add_ons/`).
    4.  Correct guidance for Planning AI's output to its *own* `prompts/tasks/` dir.
    5.  Automated Dependency Linkage in TLP.
    6.  Estimated Time/Complexity in TLP.
    7.  Dynamic Risk Assessment directive (IEP v0.2 supports this).
    8.  Knowledge Capture in `_next_steps.md`.
    9.  Automated Sanity Checks by Planning AI.
    10. Planning AI Confidence Score reporting.
    11. Visual Plan Overview in TLP.
-   `prompts/iep/Base_IEP.txt` content is v0.2 (with Risk Assessment example).
-   All guides and examples in `/framework_dev_docs/` updated to reflect new paths and MPS functionality.
-   Old `/mps_01/` and `/mps_02/` directories were deleted.
-   All changes committed to new branch `feat/canonical-mps-framework-v1.0`.
**State of Deliverables:** Canonical framework v1.0 is established. Ready for user to prepare target repo for gritos test using these files, or for further feature design on these canonical files.
**Next Steps (User):** Prepare target repo for gritos test by:
    1. Creating a main spawn prompt (e.g., `target_repo/prompts/p_gritos_spawn.md`) using `framework_dev_docs/guides/p_gritos_dev_plan_spawn_EXAMPLE.md` as a guide and embedding content from `prompts/Master_Prompt_Segment.txt`.
    2. Editing `[[USER PATH CONFIGURATION]]` and `[[USER_ADDON_SELECTION]]` blocks within their spawn prompt's MPS section.
    3. Copying content from `prompts/iep/Base_IEP.txt` to `target_repo/prompts/iep/Base_IEP.txt`.
    4. Copying content from `prompts/add_ons/*` to `target_repo/prompts/add_ons/*`.
    5. Ensuring gritos input files are in target repo.
    6. Launching new Jules (Planning AI) with `"/prompts/p_gritos_spawn.md is the prompt..."`.
**Next Steps (This Jules instance/session):** Await user action or conclude session.
---

## MPS Performance Feedback Log

This section logs specific feedback on the performance, clarity, and effectiveness of the Master Prompt Segment (MPS) versions and the artifacts they help generate.

---
**Feedback Entry - 2023-11-02**
**Source of Feedback:** Initial setup of this feedback log section during MPS feature design.
**MPS Version Referenced:** Relevant for `prompts/Master_Prompt_Segment.txt` (v0.3.2 and future versions).
**Context/Scenario:** Establishing the feedback mechanism as per design discussions for feature "Feedback Loop for MPS Refinement".
**Observation/Issue:** This log is intended to capture issues like:
    - Ambiguities in any version of `Master_Prompt_Segment.md` that lead to incorrect or suboptimal behavior by a Planning AI.
    - Problems encountered by Task AIs due to the structure or content of prompts generated based on an MPS version.
    - User difficulties in understanding or using the MPS, its generated `00_task_launch_plan.md`, or other artifacts.
    - Success stories where a particular MPS instruction proved very effective.
**Suggestion for MPS Refinement (if any):** N/A
---
*(Future entries will be appended below this line)*
---
