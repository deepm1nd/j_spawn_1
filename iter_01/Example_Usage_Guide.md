# Example Usage Guide: AI-Assisted Document Analysis

This guide demonstrates a hypothetical interaction between a User and an AI, leveraging the Master Prompt Segment (MPS), Information Exchange Protocol (IEP), and Task Launch Plan (TLP) to analyze a set of customer feedback documents.

**Objective:** To identify common themes and sentiment in customer feedback regarding a new software feature.

## Iteration 1: Initial Setup & Data Ingestion

**1. User Delivers MPS:**

*   **User:** "Hello AI. I'd like to start a new project. Please review this Master Prompt Segment (MPS) which outlines how we'll work together.
    ```
    ## Master Prompt Segment (MPS)
    [Content of MPS as defined previously, including embedded IEP and TLP template]
    ```"
*   **AI:** "Understood. I have processed the Master Prompt Segment (MPS), including the Information Exchange Protocol (IEP) and the Task Launch Plan (TLP) template. I am ready to proceed according to these guidelines."

**2. User Delivers First TLP:**

*   **User:** "Great. Here is our first Task Launch Plan for this project.
    ```markdown
    ## Task Launch Plan (TLP): CustomerFeedbackAnalysis-Phase1

    **1. Overall Goal:**
        *   Identify common themes and sentiment in customer feedback regarding the new 'Project Sunrise' software feature.

    **2. Current Sub-Task:**
        *   **ID:** TLP-001-DataIngestion
        *   **Objective:** Ingest customer feedback from three sources: 'feedback_emails.txt', 'survey_responses.csv', and 'app_store_reviews.json'. Consolidate them into a structured format.
        *   **Expected Deliverables:**
            1.  A single JSON file named `consolidated_feedback_v1.json` containing all feedback entries. Each entry should have `source`, `date`, `customer_id`, and `feedback_text` fields.
            2.  A brief data quality report highlighting any immediate issues (e.g., missing fields, inconsistent date formats).

    **3. Context & Background:**
        *   'Project Sunrise' was launched last week. We have collected initial feedback.
        *   `feedback_emails.txt`: Plain text, one email per entry, may need parsing for date/customer.
        *   `survey_responses.csv`: CSV format, headers: `SurveyDate`, `UserID`, `Q1_Feedback`.
        *   `app_store_reviews.json`: JSON array, objects with `reviewDate`, `user`, `comment`.

    **4. Information Requirements (for AI to complete the current sub-task):**
        *   [X] File: `feedback_emails.txt` (Attached)
        *   [X] File: `survey_responses.csv` (Attached)
        *   [X] File: `app_store_reviews.json` (Attached)
        *   [ ] User clarification: How to handle anonymous feedback where `customer_id` might be missing? (AI to ask if not clear from data)

    **5. Steps & Execution Strategy:**
        *   1. Parse `feedback_emails.txt`. Extract date, sender (as customer_id if possible), and body.
        *   2. Load `survey_responses.csv`. Map headers to standard fields.
        *   3. Load `app_store_reviews.json`. Map keys to standard fields.
        *   4. Standardize date formats (e.g., to YYYY-MM-DD).
        *   5. Consolidate all entries into the specified JSON structure.
        *   6. Generate data quality report.

    **6. AI Role & Constraints:**
        *   **Role:** Data processing and structuring assistant. Use Python for scripting if needed.
        *   **Constraints:** Prioritize accurate extraction. If unsure about parsing a specific entry, flag it in the quality report. For missing `customer_id` in emails, use "email_anonymous_[index]". For app store, use "appstore_user_[username]" if no numeric ID.

    **7. Evaluation Criteria:**
        *   Completeness of `consolidated_feedback_v1.json`.
        *   Accuracy of field mapping and data extraction.
        *   Clarity of the data quality report.

    **8. Next Anticipated Sub-Task (Provisional):**
        *   TLP-002-SentimentAnalysis: Perform sentiment analysis on the `feedback_text` field.

    **9. Iteration & Feedback Points:**
        *   Review after parsing each file type (AI to provide interim summary).
        *   Final review of consolidated file and quality report.

    **10. Log of Significant Decisions / Changes to TLP:**
        *   (Empty for now)
    ```
    *(User attaches the three specified data files)*"

**3. AI Response (Following IEP):**

*   **AI:**
    ```
    ## AI Response

    **Response ID:** AI-RESP-001
    **Task ID Echo:** TLP-001-DataIngestion
    **Summary of Understanding:** I will ingest data from `feedback_emails.txt`, `survey_responses.csv`, and `app_store_reviews.json`. I will parse and standardize these into a single JSON output file (`consolidated_feedback_v1.json`) with `source`, `date`, `customer_id`, and `feedback_text` fields. I will also provide a data quality report.
    **Assumptions:**
        *   Dates will be standardized to YYYY-MM-DD.
        *   For emails, I will attempt to infer `customer_id` from sender information; if not possible, I will use the format "email_anonymous_[index]".
        *   For app store reviews, if a numeric `user` ID is not present, I will use "appstore_user_[username]".
    **Information Requests:**
        *   Regarding anonymous feedback where `customer_id` might be missing (TLP Section 4): The TLP provides guidance for emails and app store reviews. For `survey_responses.csv`, if `UserID` is blank, how should I generate the `customer_id`? For example, "survey_anonymous_[index]"?
    **Proposed Solution/Output:** I will begin by parsing `feedback_emails.txt`. I will provide an interim summary once this is complete, as per TLP Section 9.
    **Next Steps Suggested:** Await User clarification on handling blank `UserID` in `survey_responses.csv`.
    ```

## Iteration 2: Clarification and Partial Delivery

**4. User Provides Clarification (Following IEP):**

*   **User:**
    ```
    ## User Request

    **Task ID:** TLP-001-DataIngestion
    **Objective:** Provide clarification on handling blank UserID in survey responses.
    **Context:** Responding to AI-RESP-001 Information Request.
    **Inputs:**
        *   Clarification: Yes, please use "survey_anonymous_[index]" if `UserID` is blank in `survey_responses.csv`.
    **Deliverables Expected:** Continue with data ingestion as per TLP-001.
    **Constraints/Preferences:** None.
    ```

**5. AI Response (Interim Update):**

*   **AI:**
    ```
    ## AI Response

    **Response ID:** AI-RESP-002
    **Task ID Echo:** TLP-001-DataIngestion
    **Summary of Understanding:** I have received your clarification. I will use "survey_anonymous_[index]" for blank UserIDs in the survey data. I have now processed `feedback_emails.txt`.
    **Assumptions:** None new.
    **Information Requests:** None at this stage.
    **Proposed Solution/Output (Interim):**
        *   Successfully parsed 50 entries from `feedback_emails.txt`.
        *   Example extracted entry: `{ "source": "email", "date": "2023-10-26", "customer_id": "user@example.com", "feedback_text": "The new feature is great!" }`
        *   Encountered 2 emails with no clear sender for `customer_id`; used "email_anonymous_0", "email_anonymous_1".
        *   No major parsing issues.
    **Next Steps Suggested:** I will now proceed to parse `survey_responses.csv`.
    ```

**(Interaction continues following this pattern: AI provides updates after each major step as per TLP Section 9. User provides feedback if necessary. AI delivers final files.)**

## Iteration 3: Final Delivery of Sub-Task

**6. AI Delivers Final Output for TLP-001 (Following IEP):**

*   **AI:**
    ```
    ## AI Response

    **Response ID:** AI-RESP-005
    **Task ID Echo:** TLP-001-DataIngestion
    **Summary of Understanding:** I have completed all steps for TLP-001-DataIngestion. All three files have been processed, data standardized, and consolidated.
    **Assumptions:** Date parsing for `survey_responses.csv` assumed 'MM/DD/YYYY' format based on observed data.
    **Information Requests:** None.
    **Proposed Solution/Output:**
        *   **File 1:** `consolidated_feedback_v1.json` (Attached). Contains 150 total entries (50 email, 70 survey, 30 app store).
        *   **File 2:** `data_quality_report_v1.txt` (Attached).
            *   Contents:
                *   Total entries processed: 150.
                *   Date standardization applied to all entries. 3 entries from `survey_responses.csv` had ambiguous date formats (e.g., 01/02/03) and were flagged; assumed MM/DD/YY.
                *   Missing `customer_id`: 2 from email (handled as "email_anonymous_X"), 5 from survey (handled as "survey_anonymous_X").
                *   No other significant data quality issues identified during ingestion.
    **Next Steps Suggested:** Ready to proceed to TLP-002-SentimentAnalysis, or await new TLP from User.
    ```

**7. User Reviews and Prepares for Next Task:**

*   **User:** "Thank you, AI. I have reviewed `consolidated_feedback_v1.json` and `data_quality_report_v1.txt`. This looks good.
    For the ambiguous dates, let's assume MM/DD/YY was correct for now.
    Please proceed with TLP-002. I will prepare a new TLP document for that and send it over."
    *(User would then draft and send `TLP-002-SentimentAnalysis`)*

This example illustrates:
*   The structured flow of communication.
*   How the TLP guides the AI's work for a specific sub-task.
*   How the IEP ensures clarity and context in each message.
*   The iterative nature of the process, with checkpoints for review.
*   How the AI requests clarification and reports its progress and findings.
