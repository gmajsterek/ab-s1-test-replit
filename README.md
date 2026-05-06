# Spec: Stage 1 Demo Track - Ticket Creation
**Scenario ID:** S1-DEMO-TICKET | **Stage:** 1 | **Mode:** Deployable Demo (Chat Agent UX + API + In-Memory Storage)

## Intent
Create a realistic, clickable, deployable PoC demo that simulates a user interacting with a support chat agent. The mocked agent converses with the user to gather the information needed to create a support ticket, or deflects to a knowledge base article when the issue can be resolved without raising a ticket. No real LLM or external AI service is used — agent behaviour is fully scripted/state-machine driven and self-contained.

## User Stories

1. As a support requester, I can open a chat window and describe my issue in natural language to a mocked support agent.
2. As a support requester, the agent asks me follow-up questions to collect each required ticket field conversationally, one step at a time.
3. As a support requester, when I describe a common or known issue, the agent offers a relevant knowledge base article and asks whether it resolves my problem before proceeding to create a ticket.
4. As a support requester, if the knowledge base article resolves my issue, I can close the chat without a ticket being created.
5. As a support requester, if the KB article does not resolve my issue (or no article matches), the agent continues gathering the remaining ticket fields.
6. As a support requester, I receive clear inline feedback in the chat when I provide an invalid or missing value, and the agent re-prompts me for the same field without losing my earlier answers.
7. As a support requester, I see a confirmation message in the chat with the generated ticket ID and a summary of the submitted ticket.
8. As a support requester, I can start a new conversation from the confirmation state without a page reload.
9. As a demo reviewer, I can walk through the full KB-deflection path and the full ticket-creation path without external dependencies.
10. As a demo reviewer, I can see all submitted tickets in a panel or summary view confirming persistence in the UI.
11. As a demo reviewer, I can reset the demo to its seed state via a UI action or single documented command without restarting the server.

## Acceptance Criteria

- Chat interface presents a mocked agent that greets the user and asks them to describe their issue.
- Agent matches the user's described issue against a local knowledge base (minimum 3 KB articles); when a match is found the agent surfaces the article and asks whether it resolves the issue before continuing.
- If KB deflection succeeds (user confirms resolved), no ticket is created and the conversation ends gracefully.
- If KB deflection fails or no article matches, the agent collects all required ticket fields conversationally.
- Agent gathers ticket fields one at a time, in a logical sequence, and re-prompts on invalid input with deterministic, field-specific error messages without losing previously collected values.
- Successful ticket submission persists data to a local in-memory store and shows a confirmation message with ticket ID and summary inside the chat.
- After confirmation, the user can start a new conversation and submit a second ticket without a page reload.
- Submitted tickets are visible in a ticket list or summary panel — persistence is demonstrable in the UI, not just internal state.
- Demo can be reset to seed state via a UI action or documented single command without restarting the server.
- Thinking/processing, error, and success states in the chat are visibly distinct.
- When the agent asks for a constrained-choice field (issue_category, priority, KB yes/no, new-conversation prompt), it offers a selectable interaction pattern (chips, buttons, or equivalent) so the user does not have to guess or type raw enum values. The specific design, labels, and visual treatment are left to the agent's judgment — use what aligns best with modern chat UX practices.
- All user-facing text uses plain, human-readable language. Internal field names and enum keys are never exposed to the user.
- The conversation with agent seems natural
- UI is responsive at mobile (375px), tablet (768px), and desktop (1440px) breakpoints.
- Local API endpoint(s) handle ticket create, ticket read (list), knowledge base search, and demo reset.
- Synthetic seed data is available: minimum 5 seed tickets and minimum 3 KB articles.
- OpenAPI spec documents all exposed API endpoints.
- Business observability events are emitted (at least: `conversation_started`, `kb_article_suggested`, `kb_deflection_succeeded`, `kb_deflection_failed`, `validation_failed`, `ticket_created`) as structured log entries containing event name, timestamp, and ticket ID or session ID where applicable.
- Light tests cover the KB-deflection happy path, the ticket-creation happy path, and at least one validation failure path.
- No external API calls, no external model calls, and no remote services are required for any path.
- Run steps and known limitations are documented.
- user can send message by hitting Enter key

## Scope

- Local frontend chat UI + local scripted agent state machine + local API + local in-memory data store.
- Mocked conversational agent behaviour (scripted, no real LLM); realistic  to demonstrate the intended UX pattern.
- Local knowledge base with a small set of static articles about password reset used for deflection matching.
- Single-user local run.

## Tech Stack

The tool chooses the tech stack. No stack is prescribed. This is intentional — stack selection is part of what the benchmark observes. Document the chosen stack in the architecture note.

## Non-goals

- Real LLM or AI model integration.
- External integrations.
- Multi-user sessions or authentication.
- Production security and deployment hardening.

## Execution Pack (Merged)

### Shared Ticket Contract

Required fields:
- ticket_id (format: `TKT-` prefix + zero-padded auto-increment, e.g. `TKT-0001`)
- requester_name
- requester_email
- issue_category (enum: `bug`, `feature_request`, `question`, `account_issue`)
- priority (enum: `low`, `medium`, `high`, `critical`)
- short_description (max 200 characters)
- detailed_description
- created_at (ISO 8601 UTC timestamp, server-generated)
- status (enum: `open`, `in_progress`, `resolved`, `closed`; default on creation: `open`)

Validation baseline:
- Required-field validation (all fields except detailed_description are required)
- Email format validation (standard RFC 5322 local@domain)
- issue_category must be one of: `bug`, `feature_request`, `question`, `account_issue`
- priority must be one of: `low`, `medium`, `high`, `critical`
- short_description max length: 200 characters
- Deterministic user-readable error messages (one message per failing field)

In-memory storage baseline:

- Local in-memory storage only (no external database)
- Deterministic initialization/reset mechanism required

Seed data baseline:
- Minimum 5 seed tickets covering at least 3 different issue_category values and 3 different priority values
- Minimum 3 knowledge base articles; each article must have: `article_id`, `title`, `body`, and one or more `keywords` used for matching
- Seed script or endpoint must be idempotent (safe to run multiple times)

### Agent Conversation Contract

The mocked agent follows a deterministic state machine. Required states:

| State | Description |
|---|---|
| `GREETING` | Agent greets user and asks them to describe their issue |
| `KB_SUGGEST` | Agent presents a matching KB article and asks if it resolves the issue |
| `KB_RESOLVED` | User confirms resolved — conversation closes gracefully, no ticket created |
| `COLLECT_FIELDS` | Agent asks for each required ticket field in sequence |
| `VALIDATE` | Agent detects invalid input, shows error message, re-prompts the same field |
| `CONFIRM` | Agent shows ticket ID and summary, offers to start a new conversation |

KB matching logic may be keyword/substring-based. No semantic search or external AI is required.

### Conversational UX Standards

The chat experience must feel like a well-designed product, not a developer demo. Add animation and other effects to make it feel natural. Prioritise ease of use, natural user flow, and visual polish.

**Hard constraints** (must be met — these are testable):
- Constrained-choice fields (issue_category, priority, KB resolution, post-confirmation) must offer a selectable interaction pattern (e.g., quick-reply chips, buttons, selectable cards) so the user never has to guess valid values or type raw enum keys.
- All user-facing text — labels, options, prompts, confirmations, errors — must use plain, human-readable language. Internal field names and enum values must never appear in the UI.
- One question or prompt per agent turn. Never bundle multiple questions into a single message.
- Error messages must name the specific problem and tell the user how to fix it.
- The conversation history must remain scrollable so the user can review earlier messages.

**Design decisions left to the agent's judgment** (exercise creativity — these will be evaluated for quality):
- How to label selectable options. For example, for `issue_category`: decide whether "Bug report", "Something isn't working", or an icon + label approach best communicates each choice. Choose labels that a non-technical user would understand without hesitation.
- How to present the KB article to the user — inline card, expandable section, modal, or another pattern. Pick what makes the conversation flow feel natural.
- Visual treatment of distinct chat states (thinking/processing, validation error, KB article, confirmation). Decide on colours, icons, typography, and layout that make each state immediately recognisable.
- Whether to show a character counter, progress indicator, or other affordance for free-text fields with limits — and how to present it.
- How to render the ticket confirmation — summary card, receipt-style layout, or another pattern. It must be clearly readable, not raw JSON or a plain list.
- Overall tone, personality, and greeting style of the agent. Make it feel like a real product.
- Any additional micro-interactions, transitions, or polish that improve the experience.

The benchmark evaluates the agent's design taste and UX sensibility. A cookie-cutter chat with raw labels will score lower than a thoughtfully designed interaction — even if both are functionally correct.

### Guardrails

Stage 1 Demo generic guardrails reference (required for all Stage 1 Demo scenarios):
- `Test_Scenarios/S1-GENERIC-GUARDRAILS.md`

Scenario-specific guardrails for this ticket demo:

- Build a runnable local chat UI + scripted agent state machine + API + in-memory storage. No real LLM or AI service.
- Mocked agent must feel conversational — one question or prompt per turn. For constrained-choice fields use quick-reply option chips (not a free-form text input or a full form). Chips are part of the chat bubble pattern and are encouraged.
- Use local in-memory storage only for persistence during runtime (tickets and KB articles).
- Provide synthetic seed data (tickets + KB articles) for demonstration purposes.
- Place all generated app code for this scenario in `Test_Scenarios/CreatedApps/S1-DEMO-TICKET/`.
- The folder name must match the scenario ID exactly (`S1-DEMO-TICKET`) and be kept separate from other scenario folders.

### Initial Prompt

Build a realistic, clickable, deployable support chat agent demo as a local PoC. The demo simulates a user interacting with a mocked support agent that gathers ticket information conversationally or deflects to a knowledge base article. No real LLM or external AI service is used.

**Before implementation:** You must read and follow Stage 1 Demo generic guardrails in:

- `Test_Scenarios/S1-GENERIC-GUARDRAILS.md`

Shared ticket contract fields:

- ticket_id (format: TKT-0001)
- requester_name
- requester_email
- issue_category (enum: bug, feature_request, question, account_issue)
- priority (enum: low, medium, high, critical)
- short_description (max 200 chars)
- detailed_description
- created_at (ISO 8601 UTC, server-generated)
- status (enum: open, in_progress, resolved, closed; default: open)

Requirements:

1. Build a chat UI where the user types messages and a mocked support agent responds turn by turn. The agent is scripted (state machine) — no real LLM.
2. Agent state machine must cover: GREETING → KB_SUGGEST (if keyword match found) → KB_RESOLVED or COLLECT_FIELDS → VALIDATE (on invalid input, re-prompt same field) → CONFIRM.
3. Implement a local knowledge base (minimum 3 articles with article_id, title, body, keywords). When the user's opening message matches article keywords, the agent surfaces the article and asks if it resolves the issue before collecting ticket fields.
4. If KB deflection succeeds (user confirms resolved), end the conversation gracefully without creating a ticket.
5. If KB deflection fails or no article matches, the agent collects all required ticket fields one at a time in conversation, re-prompting on invalid input with deterministic field-specific error messages without losing previously collected values.
6. Implement a local API layer for: ticket create, ticket list, KB article search, and demo reset (seed reload).
7. Persist submitted tickets to local in-memory storage.
8. Add at least 5 synthetic seed tickets (covering 3+ categories and 3+ priorities) and at least 3 KB articles, with an idempotent way to initialize or reset to seed state without restarting the server.
9. Show visibly distinct states in the chat: agent thinking/processing, validation error, KB article card, ticket confirmation.
10. Apply the Conversational UX Standards defined in this spec. The hard constraints are non-negotiable. For all design decisions marked as "left to the agent's judgment", use your best UX judgment — choose labels, layouts, visual patterns, and interaction styles that you believe create the best user experience. Explain your design choices in the architecture note.
11. After ticket confirmation, user can start a new conversation and submit a second ticket without a page reload.
12. Include a ticket list or summary panel so persistence is visible in the UI, not just the database.
13. Provide an OpenAPI document for all demo API endpoints.
14. Add a 1-2 page architecture note covering the agent state machine, KB matching logic, API usage, and your UX design rationale.
15. Emit structured business observability log entries for at least: `conversation_started`, `kb_article_suggested`, `kb_deflection_succeeded`, `kb_deflection_failed`, `validation_failed`, `ticket_created` — each with event name, timestamp, and session ID or ticket ID where applicable.
16. Add light tests covering: KB-deflection happy path, ticket-creation happy path, and at least one validation failure path.
17. Keep everything local and self-contained. No external APIs, no external model calls, and no remote services.
18. Make UI responsive for mobile (375px), tablet (768px), and desktop (1440px).
19. Provide clear run steps and known limitations.
20. Run a SonarQube scan (or equivalent static analysis) on the submitted code and resolve any blocker or critical issues.
21. Place all generated app code for this scenario under `Test_Scenarios/CreatedApps/S1-DEMO-TICKET/` (folder name must equal the scenario ID) and do not modify files outside that directory except session logs/checklists.
