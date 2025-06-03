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
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Actions:**
- Completed a significant refactor to make the core task spawning/project planning functionality optional and add-on driven.
- **Created `prompts/add_ons/task_spawning_addon.txt`**: This new add-on now houses the detailed instructions for generating a main development plan, Task Launch Plan (TLP), and individual task prompts. This logic was moved from `prompts/iep/Core_Planning_Instructions.txt`.
- **Updated `prompts/iep/Core_Planning_Instructions.txt`**: This file now focuses on foundational framework logic: parsing user configurations (`[[USER PATH CONFIGURATION]]`, `[[USER_ADDON_SELECTION]]`), managing the add-on list, and implementing conditional logic to execute `task_spawning_addon.txt` (or other primary add-ons) if selected. It also handles utility outputs like `Information_Exchange_Protocol.md`.
- **Modified `prompts/Master_Prompt_Segment.txt`**: Added `task_spawning_addon.txt` to the `[[USER_ADDON_SELECTION]]` block as a user-selectable option, clarifying its role.
- **Updated `prompts/add_ons/available_addons_manifest.md`**: Added `task_spawning_addon.txt` with a description of its function and importance.
- **Updated `framework_dev_docs/guides/MPS_Usage_Guide.md`**: Extensively revised the guide to explain the new optional task spawning mechanism, how to enable it, and the AI's behavior with and without `task_spawning_addon.txt` selected.
**State of Deliverables:** The MPS framework now features optional, add-on-driven task spawning, significantly increasing its modularity and flexibility. Core logic for this is in place, and documentation has been updated. Ready for testing of various add-on combinations.
**Suggestions for Next Steps/Instance:**
- Thoroughly test the new conditional logic with `task_spawning_addon.txt` selected and not selected.
- Test with combinations of `task_spawning_addon.txt` and other utility add-ons.
- Evaluate if the instructions in `Core_Planning_Instructions.txt` regarding behavior *without* `task_spawning_addon.txt` (and no other primary add-on) are sufficiently clear and lead to sensible AI actions.
- Consider if any further logic from `Core_Planning_Instructions.txt` can be either minimized or moved to more specialized utility add-ons.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Actions:**
- Addressed user queries regarding add-on interactions, focusing on ensuring `task_spawning_addon.txt` is not inherited by Task AIs and clarifying add-on ordering for users.
- **Refined `prompts/iep/Core_Planning_Instructions.txt` (Section I.B)**: Modified the add-on parsing logic to create two distinct collections:
    - `All_Selected_Addons_Content_Map`: For Planning AI execution of all selected add-ons.
    - `Inheritable_Addons_Content_Ordered_List`: A filtered, ordered list of add-on *contents* (excluding `task_spawning_addon.txt`) specifically for Task AI prompt inheritance. This ensures `task_spawning_addon.txt`'s logic is not passed to Task AIs.
- **Updated `prompts/Master_Prompt_Segment.txt`**: Revised the `[[USER_ADDON_SELECTION]]` block to:
    - Recommend listing `task_spawning_addon.txt` last for user clarity if it's selected.
    - Add comments explaining that the order of other (inheritable) add-ons is preserved when appended to task prompts by `task_spawning_addon.txt`.
- **Updated `framework_dev_docs/guides/MPS_Usage_Guide.md`**:
    - Documented that `task_spawning_addon.txt` itself is not inherited by Task AI prompts.
    - Clarified the significance of the listing order for inheritable add-ons in `[[USER_ADDON_SELECTION]]` and the recommendation for placing `task_spawning_addon.txt` last.
- Confirmed these changes align with user expectations for clarity and safe add-on processing.
**State of Deliverables:** Add-on processing logic and user guidance have been refined. The system is safer regarding add-on inheritance by Task AIs, and documentation provides clearer instructions on add-on ordering. Framework is robust and ready for submission or further advanced testing.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Actions:**
This session focused on introducing a new "process" type add-on, `build_product_specs_process.txt`, and significantly enhancing the core framework's ability to manage different types of primary directive add-ons.

*   **`build_product_specs_process.txt` Add-on Development:**
    *   Conceptualized and created the new add-on `prompts/add_ons/build_product_specs_process.txt` to guide an AI in generating comprehensive product specification documents.
    *   Defined necessary user configuration variables in `prompts/Master_Prompt_Segment.txt` for this process:
        *   `PRODUCT_NAME`
        *   `PRODUCT_SPECS_INPUT_DOCS_PATH`
        *   `PRODUCT_SPECS_GUIDANCE_DOCS_PATH`
        *   `PRODUCT_SPECS_OUTPUT_PATH`
        *   `PRODUCT_SPECS_PRIMARY_INPUT_FILE` (optional, for heading-based research focus)
        *   `PRODUCT_SPECS_REF_DEPTH` (optional, 0-2, for controlling internal document reference following)
    *   Detailed the multi-phase logic within `build_product_specs_process.txt` covering initialization, input ingestion, guided deep research (including primary document heading focus and configurable reference depth), decision making, and structured output generation (product spec, requirements list, optional architectural spec).
    *   Updated `prompts/add_ons/available_addons_manifest.md` to include `build_product_specs_process.txt` with its type, purpose, inputs, and outputs.
    *   Updated `framework_dev_docs/guides/MPS_Usage_Guide.md` to document the new configuration variables and explain their use with the `build_product_specs_process.txt` add-on.

*   **`Core_Planning_Instructions.txt` Framework Refinement:**
    *   To robustly support diverse primary add-ons (like the new `build_product_specs_process.txt` alongside `task_spawning_addon.txt`), the add-on handling logic in `prompts/iep/Core_Planning_Instructions.txt` was enhanced:
        *   Introduced `Known_Primary_Directive_Addons`: A defined list of add-on filenames recognized as capable of being the main directive for a session.
        *   Implemented logic to identify an `Active_Primary_Addon_Filename`: The first add-on selected by the user that matches an entry in `Known_Primary_Directive_Addons` takes precedence.
        *   Added handling for scenarios where multiple known primary add-ons are selected, ensuring only the first one (by user order) is active and a warning/note is made.
        *   Generalized the non-inheritance mechanism: The `Inheritable_Addons_Content_Ordered_List` (for appending to sub-prompts) now correctly excludes the content of whatever add-on is currently the `Active_Primary_Addon_Filename`, not just `task_spawning_addon.txt` by its hardcoded name. This makes the filtering dynamic.
        *   Updated commit message instructions to reflect which primary add-on was active.

**State of Deliverables:**
The new `build_product_specs_process.txt` add-on is created, documented, and integrated with new configuration options. The core MPS framework's add-on handling is now significantly more robust, extensible to multiple primary directive add-ons, and clearly defines precedence and inheritance rules. All relevant documentation (`MPS_Usage_Guide.md`, `available_addons_manifest.md`, `Master_Prompt_Segment.txt`) has been updated. The system is ready for submission and testing of these new capabilities.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Actions:**
This session focused on fully implementing the "Level 0 Deep Research" methodology within the `build_product_specs_process.txt` add-on, including integration of previously defined codebase review preferences.

*   **`build_product_specs_process.txt` "Level 0 Deep Research" Implementation:**
    *   **New User Configurations:** Added `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` (options: "complex", "cursory", "focused"; default "cursory") and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` (optional keywords for "focused" review) to `prompts/Master_Prompt_Segment.txt`.
    *   **Documentation:** Updated `framework_dev_docs/guides/MPS_Usage_Guide.md` to document these new configuration variables for the "Build Product Specs" process.
    *   **Add-on Logic Refinement (`prompts/add_ons/build_product_specs_process.txt`):**
        *   **Phase 1 (Initialization):** Ensured retrieval and validation of the new codebase review variables.
        *   **Phase 2 (Input Ingestion):** Clarified identification of the 'Primary Input Document'.
        *   **New Sub-Phase 3.A (Overall Primary Input Document Review):** Added an initial high-level review of the Primary Input Document to identify broad themes.
        *   **Restructured Sub-Phase 3.B (Per-Heading Deep Dive Cycle):** This is now the core of the research process. For each major heading in the Primary Input Document, the AI performs a 5-step analysis:
            1.  Heading Context Analysis.
            2.  Supporting Input Documents Analysis (contextual to the current heading).
            3.  References & Codebase Analysis:
                *   Document-to-document references honor `PRODUCT_SPECS_REF_DEPTH`.
                *   Codebase references are analyzed based on `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS`.
                *   **"Level 0 Code Analysis ONLY"** rule strictly enforced (AI does not follow internal code references like imports from fetched code text).
            4.  Consolidated Research Execution (for the current heading, using all gathered information).
            5.  Iterative Content Contribution (drafting content for output documents relevant to the current heading).
        *   **Revised Phase 4 (Decision Making):** Now explicitly follows the per-heading cycle, focusing on finalizing document structures based on iteratively drafted content.
        *   **Revised Phase 5 (Output Generation):** Changed to "Output Document Assembly and Finalization," emphasizing assembly and review of the iteratively drafted content.

**State of Deliverables:**
The `build_product_specs_process.txt` add-on now implements a detailed "Level 0 Deep Research" methodology with per-heading analysis. This includes configurable codebase review preferences and a strict "Level 0" approach to code analysis. All associated configurations in `Master_Prompt_Segment.txt` and documentation in `MPS_Usage_Guide.md` have been updated. This "Phase A" of the `build_product_specs_process.txt` development is complete and ready for submission and testing before considering any potential "Phase B" (multi-pass iterative deepening) refinements.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Actions for Enhanced Iterative Deepening (0-3 Levels) - "Phase B (Revised)":**
This session focused on expanding and refining the iterative deepening capabilities of the `build_product_specs_process.txt` add-on to support four distinct levels (0-3) with specific content generation goals for each.

*   **Updated `prompts/Master_Prompt_Segment.txt`:**
    *   Modified the `PRODUCT_SPECS_ITERATION_DEPTH` variable within `[[USER PATH CONFIGURATION]]` to accept a range of 0-3.
    *   Updated its comment to describe the new purpose for each level:
        *   `0`: Outline Generation (all heading levels 3-4 deep, title, 1 para key ideas per heading).
        *   `1`: Basic Content (Bachelor's level, ~3 paras per original D0 heading, with concepts, examples). Builds on D0.
        *   `2`: Expert Elaboration (~1 page per original D0 heading, deeper analysis, more nuanced). Builds on D1.
        *   `3`: AI Unique Insights (Extensive elaboration, ~3 pages per original D0 heading, novel ideas, critical analysis). Builds on D2.
    *   Confirmed `PRODUCT_SPECS_PREVIOUS_ITERATION_OUTPUT_PATH` remains correctly defined as required for depths 1, 2, and 3.

*   **Updated `framework_dev_docs/guides/MPS_Usage_Guide.md`:**
    *   **Variable Documentation:** The description for `PRODUCT_SPECS_ITERATION_DEPTH` was updated to reflect the new 0-3 range and the detailed definitions for each level.
    *   **Workflow Section ("Using Iterative Deepening..."):**
        *   The step-by-step guide was revised to incorporate all four iteration levels (0-3).
        *   Descriptions for Iterations 0, 1, and 2 were updated to match the new content goals.
        *   Iteration 3 (AI Unique Insights) was added with instructions on setting variables and expected outcomes (`*_D3.md` files).
        *   Clarified user responsibility for managing output folders across all iterations (D0, D1, D2, D3).

*   **Extensively Refined `prompts/add_ons/build_product_specs_process.txt`:**
    *   **Phase 1 (Initialization):** Updated `PRODUCT_SPECS_ITERATION_DEPTH` validation to [0, 3] (default 0). Added strict validation for `PRODUCT_SPECS_PREVIOUS_ITERATION_OUTPUT_PATH` (must be provided and accessible if depth > 0, otherwise critical error and halt). Logic to determine output file suffix (`_D0` to `_D3`) added.
    *   **Phase 2 (Input Processing):** Made conditional. Depth 0 processes original inputs. Depths 1-3 primarily process previous iteration's outputs, using original inputs as reference.
    *   **Phase 3 (Content Generation):** Implemented distinct conditional logic for each `PRODUCT_SPECS_ITERATION_DEPTH` (0-3):
        *   **Depth 0 ("Outline Generation"):** Uses the "Level 0 Deep Research" per-heading methodology. Output per heading is strictly title and a single paragraph of key ideas.
        *   **Depth 1 ("Basic Content"):** Reads D0 outputs. Expands each D0 key idea paragraph to ~3 paragraphs (Bachelor's level detail).
        *   **Depth 2 ("Expert Elaboration"):** Reads D1 outputs. Expands each original major heading's section to ~1 page of expert-level content.
        *   **Depth 3 ("AI Unique Insights"):** Reads D2 outputs. Expands each original major heading's section to ~3 pages with novel AI-driven insights.
    *   Included general instructions for iterations 1-3 on building upon previous work and maintaining consistency.
    *   **Phase 4 (Decision Making) & Phase 5 (Output Assembly):** Adapted to support the iterative workflow, ensuring correct document structures are defined (primarily at D0) and content is assembled with appropriate depth suffixes for filenames.

**State of Deliverables:**
The `build_product_specs_process.txt` add-on now fully supports a 0-3 level iterative deepening workflow with clearly defined goals and logic for each depth. All associated configurations in `Master_Prompt_Segment.txt` and documentation in `MPS_Usage_Guide.md` have been updated to reflect these significant enhancements. This "Phase B (Revised)" of the `build_product_specs_process.txt` development, focusing on iterative deepening, is complete. The system is ready for submission and extensive testing of this multi-stage process.
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
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request and illustrative example: `[ ] task_spawning_addon.txt` to Jules instance).
**MPS Version Referenced:** The modularized MPS version (where `Core_Planning_Instructions.txt` was already separate) and the current version implementing optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User desired to further enhance the framework's modularity by making the entire project planning and task generation process an optional component, selectable by the user, rather than an inherent, non-optional function of the MPS.
**Observation/Issue:** While `Core_Planning_Instructions.txt` was already separated, it still implicitly mandated the task spawning workflow. The framework lacked the flexibility to be used for other purposes without invoking this primary planning behavior.
**Suggestion for MPS Refinement (Implemented):** The user's suggestion to make task spawning an optional add-on was implemented.
    - The core logic for project planning, TLP generation, and task prompt creation was moved from `prompts/iep/Core_Planning_Instructions.txt` to a new file: `prompts/add_ons/task_spawning_addon.txt`.
    - `prompts/iep/Core_Planning_Instructions.txt` was updated to include conditional logic. It now checks if `task_spawning_addon.txt` is selected by the user in `[[USER_ADDON_SELECTION]]` (within `Master_Prompt_Segment.txt`).
    - If selected, the `task_spawning_addon.txt` is executed.
    - If not selected, the AI is instructed to acknowledge this, skip default plan generation, and process any other selected add-ons.
    - This change makes the MPS framework significantly more flexible, allowing users to engage the Planning AI for potentially different primary functions by selecting different combinations of add-ons, or by using it with only utility add-ons.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifying questions and specific directive regarding `task_spawning_addon.txt` inheritance and add-on ordering).
**MPS Version Referenced:** The MPS version with optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User inquired about how multiple add-ons interact, particularly how `task_spawning_addon.txt` would affect other add-ons, and then specifically requested that `task_spawning_addon.txt` should not be inherited by the Task AIs it generates. User also sought clarity on the importance of add-on ordering in the selection block.
**Observation/Issue:**
    1. The previous add-on processing logic could have resulted in `task_spawning_addon.txt` (intended for the Planning AI) being appended to Task AI prompts, leading to overly verbose prompts and potential for unintended behavior.
    2. The impact of add-on order in `[[USER_ADDON_SELECTION]]` on the final content of Task AI prompts was not explicitly documented, potentially leading to user confusion.
**Suggestion for MPS Refinement (Implemented):**
    - **Non-inheritance of `task_spawning_addon.txt`**: Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B) to create a filtered list (`Inheritable_Addons_Content_Ordered_List`) of add-on contents for Task AI prompt inheritance, which explicitly excludes the content of `task_spawning_addon.txt`. This ensures the Planning AI uses `task_spawning_addon.txt` for its process, but Task AIs receive cleaner, more focused prompts.
    - **Clarified Add-on Ordering**: Updated `prompts/Master_Prompt_Segment.txt` (comments in `[[USER_ADDON_SELECTION]]`) and `framework_dev_docs/guides/MPS_Usage_Guide.md` to explain that the order of inheritable add-ons is preserved when `task_spawning_addon.txt` appends them to task prompts. Also recommended listing `task_spawning_addon.txt` last for visual clarity if used.
    - These refinements improve the safety and predictability of add-on interactions and provide users with better control and understanding of how their selections affect Task AI prompts.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (initial idea for process automation, then specific refinements for primary input and reference depth for product specs).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** User expressed a need to use the MPS framework for more than just task spawning, specifically for a structured process to generate product specification documents from various inputs. Later refinements included requests for more control over the research phase of this process.
**Observation/Issue:** The initial MPS framework was primarily geared towards task spawning. A structured approach was needed to define and execute other complex, multi-step AI-driven processes. For the product specs process,
        *   Added `PRODUCT_SPECS_PRIMARY_INPUT_FILE`: An optional path to a specific document. The AI is instructed to focus its research by analyzing at least every numbered heading in this document. If not provided, the AI selects the most comprehensive input document for this role.
        *   Added `PRODUCT_SPECS_REF_DEPTH`: An optional integer (0-2, default 1) to control how many levels deep the AI will follow internal references within the provided document set.
    These enhancements make the product specification generation process more guided and configurable.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** Jules (proactive refinement during development of `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** The introduction of `build_product_specs_process.txt` as a new "primary directive" add-on (similar in role to `task_spawning_addon.txt` but with a different function) highlighted the need to generalize how the core framework selects and manages such add-ons.
**Observation/Issue:** The existing logic in `prompts/iep/Core_Planning_Instructions.txt` was somewhat hardcoded around `task_spawning_addon.txt` for determining the main execution path and for non-inheritance of add-on content into sub-prompts. This would not scale well if many different primary add-ons were developed.
**Suggestion for MPS Refinement (Implemented):**
    - Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B and II) to:
        - Define a list of `Known_Primary_Directive_Addons`.
        - Dynamically identify an `Active_Primary_Addon_Filename` by checking the user's selection against this known list, prioritizing the first one selected by the user if multiple are chosen. A warning is logged if multiple primary add-ons are selected.
        - Ensure that the `Inheritable_Addons_Content_Ordered_List` (used for appending to sub-prompts) correctly excludes the content of the currently `Active_Primary_Addon_Filename`, not just `task_spawning_addon.txt`.
    - This makes the core framework more robust and extensible, allowing new primary add-ons to be integrated more easily without requiring significant changes to the core conditional logic each time. It also handles user selection of multiple primary add-ons more gracefully.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (specific feedback on research methodology, codebase interaction rules like "level 0 only for codebases," and codebase review preferences for the `build_product_specs_process.txt` add-on).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on.
**Context/Scenario:** User provided detailed requirements to refine the initial `build_product_specs_process.txt` add-on to achieve a more structured, in-depth, and controllable research and content generation process, particularly focusing on how the AI should interact with a primary document and linked codebases.
**Observation/Issue:** The initial version of `build_product_specs_process.txt` had a more general research phase. The user required a more granular, heading-based research approach for the Primary Input Document, specific rules for how the AI should analyze linked codebases (including different levels of review depth/focus), and a strict "level 0 only" rule for code analysis to prevent unintended deep dives into code.
**Suggestion for MPS Refinement (Implemented):**
The `prompts/add_ons/build_product_specs_process.txt` was significantly updated to incorporate the "Level 0 Deep Research" methodology as per user requirements:
    - **New Configurations:** Introduced `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` in `Master_Prompt_Segment.txt` and documented them in `MPS_Usage_Guide.md`.
    - **Per-Heading Research Cycle:** The add-on's Phase 3 was restructured. It now includes an initial high-level review of the Primary Input Document. Then, for each major heading in that document, it performs a 5-step analysis: Heading Context Analysis, Supporting Docs Analysis, References & Codebase Analysis, Consolidated Research for the heading, and Iterative Content Contribution for that heading.
    - **Codebase Review Integration:** The "References & Codebase Analysis" step now explicitly uses the `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` ("complex", "cursory", "focused") and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` to guide its interaction with linked code.
    - **"Level 0 Code Analysis ONLY":** A strict rule was added, instructing the AI not to follow internal code references (like imports or function calls to other files) from any fetched code text. Its analysis is confined to the directly retrieved code content.
    - **Iterative Content Drafting:** Content for output documents is now drafted iteratively during the per-heading analysis and then assembled and finalized in later phases.
    This refined process provides a more systematic and controllable approach for the AI when generating product specifications.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifications and new requirements for iterative deepening for `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on.
**Context/Scenario:** Expanding the iterative capabilities of `build_product_specs_process.txt` from an initial 0-2 level concept to a more detailed 0-3 level process with specific content goals for each level.
**Observation/Issue:** The initial iterative deepening plan (0-2 levels) for `build_product_specs_process.txt` needed more specific content goals for each level and an additional level for AI-driven insights to meet user's evolving requirements for content depth and sophistication.
**Suggestion for MPS Refinement (Implemented):**
The iterative deepening feature of `build_product_specs_process.txt` was expanded to support 0-3 levels, and associated configurations and documentation were updated:
    - **Configuration Updates (`Master_Prompt_Segment.txt`):** `PRODUCT_SPECS_ITERATION_DEPTH` comment updated to reflect the 0-3 range and specific goals:
        - `0`: Outline Generation (headings with single key idea paragraphs).
        - `1`: Basic Content (Bachelor's level, ~3 paras per D0 heading).
        - `2`: Expert Elaboration (~1 page per original D0 heading).
        - `3`: AI Unique Insights (~3 pages per original D0 heading, novel ideas).
    - **Add-on Logic (`build_product_specs_process.txt`):**
        - Phase 1 (Initialization) now validates depth (0-3) and `PRODUCT_SPECS_PREVIOUS_ITERATION_OUTPUT_PATH` (required for D1-D3, critical error if missing). Output file suffixes `_D0` to `_D3` are determined.
        - Phase 2 (Input Processing) is conditional: D0 uses original inputs; D1-D3 use previous iteration's outputs as primary, with original inputs as reference.
        - Phase 3 (Content Generation) has distinct logic for each depth (0-3), detailing how to build upon previous work (for D1-D3) or generate outlines (D0) according to the specified content goals for each level.
    - **Documentation (`MPS_Usage_Guide.md`):** Updated to detail the 0-3 levels, their specific purposes, and the user workflow for managing iterative runs and output folders.
    This provides a more granular, powerful, and well-defined iterative workflow for the `build_product_specs_process.txt` add-on.
---
