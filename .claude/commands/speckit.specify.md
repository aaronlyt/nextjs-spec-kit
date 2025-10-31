---
description: Create or update the feature specification from a natural language feature description.
scripts:
  sh: scripts/bash/create-new-feature.sh --json "{ARGS}"
  ps: scripts/powershell/create-new-feature.ps1 -Json "{ARGS}"
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

---

## Overview

The text the user typed after `/speckit.specify` in the triggering message **is** the feature description. Assume you always have it available in this conversation even if `{ARGS}` appears literally below. Do not ask the user to repeat it unless they provided an empty command.

**Your Task**: Transform the natural language feature description into a complete, validated Product Requirements Document (PRD) using the template structure.

---

## Execution Flow

### Phase 1: Setup & Context Extraction

**1.1 Initialize Feature Structure**

Run script `{SCRIPT}` from repo root:
- **CRITICAL**: Run this script ONLY ONCE at the start
- Parse JSON output to extract:
  - `BRANCH_NAME`: Git branch for this feature
  - `SPEC_FILE`: Absolute path to specification file (e.g., `/path/to/features/###-feature-name/spec.md`)
  - `FEATURE_DIR`: Parent directory of SPEC_FILE (e.g., `/path/to/features/###-feature-name/`)
- **Escaping**: For single quotes in args like "I'm Groot", use: `'I'\''m Groot'` or `"I'm Groot"`

**1.2 Load Template**

Load `templates/spec-template.md` to understand:
- Required section structure and ordering
- Mandatory vs optional sections
- Numbering conventions (US#, J#, AC-US#.#, FR-US#.#, ENT#)
- Example formats for each section

**1.3 Parse User Description**

Extract from `$ARGUMENTS`:
- **Actors**: Who will use this feature? (roles, personas)
- **Goals**: What do they want to achieve? (objectives, outcomes)
- **Value**: Why do they need this? (benefits, business value)
- **Scenarios**: When/where will they use it? (contexts, workflows)
- **Constraints**: Any explicit limitations mentioned? (technical, budget, timeline)

**ERROR CONDITION**: If `$ARGUMENTS` is empty → STOP and return: "No feature description provided. Please use `/speckit.specify [description]`"

---

### Phase 2: Requirements Generation (User-Centric → System Capabilities)

**2.1 Generate User Stories**

Format: `US#: As a [role], I want to [goal], so that [benefit]`

**Rules**:
- Each story = one discrete user value proposition
- Number sequentially: US1, US2, US3...
- Focus on user outcomes, NOT system features
- Include 3-7 stories for typical feature (fewer for simple, more for complex)

**Quality Check**:
- ✅ Each story describes user value, not technical implementation
- ✅ Each story is independently valuable and testable
- ✅ Stories cover all actors identified in 1.3

**2.2 Map Primary User Journey**

Format: `Entry Point → [J1] → [J2] → [J3] → Exit Point`

**Rules**:
- Each journey step (J#) represents a user action or system state change
- Link each step to enabling user story: `J1 → US1`, `J2 → US1`, `J3 → US2`
- Include specific details:
  - *Enables*: Which US# this step fulfills
  - *User sees*: Specific UI elements or information
  - *User does*: Specific actions taken
  - *Decision point*: (if applicable) User choices or branching paths

**Alternative Paths**:
- Use numbering: `Alt-J2.1` (alternative to J2), `Alt-J3.1` (alternative to J3)
- Specify condition that triggers alternative
- Show flow: `J1 → J2.1 → J3` or `J1 → J5 → J6`

**Exit Points**:
- Success Exit (Primary): Happy path completion
- Success Exit (Secondary): Alternative successful outcomes
- Abort Exit: User cancels or abandons
- Error Exit: System failures with recovery
- Other domain-specific exits (e.g., Expiration Exit)

**Quality Check**:
- ✅ Every US# appears in at least one journey step
- ✅ Journey shows end-to-end flow from user perspective
- ✅ No orphaned journey steps (all reference US#)
- ✅ Decision points and alternatives are explicit

**2.3 Define Acceptance Criteria**

Format: `AC-US#.#: Given [context], When [action], Then [outcome]`

**Rules**:
- Each US# needs minimum 2-5 acceptance criteria
- Number format: `AC-US1.1, AC-US1.2` (for US1), `AC-US2.1, AC-US2.2` (for US2)
- Cover happy path, edge cases, and error scenarios:
  - Normal criteria: `AC-US1.1`, `AC-US1.2`
  - Edge cases: `AC-US1.3-Edge`, `AC-US1.4-Edge`
  - Error cases: `AC-US1.5-Error`, `AC-US1.6-Error`
- Each criterion must be:
  - **Testable**: Can be verified with pass/fail test
  - **Unambiguous**: Single clear interpretation
  - **Complete**: Specifies initial state, action, and expected outcome

**Quality Check**:
- ✅ Every US# has at least one acceptance criterion
- ✅ All criteria follow Given-When-Then format
- ✅ Edge cases and errors are covered, not just happy path
- ✅ Criteria are specific enough to write test cases

**2.4 Derive Functional Requirements**

Format: `FR-US#.#: System MUST [capability]`

**Rules**:
- Each acceptance criterion needs 1-3 functional requirements
- Number format: `FR-US1.1, FR-US1.2` (for US1), `FR-US2.1, FR-US2.2` (for US2)
- Each requirement includes:
  - *Traces to*: US#, J#, AC-US#.# (explicit traceability)
  - *Rationale*: Why this capability is needed
- Requirements describe WHAT system must do, NOT HOW to build it
- Use "System MUST [capability]" format (not "should" or "could")

**Cross-Story Requirements** (if needed):
- Format: `FR-X1, FR-X2, FR-X3...`
- For capabilities affecting multiple user stories
- Still maintain traceability to all affected US# and AC#

**Requirements Needing Clarification**:
- Format: `FR-US?.?: System MUST [NEEDS CLARIFICATION: specific question]`
- Place in Section 4.5 of spec
- Include:
  - *Traces to*: Affected user stories/criteria
  - *Impact*: What design decisions are blocked
  - *Options*: Suggested alternatives (if any)
- **LIMIT**: Maximum 3 clarification markers (see Phase 3 for handling)

**Quality Check**:
- ✅ Every AC-US#.# has at least one FR-US#.#
- ✅ Requirements are system capabilities, not technical solutions
- ✅ No implementation details (databases, frameworks, APIs)
- ✅ All requirements are testable and unambiguous
- ✅ Traceability is explicit (Traces to: ...)

**2.5 Identify Key Entities**

Format: `ENT#: [Entity Name]`

**When to Include**:
- Mandatory: If feature involves data persistence or data flow
- Optional: For pure UI/workflow features with no data

**Rules**:
- Define business entities, NOT database tables
- Each entity includes:
  - Purpose: What it represents in business terms
  - Key Attributes: Main data points (business view, not DB columns)
  - Lifecycle: Create, read, update, delete operations
  - Relationships: Connections to other entities
  - Business Rules: Constraints and validations
  - Referenced by: Which US#, FR#, AC# need this entity
- Number sequentially: ENT1, ENT2, ENT3...

**Quality Check**:
- ✅ All entities referenced by at least one FR-US#.#
- ✅ Entities describe business concepts, not technical implementation
- ✅ Relationships between entities are explicit
- ✅ Lifecycle covers creation through deletion

---

### Phase 3: Informed Guessing & Clarification Strategy

**3.1 Make Informed Guesses**

When user description lacks details, use reasonable defaults:

**✅ Make Assumptions For** (document in Section 7.1):
- Data retention periods: Industry standards (7 days for temporary, 30 days for standard)
- Performance targets: Standard web/mobile expectations (< 2 seconds for user actions)
- Error handling: User-friendly messages with appropriate fallbacks
- Authentication: Standard session-based or OAuth2 for web apps
- Integration patterns: RESTful APIs unless specified otherwise
- Access control: Role-based if users mentioned, public if not
- Character limits: Reasonable defaults (e.g., 10,000 for text fields)

**❌ Don't Assume** (mark with [NEEDS CLARIFICATION]):
- Feature scope boundaries (include/exclude specific use cases)
- Security/compliance requirements (legally/financially significant)
- User roles with different permissions (if multiple interpretations possible)
- Data privacy requirements (retention, deletion, anonymization)
- Critical business rules affecting user experience
- Integration requirements with external systems

**3.2 Prioritize Clarifications**

If more than 3 `[NEEDS CLARIFICATION]` markers exist:

**Priority Order** (keep top 3 only):
1. **Scope**: Include/exclude decisions significantly affecting development effort
2. **Security/Privacy**: Legal compliance, data protection, user safety
3. **User Experience**: Decisions with multiple reasonable interpretations affecting usability
4. **Technical**: Integration boundaries, performance requirements

**For Removed Markers**:
- Make informed guess based on common patterns
- Document assumption in Section 7.1 (Assumptions)
- Note in assumption: "Auto-resolved from clarification - validate with stakeholders"

**3.3 Document Assumptions**

In Section 7.1, record:
- What assumption was made
- Validation needed from whom (stakeholder/team)
- Impact if assumption is wrong

---

### Phase 4: Specification Writing

**4.1 Generate Complete Specification**

Write to `SPEC_FILE` using template structure:

**Section Order** (must follow template):
0. Feature Overview (mandatory)
1. User Stories & Actors (mandatory)
2. Primary User Journey (mandatory)
3. Acceptance Criteria (mandatory)
4. Functional Requirements (mandatory)
5. Key Entities (mandatory if data persistence)
6. Non-Functional Requirements (if applicable)
7. Risks, Assumptions & Dependencies (mandatory)
8. Traceability Matrix (mandatory)
9. Success Metrics (optional)
10. Glossary (optional)

**For Each Section**:
- Replace template placeholders with concrete details from user description
- Preserve section headings and numbering format
- Include examples where helpful
- Remove optional sections if not applicable (don't leave as "N/A")
- Use consistent numbering: US#, J#, AC-US#.#, FR-US#.#, ENT#

**4.2 Build Traceability Matrix**

Create table mapping:
- User Story → Journey Steps → Acceptance Criteria → Functional Requirements → Entities
- Verify no gaps or orphaned items

**4.3 Document Metadata**

At top of spec file:
- Feature Branch: `BRANCH_NAME` from script output
- Created: Current date
- Status: Draft
- Input: User description (quote `$ARGUMENTS`)

---

### Phase 5: Quality Validation

#### **5.1 Create Validation Checklist**

Generate file: `FEATURE_DIR/checklists/requirements.md`

**Checklist Structure**:
```markdown
# Specification Quality Checklist: [FEATURE NAME]

**Purpose**: Validate specification completeness and quality before proceeding to planning  
**Created**: [DATE]  
**Feature**: [Relative path to spec.md]

## Structure & Completeness
- [ ] All User Stories follow "As [role], I want [goal], so that [benefit]" format
- [ ] Primary User Journey maps complete end-to-end flow
- [ ] Every Journey Step references at least one User Story
- [ ] Every User Story appears in at least one Journey Step
- [ ] Every User Story has minimum 2 Acceptance Criteria
- [ ] All Acceptance Criteria follow Given-When-Then format
- [ ] Every Acceptance Criterion has at least one Functional Requirement
- [ ] Traceability matrix is complete (US# → J# → AC-US#.# → FR-US#.# → ENT#)
- [ ] All mandatory sections completed
- [ ] Optional sections removed if not applicable (not marked N/A)

## Content Quality
- [ ] No implementation details (languages, frameworks, APIs, databases)
- [ ] Journey shows user perspective, not system internals
- [ ] Requirements are system capabilities, not technical solutions
- [ ] All requirements are testable and unambiguous
- [ ] User Stories focus on value, not features
- [ ] Written for non-technical stakeholders AND system designers
- [ ] Success criteria are measurable (if Section 9 included)

## Requirement Completeness
- [ ] User roles and permissions are clear
- [ ] Data lifecycle (retention, deletion) specified
- [ ] Performance targets defined where relevant
- [ ] Error handling behaviors described
- [ ] Edge cases and error scenarios covered
- [ ] Integration boundaries identified (if external systems involved)
- [ ] Security/compliance needs addressed (if applicable)
- [ ] All [NEEDS CLARIFICATION] markers limited to max 3 critical items

## Design Readiness
- [ ] Sufficient detail for UI/UX design phase
- [ ] User journey shows all interaction points
- [ ] Sufficient detail for system architecture phase
- [ ] Key entities identified with relationships (if data persistence)
- [ ] Non-functional requirements specify constraints (if applicable)
- [ ] Dependencies and assumptions documented
- [ ] Scope is clearly bounded (Section 0)

## Traceability Verification
- [ ] Forward: All US# → J# → AC-US#.# → FR-US#.# → ENT# chain complete
- [ ] Backward: All FR-US#.# → AC-US#.# → US# chain complete
- [ ] No orphaned specifications (all items referenced)
- [ ] Coverage gaps documented in Section 8.2

---

## Execution Status
*Updated during specification generation process*

- [ ] User description parsed
- [ ] User context extracted (actors, goals, scenarios)
- [ ] User Stories generated (US#)
- [ ] Primary User Journey mapped (J#)
- [ ] Journey steps linked to User Stories
- [ ] Acceptance Criteria defined (AC-US#.#) for each User Story
- [ ] Functional Requirements derived (FR-US#.#) from Acceptance Criteria
- [ ] Key Entities identified (ENT#) - if applicable
- [ ] Traceability established (US# → J# → AC-US#.# → FR-US#.# → ENT#)
- [ ] Assumptions documented for informed guesses
- [ ] [NEEDS CLARIFICATION] markers limited to max 3 items
- [ ] Quality validation passed
- [ ] Clarifications resolved (if any)

## Validation Results

### Pass ✅
*List items that passed validation*

### Fail ❌
*List items that failed validation with specific issues*

### Pending Clarification ⏳
*List items blocked by [NEEDS CLARIFICATION] markers*

---

## Notes

- Items marked incomplete require spec updates before `/speckit.plan`
- Failed validations must be resolved before handoff
- Clarifications must be answered before design phase
```

#### **5.2 Run Initial Validation**

For each checklist item:
- **Pass**: Item meets requirements → Mark `[x]`
- **Fail**: Item has issues → Mark `[ ]` and document specific problem in "Validation Results"
- **N/A**: Item not applicable to this feature → Mark `[x]` and note reason

#### **5.3 Handle Validation Results**

**If ALL Items Pass**:
→ Mark checklist complete in "Validation Results"  
→ Proceed to Phase 6 (Completion Report)

**If Items Fail** (excluding clarification-related):
→ Document failures in "Validation Results - Fail ❌" section  
→ Update spec to address each issue  
→ Re-run validation (max 3 iterations)  
→ If still failing after 3 iterations:
  - Document remaining issues in checklist notes
  - Warn user: "Specification has quality issues that need manual review"
  - List specific issues and recommended fixes

**If [NEEDS CLARIFICATION] Markers Exist** (max 3):
→ Proceed to Phase 5.4 (Clarification Workflow)

#### **5.4 Clarification Workflow**

**Step 1: Extract Clarification Needs**

Find all `[NEEDS CLARIFICATION: ...]` markers in Section 4.5 of spec.

**Step 2: Format Questions for User**

Present each clarification as a structured question:

```markdown
## Question N: [Topic from marker]

**Context**: [Quote relevant spec section where this appears]

**What we need to know**: [Specific question from NEEDS CLARIFICATION marker]

**Why this matters**: [Impact statement from spec - what's blocked]

**Suggested Answers**:

| Option | Answer | Implications |
|--------|--------|--------------|
| A      | [First suggested answer] | [What this means for scope/design/implementation] |
| B      | [Second suggested answer] | [What this means for scope/design/implementation] |
| C      | [Third suggested answer] | [What this means for scope/design/implementation] |
| Custom | Provide your own answer below | You can describe a different approach |

**Your choice**: _Please respond with A, B, C, or Custom (with details)_
```

**CRITICAL - Table Formatting**:
- Align pipes consistently: `| Content |` not `|Content|`
- Header separator: `|--------|` (minimum 3 dashes)
- Space around cell content: `| Text |` not `|Text|`
- Test markdown rendering before presenting to user

**Step 3: Present All Questions**

- Number questions: Q1, Q2, Q3 (max 3 total)
- Present all questions together in one message
- Wait for user to respond to ALL questions

**Step 4: Wait for User Response**

Expect format: `Q1: A, Q2: Custom - [details], Q3: B`

**Step 5: Update Specification**

For each answered question:
- Locate corresponding `[NEEDS CLARIFICATION: ...]` marker in Section 4.5
- Replace marker with user's selected or provided answer
- Update requirement to be complete and unambiguous
- Remove from Section 4.5 (Requirements Needing Clarification)
- Add to appropriate Section 4.1-4.3 as normal requirement

**Step 6: Document Resolution**

In Section 7.1 (Assumptions), add note:
```markdown
- **[Assumption ID]**: [Clarification question that was resolved]
  *User selected*: [Option A/B/C or custom answer]
  *Validation needed from*: Product owner
  *Impact if changed*: [What would need to change]
```

**Step 7: Re-run Validation**

After all clarifications resolved:
- Update checklist: Mark "Clarifications resolved" as `[x]`
- Re-run full validation (Phase 5.2)
- Update "Validation Results" section

---

### Phase 6: Completion Report

#### **6.1 Update Checklist Final Status**

In `FEATURE_DIR/checklists/requirements.md`:
- Mark all "Execution Status" items as `[x]`
- Update "Validation Results" with final pass/fail/pending status
- Add completion timestamp

#### **6.2 Update Spec Document Status**

At bottom of `SPEC_FILE`:
- Update status: "Draft" → "Ready for Design" (if all passed)
- Add validation reference: "Validation Checklist: See `checklists/requirements.md`"

#### **6.3 Generate Completion Summary**

Report to user:

```markdown
✅ **Specification Generation Complete**

**Feature**: [Feature Name]
**Branch**: `BRANCH_NAME`
**Spec File**: `SPEC_FILE`
**Checklist**: `FEATURE_DIR/checklists/requirements.md`

**Generated Content**:
- User Stories: [count] (US1-US#)
- Journey Steps: [count] (J1-J#, including alternatives)
- Acceptance Criteria: [count] (AC-US#.#)
- Functional Requirements: [count] (FR-US#.#)
- Key Entities: [count] (ENT1-ENT#) or "N/A - no data persistence"
- Non-Functional Requirements: [count] or "N/A"

**Validation Status**:
- Structure & Completeness: [✓ Pass / ✗ Fail - # issues]
- Content Quality: [✓ Pass / ✗ Fail - # issues]
- Requirement Completeness: [✓ Pass / ✗ Fail - # issues]
- Design Readiness: [✓ Pass / ✗ Fail - # issues]
- Traceability: [✓ Pass / ✗ Fail - # issues]

**Traceability Coverage**:
- Forward trace complete: [Yes/No]
- Backward trace complete: [Yes/No]
- Orphaned items: [None / List items]

**Next Steps**:
[If all passed]
✅ Specification is ready for design phase
→ Use `/speckit.plan` to generate system design

[If clarifications resolved]
✅ All clarifications answered and incorporated
→ Specification ready for `/speckit.plan`

[If validation failed]
⚠️ Specification has quality issues:
[List specific issues]
→ Review and update spec before `/speckit.plan`

[If clarifications pending]
⏳ Awaiting clarifications:
[List unanswered questions]
→ Answer questions above to proceed
```

---

## Core Principles

### User-Centric Design
- ✅ Start with user goals, not system features
- ✅ Every requirement must trace back to user value
- ✅ Focus on WHAT users need, not HOW to build it

### Flow-Based Thinking
- ✅ Show complete end-to-end user journey
- ✅ Include decision points and alternative paths
- ✅ Map journey steps to user stories explicitly

### Testable & Traceable
- ✅ Every requirement must be verifiable (testable)
- ✅ Every requirement must trace to user story (traceable)
- ✅ Use Given-When-Then for all acceptance criteria

### No Technical Implementation
- ❌ Never specify programming languages, frameworks, databases
- ❌ Never describe APIs, data schemas, or architecture
- ❌ Never use technical jargon (unless in Glossary with definition)
- ✅ Describe system capabilities and user interactions only

### Complete Chain Requirement
Every specification must have:
**User Story (US#)** → **Journey Step (J#)** → **Acceptance Criteria (AC-US#.#)** → **Functional Requirement (FR-US#.#)** → **Entity (ENT#)** *(if applicable)*

---

## Numbering Convention Reference

| Element | Format | Example | Parent |
|---------|--------|---------|--------|
| User Story | US# | US1, US2, US3 | - |
| Journey Step | J# | J1, J2, J3 | - |
| Alternative Journey | Alt-J#.# | Alt-J2.1, Alt-J2.2 | J# |
| Acceptance Criterion | AC-US#.# | AC-US1.1, AC-US1.2 | US# |
| Edge Case Criterion | AC-US#.#-Edge | AC-US1.3-Edge | US# |
| Error Case Criterion | AC-US#.#-Error | AC-US1.4-Error | US# |
| Functional Requirement | FR-US#.# | FR-US1.1, FR-US1.2 | US# |
| Cross-Story Requirement | FR-X# | FR-X1, FR-X2 | Multiple US# |
| Entity | ENT# | ENT1, ENT2 | - |
| Non-Functional Req | NFR-[Type]# | NFR-P1, NFR-S1 | - |

**NFR Types**: P=Performance, S=Security, U=Usability, R=Reliability, SC=Scalability, C=Compliance

---

## Error Handling

**Empty Input**:
→ ERROR: "No feature description provided. Use `/speckit.specify [description]`"

**Script Execution Fails**:
→ ERROR: "Failed to initialize feature structure. Check script output: [error message]"

**Validation Fails After 3 Iterations**:
→ WARN: "Specification has persistent quality issues. Manual review required."
→ List specific issues and recommended fixes

**Traceability Gaps Found**:
→ ERROR: "Broken traceability chain: [list missing links]"
→ Must fix before proceeding to design phase

**Too Many Clarifications (>3)**:
→ Auto-resolve excess markers using informed guesses
→ Document resolutions in Section 7.1 (Assumptions)
→ Note in completion report: "X clarifications auto-resolved - validate with stakeholders"

---

## Quality Gates

**Before Writing Spec** (Phase 2 complete):
- [ ] All user stories focus on value, not features
- [ ] Journey shows complete user flow
- [ ] Every acceptance criterion is testable
- [ ] All requirements trace to acceptance criteria

**Before Validation** (Phase 4 complete):
- [ ] Spec file written with all mandatory sections
- [ ] Traceability matrix populated
- [ ] Assumptions documented for all informed guesses
- [ ] [NEEDS CLARIFICATION] limited to max 3 items

**Before Completion** (Phase 5 complete):
- [ ] Validation checklist created and executed
- [ ] All structural/content issues resolved OR documented
- [ ] Clarifications answered OR pending user response
- [ ] Checklist results documented

**Ready for `/speckit.plan`**:
- [ ] All validation items passed
- [ ] No [NEEDS CLARIFICATION] markers remain
- [ ] Traceability complete (forward & backward)
- [ ] Document status = "Ready for Design"

---

## Success Criteria for Specification

A complete specification must:

1. **Be Technology-Agnostic**: No mention of frameworks, languages, databases, or tools
2. **Be User-Focused**: Describe outcomes from user/business perspective, not system internals
3. **Be Verifiable**: Every requirement can be tested without knowing implementation details
4. **Be Traceable**: Every requirement traces back through acceptance criteria to user story
5. **Be Complete**: All mandatory sections filled, optional sections removed if N/A
6. **Be Unambiguous**: Single clear interpretation for each requirement
7. **Be Consistent**: Numbering follows convention, terminology is uniform
8. **Be Bounded**: Scope clearly defined in Section 0 with explicit in/out boundaries

---

## Notes for AI Generation

**When to Make Informed Guesses** (see Phase 3.1):
- Use industry standards and common patterns
- Document all assumptions in Section 7.1
- Prioritize reasonable defaults over clarification questions

**When to Mark [NEEDS CLARIFICATION]** (max 3):
- Critical scope decisions affecting development effort significantly
- Security/privacy requirements with legal/financial implications
- User permission models with multiple conflicting interpretations
- Business rules where no reasonable default exists

**Think Like a Tester**:
- Can I write a test case for this acceptance criterion?
- Can I verify this requirement without seeing code?
- Would two developers implement this the same way from description?
- If answer is "no" to any → requirement needs refinement

**Common Pitfall Avoidance**:
- ❌ "System stores data in PostgreSQL" → ✅ "System persists user content for 7 days"
- ❌ "API returns JSON response" → ✅ "System provides shareable link to user"
- ❌ "Use React hooks for state" → ✅ "Interface updates in real-time as user types"
- ❌ "Implement OAuth2" → ✅ "System authenticates users securely"

---

**Reminder**: This specification is the foundation for UI/UX design and system architecture. Focus on WHAT the system must do for users, never HOW to build it technically.