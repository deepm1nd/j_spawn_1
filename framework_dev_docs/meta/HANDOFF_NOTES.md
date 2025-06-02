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
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Actions:**
- Modularized `prompts/Master_Prompt_Segment.txt` by extracting its core planning instructions.
- Created `prompts/iep/Core_Planning_Instructions.txt` to house these extracted instructions.
- Updated `prompts/Master_Prompt_Segment.txt` to use an `[[INCLUDE Core_Planning_Instructions.txt FROM /prompts/iep/Core_Planning_Instructions.txt]]` directive to load the core instructions.
- Updated `framework_dev_docs/guides/MPS_Usage_Guide.md` to reflect these structural changes and provide updated instructions for users on preparing their target repositories.
**State of Deliverables:** Modularization of `Master_Prompt_Segment.txt` is complete. The `MPS_Usage_Guide.md` is updated. Framework is ready for testing of this new structure or for further refinement.
**Suggestions for Next Steps/Instance:**
- Test the new modular MPS structure by having a Planning AI use it for a sample project.
- Review the clarity of the `[[INCLUDE ...]]` directive and its processing requirements for the Planning AI.
- Consider if other sections of `Master_Prompt_Segment.txt` (if any more are added later) or other core prompt files could benefit from similar modularization.
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
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request to Jules instance for refactoring).
**MPS Version Referenced:** `prompts/Master_Prompt_Segment.txt` (approx. v0.3.2, prior to modularization) and the new modularized version (post this session's changes).
**Context/Scenario:** User identified that the `Master_Prompt_Segment.txt` was becoming very long and dense, making it difficult to read, manage, and maintain.
**Observation/Issue:** The monolithic nature of `Master_Prompt_Segment.txt` was impacting usability and the ease of making targeted changes to core planning instructions without affecting user-configurable sections.
**Suggestion for MPS Refinement (Implemented):** To improve readability and maintainability, the core planning AI instructions were extracted from `Master_Prompt_Segment.txt` and placed into a new, dedicated file: `prompts/iep/Core_Planning_Instructions.txt`. The `Master_Prompt_Segment.txt` now includes these instructions using an `[[INCLUDE Core_Planning_Instructions.txt FROM /prompts/iep/Core_Planning_Instructions.txt]]` directive. This separation is intended to make both the user-facing configuration parts of the MPS and the core instructional logic easier to manage.
---
