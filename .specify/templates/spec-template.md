# Feature Specification: [FEATURE NAME]

**Feature Branch**: `[###-feature-name]`  
**Created**: [DATE]  
**Status**: Draft  
**Input**: User description: "$ARGUMENTS"

---

## 0. Feature Overview *(mandatory)*

**Summary**: [2-3 sentence description of what this feature does and why it exists]

**Primary Value**: [Single sentence describing the main user/business benefit]

**Scope Boundary**: 
- **In Scope**: [What this feature includes]
- **Out of Scope**: [What this feature explicitly does NOT include - prevents scope creep]

*Example:*
**Summary**: A text-sharing service that allows users to quickly share text content via generated links without requiring authentication or accounts.

**Primary Value**: Enables instant, frictionless text sharing for users who need to quickly distribute content without email attachments or document sharing complexity.

**Scope Boundary**:
- **In Scope**: Text submission, link generation, link sharing, content expiration
- **Out of Scope**: User accounts, rich text formatting, file uploads, real-time collaboration, link editing after creation

---

## 1. User Stories & Actors *(mandatory)*

### 1.1 Actors

**Primary Actors**:
- **[Actor Role 1]**: [Brief description of who they are and their relationship to the system]
- **[Actor Role 2]**: [Brief description]

**Secondary Actors** *(if applicable)*:
- **[Actor Role 3]**: [Brief description]

*Example:*
**Primary Actors**:
- **Content Creator**: Individual who wants to share text content quickly (blog drafts, code snippets, meeting notes)
- **Content Recipient**: Person who receives and views the shared link

### 1.2 User Stories

**US1**: As a [user role], I want to [perform action/achieve goal], so that [business value/benefit]

**US2**: As a [user role], I want to [perform action/achieve goal], so that [business value/benefit]

**US3**: As a [user role], I want to [perform action/achieve goal], so that [business value/benefit]

*Example:*
**US1**: As a content creator, I want to submit text and instantly receive a shareable link, so that I can distribute content without email or file sharing complexity

**US2**: As a content creator, I want confirmation that my link was created successfully, so that I have confidence the content is accessible

**US3**: As a content recipient, I want to view shared text by clicking a link, so that I can access content without signing up or downloading files

---

## 2. Primary User Journey *(mandatory)*

### 2.1 Journey Map

```
Entry Point → [J1] → [J2] → [J3] → [J4] → Exit Point
              ↓       ↓       ↓       ↓
             US1     US1     US2     US2
```

**J1**: [User action/system state]  
*Enables: US# - [brief reference to user story]*  
*User sees: [specific UI elements/information]*  
*User does: [specific action]*

**J2**: [Next step in flow]  
*Enables: US# - [brief reference]*  
*User sees: [specific UI elements/information]*  
*User does: [specific action]*  
*Decision point: [if applicable - what choices does user have?]*

**J3**: [Next step]  
*Enables: US# - [brief reference]*  
*User sees: [specific UI elements/information]*  
*User does: [specific action]*

**J4**: [Final step]  
*Enables: US# - [brief reference]*  
*User sees: [specific UI elements/information]*  
*User does: [specific action]*

*Example:*

```
Landing Page → [J1] → [J2] → [J3] → [J4] → Share Link
                ↓       ↓       ↓       ↓
               US1     US1     US2     US3
```

**J1**: User arrives at app and enters content  
*Enables: US1 - Content creator wants to submit text*  
*User sees: Large text input field with placeholder "Paste your text here", character counter "0/10,000"*  
*User does: Pastes or types content*

**J2**: User triggers link generation  
*Enables: US1 - Content creator wants instant link*  
*User sees: "Generate Link" button (enabled when text present)*  
*User does: Clicks "Generate Link" button*

**J3**: System displays generated link  
*Enables: US2 - Content creator wants confirmation*  
*User sees: Unique URL with "Copy" button, "Share via Email/WhatsApp" options, expiration notice "Valid for 7 days"*  
*User does: Clicks "Copy to Clipboard"*

**J4**: User receives confirmation  
*Enables: US2 - Content creator wants confirmation*  
*User sees: "✓ Link copied! Valid for 7 days" toast message*  
*User does: Closes page or creates another link*

**J5**: Recipient accesses shared content  
*Enables: US3 - Recipient wants to view content*  
*User sees: Clean reading view with shared text, "Create Your Own" CTA*  
*User does: Reads content, optionally creates own link*

### 2.2 Alternative Paths

**Alt-J2.1**: Content exceeds character limit  
*Condition: User pastes text > 10,000 characters*  
*Flow: J1 → J2.1 → J1*  
*What happens: System truncates to 10,000 chars, shows warning banner "Text truncated to limit", user can edit*

**Alt-J3.1**: User wants to generate another link  
*Condition: User clicks "Create New Link" after J4*  
*Flow: J4 → J1 (journey restarts with cleared input)*  
*What happens: Previous link remains valid, new session starts*

**Alt-J5.1**: Recipient encounters expired link  
*Condition: Link is > 7 days old*  
*Flow: Entry → J5.1 (dead end)*  
*What happens: Shows "Content Expired" page with "Create Your Own Link" CTA*

### 2.3 Journey Exit Points

- **Success Exit (Primary)**: User successfully copies link at J4 and shares it externally
- **Success Exit (Secondary)**: Recipient successfully views content at J5
- **Abort Exit**: User closes browser at any point (no account, no data loss concern)
- **Error Exit**: System failure at J2 shows error message with retry option
- **Expiration Exit**: Recipient encounters expired link at J5.1, redirected to homepage

---

## 3. Acceptance Criteria *(mandatory)*

> **Purpose**: Define how we verify each user story is correctly implemented  
> **Format**: Given-When-Then scenarios for testing

### 3.1 For User Story US1: [Story Title]

**AC-US1.1**: **Given** [initial state/context], **When** [user action/trigger], **Then** [expected outcome/system response]

**AC-US1.2**: **Given** [initial state], **When** [action], **Then** [outcome]

**AC-US1.3**: **Given** [error condition], **When** [action], **Then** [error handling behavior]

**AC-US1.4-Edge**: **Given** [edge case condition], **When** [action], **Then** [boundary behavior]

*Example for US1:*

**AC-US1.1**: **Given** user has text in clipboard, **When** user pastes into input field, **Then** text appears immediately with character count displayed as "X/10,000"

**AC-US1.2**: **Given** valid text (1-10,000 chars) is entered, **When** user clicks "Generate Link", **Then** unique URL is displayed within 2 seconds with copy button enabled

**AC-US1.3**: **Given** empty input field, **When** user hovers over "Generate Link" button, **Then** button is disabled and tooltip shows "Please enter text first"

**AC-US1.4-Edge**: **Given** text contains exactly 10,000 characters, **When** user types one more character, **Then** input prevents additional characters and counter shows "10,000/10,000" in red

**AC-US1.5-Edge**: **Given** user pastes text with 15,000 characters, **When** paste completes, **Then** input truncates to 10,000 chars and shows warning banner "Text truncated to 10,000 character limit"

**AC-US1.6-Error**: **Given** network failure during link generation, **When** system attempts to create link, **Then** show error message "Unable to generate link. Please check your connection and try again" with retry button

### 3.2 For User Story US2: [Story Title]

**AC-US2.1**: **Given** [initial state], **When** [action], **Then** [outcome]

**AC-US2.2**: **Given** [initial state], **When** [action], **Then** [outcome]

**AC-US2.3-Edge**: **Given** [edge case], **When** [action], **Then** [boundary behavior]

### 3.3 For User Story US3: [Story Title]

**AC-US3.1**: **Given** [initial state], **When** [action], **Then** [outcome]

**AC-US3.2-Error**: **Given** [error scenario], **When** [action], **Then** [error handling]

---

## 4. Functional Requirements *(mandatory)*

> **Purpose**: Specify WHAT the system must do (not HOW to build it)  
> **Format**: "System MUST [capability]" - testable, unambiguous, traceable

### 4.1 Requirements for User Story US1: [Story Title]

**FR-US1.1**: System MUST [specific capability]  
*Traces to: US1, Journey J1, Acceptance AC-US1.1*  
*Rationale: [Why this is needed]*

**FR-US1.2**: System MUST [specific capability]  
*Traces to: US1, J2, AC-US1.2*  
*Rationale: [Why this is needed]*

**FR-US1.3**: System MUST [validation/constraint]  
*Traces to: US1, J2, AC-US1.3, AC-US1.4-Edge*  
*Rationale: [Why this is needed]*

*Example for US1:*

**FR-US1.1**: System MUST accept text input between 1 and 10,000 characters  
*Traces to: US1, J1, AC-US1.1, AC-US1.4-Edge, AC-US1.5-Edge*  
*Rationale: Balance between usability (sufficient space for most content) and system resource constraints*

**FR-US1.2**: System MUST generate a unique, unguessable identifier for each text submission  
*Traces to: US1, J2, AC-US1.2*  
*Rationale: Prevents unauthorized access via URL enumeration attacks*

**FR-US1.3**: System MUST validate input is not empty before enabling link generation  
*Traces to: US1, J2, AC-US1.3*  
*Rationale: Prevents creation of useless empty-content links that waste system resources*

**FR-US1.4**: System MUST display character count in real-time as user types  
*Traces to: US1, J1, AC-US1.1*  
*Rationale: Provides immediate feedback to help users stay within limits without trial-and-error*

**FR-US1.5**: System MUST complete link generation within 2 seconds for valid input  
*Traces to: US1, J2, AC-US1.2*  
*Rationale: Users expect instant feedback; delays beyond 2 seconds cause abandonment*

**FR-US1.6**: System MUST handle network failures gracefully with retry option  
*Traces to: US1, J2, AC-US1.6-Error*  
*Rationale: Prevents user frustration and data loss during temporary connectivity issues*

### 4.2 Requirements for User Story US2: [Story Title]

**FR-US2.1**: System MUST [specific capability]  
*Traces to: US2, J3, AC-US2.1*  
*Rationale: [Why this is needed]*

**FR-US2.2**: System MUST [specific capability]  
*Traces to: US2, J4, AC-US2.2*  
*Rationale: [Why this is needed]*

### 4.3 Requirements for User Story US3: [Story Title]

**FR-US3.1**: System MUST [specific capability]  
*Traces to: US3, J5, AC-US3.1*  
*Rationale: [Why this is needed]*

### 4.4 Cross-Story Requirements *(if applicable)*

> Requirements that affect multiple user stories or span the entire system

**FR-X1**: System MUST [capability affecting multiple user stories]  
*Traces to: US1, US2, Journey J1-J4, AC-US1.1, AC-US2.1*  
*Rationale: [Why this is needed]*

*Example:*

**FR-X1**: System MUST maintain consistent response time (<2 seconds) across all user-facing operations  
*Traces to: US1, US2, US3, J1-J5*  
*Rationale: Ensures predictable user experience regardless of action type*

**FR-X2**: System MUST be accessible via modern web browsers (Chrome, Firefox, Safari, Edge - latest 2 versions)  
*Traces to: US1, US2, US3, J1-J5*  
*Rationale: Supports 95%+ of target user base without requiring app installation*

### 4.5 Requirements Needing Clarification *(temporary - resolve before design phase)*

> Mark requirements where user input was ambiguous or incomplete

**FR-US?.?**: System MUST [NEEDS CLARIFICATION: specific question about requirement]  
*Traces to: [Story/Criteria]*  
*Impact: [What design decisions are blocked]*  
*Options: [Suggested alternatives if any]*

*Example:*

**FR-US4.1**: System MUST authenticate users via [NEEDS CLARIFICATION: auth method not specified - options include: email/password, social OAuth (Google/GitHub), magic link, or anonymous (no auth)]  
*Traces to: US4*  
*Impact: Cannot design user registration flow, data ownership model, or privacy controls*  
*Options: For MVP, recommend anonymous (no auth) to minimize friction; add auth in v2*

**FR-US4.2**: System MUST retain text content for [NEEDS CLARIFICATION: retention period not specified - options: 7 days (matches expiration), 30 days (extended access), 90 days (compliance), or indefinite]  
*Traces to: US4, Alt-J5.1*  
*Impact: Cannot determine storage requirements, data deletion workflow, or privacy policy*  
*Options: Recommend 7 days aligned with link expiration for consistency*

---

## 5. Key Entities *(mandatory for features with data persistence)*

> **Purpose**: Define WHAT information flows through the system (not database schema)  
> **Format**: Business entities with attributes, relationships, and lifecycle

**ENT1: [Entity Name]**
- **Purpose**: [What this entity represents in business terms]
- **Key Attributes**: [List main data points - business view, not database columns]
- **Lifecycle**: [How entity is created, modified, deleted]
- **Relationships**: [How it relates to other entities]
- **Business Rules**: [Constraints or validations]
- **Referenced by**: [Which user stories/requirements need this entity]

*Example:*

**ENT1: Text Snippet**
- **Purpose**: Represents user-submitted content for sharing via generated links
- **Key Attributes**: 
  - Content text (1-10,000 characters, plain text only)
  - Unique shareable identifier (unguessable, URL-safe)
  - Creation timestamp (ISO 8601 format)
  - Expiration timestamp (creation time + 7 days)
  - View count (incremented on each access)
  - Content hash (for detecting duplicates)
- **Lifecycle**: 
  - **Created**: When user successfully generates link (J2)
  - **Accessed**: When recipient opens link (J5) - increments view count
  - **Expired**: Automatically becomes inaccessible after 7 days from creation
  - **Deleted**: Permanently removed 24 hours after expiration (grace period for system cleanup)
- **Relationships**: 
  - Has many **Access Logs** (one per view event)
  - May belong to **User** (reserved for future authentication feature)
  - May reference **Parent Snippet** (reserved for future "edit link" feature)
- **Business Rules**:
  - Identifier must be globally unique across all time
  - Content cannot be modified after creation (immutable)
  - Expired snippets return 410 Gone status
  - Auto-deletion cannot be reversed
  - Duplicate content (same hash) creates new identifier
- **Referenced by**: US1, US2, US3, FR-US1.1, FR-US1.2, AC-US1.2, Alt-J5.1

**ENT2: Access Log**
- **Purpose**: Records each time a snippet is viewed for analytics and abuse detection
- **Key Attributes**:
  - Snippet identifier (foreign reference to ENT1)
  - Access timestamp (ISO 8601)
  - Referrer URL (where link was shared from, if available)
  - User agent (browser/device info)
  - IP address (anonymized for privacy)
  - Geographic region (country-level only)
- **Lifecycle**:
  - **Created**: Each time snippet link is accessed (J5)
  - **Retained**: 30 days for analytics
  - **Deleted**: Auto-deleted after retention period
- **Relationships**:
  - Belongs to **Text Snippet** (ENT1)
- **Business Rules**:
  - Multiple accesses from same IP within 5 minutes count as single view (deduplication)
  - IP addresses are hashed before storage (privacy protection)
  - Logs for expired snippets are retained for full 30-day period
- **Referenced by**: US3, FR-US3.1, NFR-P2 (abuse detection)

---

## 6. Non-Functional Requirements *(include if applicable)*

> **Purpose**: Define system qualities and constraints (performance, security, usability)  
> **Note**: Only include subsections relevant to this feature

### 6.1 Performance

**NFR-P1**: [Specific performance target with measurement]  
*Rationale: [Why this matters to users]*  
*Measurement: [How to verify - metrics and tools]*

*Example:*
**NFR-P1**: Link generation MUST complete within 2 seconds for 95% of requests (p95 latency)  
*Rationale: Users expect instant feedback; delays beyond 2 seconds cause abandonment*  
*Measurement: Monitor p95 latency via application performance monitoring (APM) in production*

**NFR-P2**: System MUST support [concurrent users/requests per second]  
*Rationale: [Business growth expectation]*  
*Measurement: Load testing with [tool] to verify capacity*

### 6.2 Security

**NFR-S1**: [Security requirement with specific control]  
*Rationale: [Risk being mitigated]*  
*Measurement: [How to verify compliance]*

*Example:*
**NFR-S1**: Generated URLs MUST contain minimum 128-bit entropy (e.g., 22+ character base64url identifier)  
*Rationale: Prevents unauthorized access via URL enumeration or brute-force attacks*  
*Measurement: Audit URL generation algorithm for cryptographically secure randomness*

**NFR-S2**: System MUST sanitize all user input to prevent XSS attacks  
*Rationale: Protects recipients from malicious scripts in shared content*  
*Measurement: Penetration testing with OWASP XSS vectors*

### 6.3 Usability *(if applicable)*

**NFR-U1**: [Usability requirement]  
*Rationale: [User experience goal]*  
*Measurement: [How to validate - user testing, accessibility audit]*

*Example:*
**NFR-U1**: Interface MUST be fully keyboard-navigable (no mouse required)  
*Rationale: Accessibility for keyboard-only users and screen reader compatibility*  
*Measurement: WCAG 2.1 Level AA audit, keyboard-only user testing*

### 6.4 Reliability *(if applicable)*

**NFR-R1**: [Availability or uptime target]  
*Rationale: [Business continuity requirement]*  
*Measurement: [Monitoring and SLA tracking]*

### 6.5 Scalability *(if applicable)*

**NFR-SC1**: [Growth target or scaling requirement]  
*Rationale: [Anticipated growth pattern]*  
*Measurement: [Load testing and capacity planning]*

### 6.6 Compliance *(if applicable)*

**NFR-C1**: [Legal or regulatory requirement]  
*Rationale: [Compliance mandate]*  
*Measurement: [Audit process]*

*Example:*
**NFR-C1**: System MUST comply with GDPR data retention and deletion requirements  
*Rationale: Legal requirement for processing EU user data*  
*Measurement: Privacy impact assessment, data flow audit*

---

## 7. Risks, Assumptions & Dependencies

> **Purpose**: Make constraints and unknowns explicit to unblock design and delivery

### 7.1 Assumptions

*List any assumptions made during requirements gathering. These should be validated with stakeholders.*

- **[Assumption A]**: [What we're assuming to be true]  
  *Validation needed from: [Stakeholder/team]*  
  *Impact if wrong: [Consequences]*

*Example:*
- **A1**: Users are comfortable with content auto-expiring after 7 days  
  *Validation needed from: Product owner, user research*  
  *Impact if wrong: May need configurable expiration or "extend link" feature*

- **A2**: Plain text (no formatting) is sufficient for MVP use cases  
  *Validation needed from: Target user interviews*  
  *Impact if wrong: May need rich text editor, increasing complexity*

- **A3**: No user authentication required for MVP  
  *Validation needed from: Product strategy, security team*  
  *Impact if wrong: Significant scope increase (registration, login, account management)*

### 7.2 Constraints

*Hard limits or requirements that cannot be changed within this feature's scope.*

- **[Constraint A]**: [Technical, budget, or policy limitation]  
  *Imposed by: [Source]*  
  *Mitigation: [How we're working within this constraint]*

*Example:*
- **C1**: Must use existing cloud infrastructure (no new platforms)  
  *Imposed by: IT policy, budget constraints*  
  *Mitigation: Design for deployment on current AWS environment*

- **C2**: Maximum storage budget of 100GB for first 6 months  
  *Imposed by: Initial budget allocation*  
  *Mitigation: Implement auto-deletion after 7 days to manage storage*

### 7.3 Dependencies

*External factors or resources that must be available for this feature to succeed.*

- **[Dependency A]**: [External service, API, or deliverable]  
  *Owner: [Team/vendor]*  
  *Required by: [Date/milestone]*  
  *Risk: [What happens if unavailable]*

*Example:*
- **D1**: Content Delivery Network (CDN) for global low-latency access  
  *Owner: Infrastructure team*  
  *Required by: Launch date*  
  *Risk: Without CDN, users outside primary region experience slow load times*

- **D2**: Legal review of Terms of Service for user-generated content  
  *Owner: Legal department*  
  *Required by: 2 weeks before launch*  
  *Risk: Cannot launch without ToS; may need to restrict content types*

### 7.4 Known Risks

*Potential issues identified during requirements phase.*

- **[Risk A]**: [Description of risk]  
  *Likelihood: [Low/Medium/High]*  
  *Impact: [Low/Medium/High]*  
  *Mitigation: [Planned response]*

*Example:*
- **R1**: Abuse risk - users may share spam or malicious links  
  *Likelihood: Medium*  
  *Impact: High (reputation damage, user trust)*  
  *Mitigation: Rate limiting, content reporting mechanism, abuse monitoring*

---

## 8. Traceability Matrix

> **Purpose**: Verify complete requirements coverage and no orphaned specifications

| User Story | Journey Steps | Acceptance Criteria | Functional Requirements | Entities |
|------------|---------------|---------------------|-------------------------|----------|
| US1 | J1, J2 | AC-US1.1, AC-US1.2, AC-US1.3, AC-US1.4-Edge, AC-US1.5-Edge, AC-US1.6-Error | FR-US1.1, FR-US1.2, FR-US1.3, FR-US1.4, FR-US1.5, FR-US1.6 | ENT1 |
| US2 | J3, J4 | AC-US2.1, AC-US2.2 | FR-US2.1, FR-US2.2, FR-X1 | ENT1 |
| US3 | J5, Alt-J5.1 | AC-US3.1, AC-US3.2-Error | FR-US3.1, FR-X2 | ENT1, ENT2 |

### 8.1 Coverage Verification

**Forward Traceability** (user value → implementation):
- [ ] All User Stories have Journey Steps? [✓/✗]
- [ ] All Journey Steps reference User Stories? [✓/✗]  
- [ ] All User Stories have Acceptance Criteria? [✓/✗]
- [ ] All Acceptance Criteria have Functional Requirements? [✓/✗]
- [ ] All Entities referenced by Requirements? [✓/✗]

**Backward Traceability** (implementation → user value):
- [ ] All Functional Requirements trace to Acceptance Criteria? [✓/✗]
- [ ] All Acceptance Criteria trace to User Stories? [✓/✗]
- [ ] All Entities referenced by at least one Requirement? [✓/✗]
- [ ] No orphaned specifications? [✓/✗]

### 8.2 Gaps Identified

*List any traceability gaps found:*

- [ ] **Gap 1**: [Description] - *Action: [How to resolve]*
- [ ] **Gap 2**: [Description] - *Action: [How to resolve]*

*Example:*
- [ ] **Gap 1**: FR-US1.7 has no corresponding Acceptance Criteria - *Action: Add AC-US1.X or remove requirement*
- [ ] **Gap 2**: AC-US2.3 not referenced by any Functional Requirement - *Action: Add FR-US2.X or remove criterion*

---

## 9. Success Metrics *(optional - include if feature has measurable goals)*

> **Purpose**: Define how to measure if this feature achieves its intended goals post-launch

**Usage Metrics**:
- [Metric 1]: [Target value] - *Measures: [What this indicates]*
- [Metric 2]: [Target value] - *Measures: [What this indicates]*

**Quality Metrics**:
- [Metric 1]: [Target value] - *Measures: [What this indicates]*

**Business Metrics**:
- [Metric 1]: [Target value] - *Measures: [What this indicates]*

*Example:*
**Usage Metrics**:
- Daily Active Links Created: 500+ within 30 days of launch - *Measures: Feature adoption*
- Link Sharing Rate: 80% of created links are accessed at least once - *Measures: Utility and actual sharing behavior*

**Quality Metrics**:
- Link Generation Success Rate: >99.5% - *Measures: System reliability*
- Average Generation Time: <1 second (p50) - *Measures: Performance perceived by users*

**Business Metrics**:
- User Retention: 25% of creators return within 7 days - *Measures: Product-market fit*
- Organic Growth: 30% of new users arrive via shared links - *Measures: Viral coefficient*

---

## 10. Glossary *(optional - include if domain-specific terms used)*

> **Purpose**: Define terms to ensure consistent understanding across stakeholders

**[Term 1]**: [Definition]

**[Term 2]**: [Definition]

*Example:*
**Link Expiration**: The point at which a generated URL becomes inaccessible (7 days after creation in this feature)

**Shareable Identifier**: The unique, unguessable portion of the URL that maps to stored content (e.g., "a7Bf9kL2pQ3x" in "https://app.com/s/a7Bf9kL2pQ3x")

**Content Hash**: A cryptographic fingerprint of the submitted text used to detect duplicate submissions

---

## Appendix A: Document Change Log

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | [DATE] | [NAME] | Initial draft from user input |
| 0.2 | [DATE] | [NAME] | Incorporated clarifications from Q1-Q3 |
| 1.0 | [DATE] | [NAME] | Validated and approved for design phase |

---

## Appendix B: References

*Links to related documents, research, or external resources:*

- **User Research**: [Link to user interviews or surveys]
- **Competitive Analysis**: [Link to analysis of similar features]
- **Technical Constraints**: [Link to infrastructure documentation]
- **Compliance Requirements**: [Link to legal/regulatory guidelines]

---

**Document Status**: ✅ Ready for `/speckit.plan` (System Design Phase)

**Validation Checklist**: See `FEATURE_DIR/checklists/requirements.md` for quality verification results