I'm working on our organization review system, which uses an AI agent to assess whether organizations should be approved or denied at submission and at payment thresholds.

*   The collect_feedback_data function must accept a list of OrganizationReviewFeedback records and return a PriorFeedbackData object whose entries list preserves the input order.

*   For each feedback record, collect_feedback_data must map actor_type, decision, and review_context to string fields, defaulting to 'unknown' when any of these is None.

*   When a feedback record has no agent_review_id, collect_feedback_data must set agent_report_summary and agent_risk_level to None, and violated_sections and dimensions to empty lists.

*   When a feedback record has an agent_review_id, collect_feedback_data must extract agent_report_summary from the parsed report's summary, agent_risk_level from overall_risk_level.value, violated_sections from the parsed report, and dimensions as a list of PriorDimensionAssessment objects (each with dimension value string, risk_level value string, and findings list).

*   If parsing the agent review raises any exception, collect_feedback_data must gracefully return None for agent_report_summary and empty lists for violated_sections and dimensions.

*   OrganizationReviewRepository must provide a get_feedback_history(organization_id) async method that returns a list of OrganizationReviewFeedback records filtered to the given organization, ordered ascending by created_at, excluding soft-deleted records (where deleted_at is set), with the linked agent_review relationship eagerly loaded. It must return an empty list when no records exist.

*   PriorDimensionAssessment must be a Pydantic model with fields: dimension (str), risk_level (str), findings (list[str]).

*   PriorFeedbackEntry must be a Pydantic model with fields: actor_type (str), decision (str), review_context (str), agent_verdict (str | None, default None), agent_risk_level (str | None, default None), reason (str | None, default None), agent_report_summary (str | None, default None), violated_sections (list[str], default []), dimensions (list[PriorDimensionAssessment], default []), created_at (datetime | None, default None). Old serialized data lacking violated_sections or dimensions must still parse, defaulting those fields to empty lists.

*   PriorFeedbackData must be a Pydantic model with a single field: entries (list[PriorFeedbackEntry], default []). PriorFeedbackEntry and PriorDimensionAssessment must fully survive a serialization roundtrip via model_dump(mode='json') followed by model_validate().

*   DataSnapshot must gain a prior_feedback field of type PriorFeedbackData with a default of PriorFeedbackData(). Existing JSONB records that do not contain a prior_feedback key must still deserialize correctly, with prior_feedback defaulting to PriorFeedbackData() (empty entries list).

*   When DataSnapshot.prior_feedback.entries is empty, ReviewAnalyzer._build_prompt must NOT include a 'Prior Review Decisions' section in the returned prompt string.

*   When DataSnapshot.prior_feedback.entries is non-empty, ReviewAnalyzer._build_prompt must include a '## Prior Review Decisions' section that appears before '## Acceptable Use Policy' and contains text with 'do NOT re-raise the same concerns'.

*   Each prior feedback entry in the prompt must be rendered as '### {REVIEW_CONTEXT_UPPERCASE} review ({date})' where date is formatted as YYYY-MM-DD. If created_at is None, the date must be rendered as 'unknown date'.

*   Within each entry in the prompt, the following fields must be included only when non-None/non-empty: '- Actor:', '- Decision:', '- Agent Verdict:', '- Agent Risk Level:', '- Agent Summary:', '- Reviewer Reason:', '- Violated Sections:', '- Dimension Assessments:'. Dimension assessments must include the dimension name, risk level, and findings. Fields that are None or empty must be completely omitted from the rendered output.

*   Multiple prior feedback entries in the prompt must appear in chronological order, with earlier entries before later ones.


*   Interface details: Type: Function
Name: collect_feedback_data
Location: server/polar/organization_review/collectors/feedback.py
Signature: collect_feedback_data(records: list[OrganizationReviewFeedback]) -> PriorFeedbackData
Description: Transforms a list of OrganizationReviewFeedback database records into a PriorFeedbackData object. Preserves input order. Defaults actor_type, decision, and review_context to "unknown" when None. Extracts agent report summary, risk level, violated sections, and dimension assessments from the linked agent_review.parsed_report when available. Gracefully handles parse errors by returning None/empty for all agent fields.

Type: Class
Name: PriorDimensionAssessment
Location: server/polar/organization_review/schemas.py
Description: Pydantic model representing a single dimension's assessment from a prior review.
Signature:
  dimension: str
  risk_level: str
  findings: list[str]

Type: Class
Name: PriorFeedbackEntry
Location: server/polar/organization_review/schemas.py
Description: Pydantic model representing a single prior review decision entry. Old serialized data without violated_sections or dimensions must still parse (both default to []). Fields with None values are optional.
Signature:
  actor_type: str
  decision: str
  review_context: str
  agent_verdict: str | None = None
  agent_risk_level: str | None = None
  reason: str | None = None
  agent_report_summary: str | None = None
  violated_sections: list[str] = []
  dimensions: list[PriorDimensionAssessment] = []
  created_at: datetime | None = None

Type: Class
Name: PriorFeedbackData
Location: server/polar/organization_review/schemas.py
Description: Pydantic model containing a list of prior feedback entries. Supports full serialization roundtrip via model_dump(mode="json") and model_validate().
Signature:
  entries: list[PriorFeedbackEntry] = []

Type: Class
Name: DataSnapshot
Location: server/polar/organization_review/schemas.py
Description: Existing Pydantic model for review data snapshots. Must gain a prior_feedback field with a default of PriorFeedbackData(). Old JSONB records that lack the prior_feedback key must still deserialize correctly (field defaults to PriorFeedbackData() with empty entries).
Signature (new field only):
  prior_feedback: PriorFeedbackData = PriorFeedbackData()

Type: Method
Name: get_feedback_history
Location: server/polar/organization_review/repository.py
Signature: async def get_feedback_history(self, organization_id: uuid.UUID) -> list[OrganizationReviewFeedback]
Description: Method on OrganizationReviewRepository. Returns all OrganizationReviewFeedback records for the given organization, ordered ascending by created_at, excluding soft-deleted records (deleted_at is not None), with the linked agent_review relationship eagerly loaded. Returns an empty list when no records exist.

Type: Method
Name: _build_prompt
Location: server/polar/organization_review/analyzer.py
Signature: _build_prompt(self, snapshot: DataSnapshot, policy_content: str) -> str
Description: Existing method on ReviewAnalyzer. Must be updated to include a "## Prior Review Decisions" section in the returned prompt string when snapshot.prior_feedback.entries is non-empty. This section must appear before "## Acceptable Use Policy" and include the text "do NOT re-raise the same concerns". When entries is empty, the section must be completely absent. Each entry is rendered as "### {REVIEW_CONTEXT_UPPERCASE} review ({date})" where date is YYYY-MM-DD. If created_at is None, "unknown date" is used instead. Within each entry, the following lines are included only when the corresponding field is non-None or non-empty: "- Actor:", "- Decision:", "- Agent Verdict:", "- Agent Risk Level:", "- Agent Summary:", "- Reviewer Reason:", "- Violated Sections:", "- Dimension Assessments:". Dimension assessments include each dimension's name, risk level, and findings. Multiple entries appear in chronological order.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.