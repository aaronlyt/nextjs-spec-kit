---
description: Execute the implementation planning workflow using the plan template to generate Next.js Development design artifacts.
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).


## Overview

### What You're Doing
Transform a project specification into a complete Next.js development plan by following 8 sequential steps. Each step maps specific spec sections to implementation artifacts.

### Your Workflow

**This file (Instructions)**: Tells you WHAT to do and HOW to think  
**Template file**: Shows you EXACTLY WHAT your output should look like

**Your Workflow**:
1. **Setup**: Run `.specify/scripts/bash/setup-plan.sh --json` from repo root and parse JSON for FEATURE_SPEC, IMPL_PLAN , SPECS_DIR, BRANCH. For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\''m Groot' (or double-quote if possible: "I'm Groot").
2. **Load context**: 
   - Read FEATURE_SPEC and `.specify.specify/memory/constitution.md`. 
   - Load IMPL_PLAN template for output tempalte
   - Load .specify/templates/state_manage.md for state manage strategy guidelines
3. For each step (0-8):
   - Read the step instructions HERE
   - **Step [1-n]** must follow Architecture Foundation Decisions in Step [0]
   - Apply the logic to the spec
   - Write output using the format from TEMPLATE file
4. Verify completeness using checklist at the end


### Template File Structure
The template file contains:
- **All section headers** you must include
- **Placeholder syntax**: `[Entity]`, `[Type]`, `[ComponentName]` - replace with your spec's values
- **Code blocks**: Copy structure, fill in actual code
- **Tables**: Use same columns, populate with your data
- **Comments**: Show where to add spec references (US#, J#, etc.)

### How to Use Placeholders
When you see placeholders in the template:
- `[Entity]` → Replace with actual entity name (e.g., "TextSnippet")
- `[ComponentName]` → Replace with actual component (e.g., "SnippetForm")
- `[Type]` → Replace with TypeScript type (e.g., "string", "number")
- `[#]` → Replace with actual number (e.g., US1, J2, FR-US1.1)
- `[Purpose]` → Write specific purpose from spec
- `[file.tsx]` → Write actual file path

**Example from Template**:
```markdown
### US1: [User Story Title]
**From Spec**: "[Full user story text]"
```

**Your Output**:
```markdown
### US1: Quick Text Sharing
**From Spec**: "As a content creator, I want to submit text and instantly receive a shareable link"
```
---

## Step-by-Step Process

### STEP 0: Architecture Foundation
Input:
- Feature Overview (Summary, Scope, Objectives)
- $ARGUMENTS (user tech stack preferences and constraints)
- state manage strategy from state_manage.md

Output: the Section 0: Architecture Foundation in template 
- Tech Stack
- Rendering Strategy
- Out of Scope

What to do:
- Check $ARGUMENTS for user preferences (database, styling, deployment)
- Check state manage strategy from state_manage.md
- Read "In Scope" features
- Make core decisions using logic below
- Write output in simple table format

#### Decision Logic
1. Next.js Pattern
   - User wants Pages Router? → Pages Router
   - Otherwise → App Router
2. Rendering per Feature
   - Public + SEO → SSG
   - User-specific → SSR  
   - Highly interactive → CSR
   - Real-time → SSR Streaming
3. Database
   - User specifies → Use that
   - Complex relationships → PostgreSQL + Prisma
   - Flexible schema → MongoDB
   - Default → PostgreSQL + Prisma
4. Styling
   - User specifies → Use that
   - Custom design → Tailwind CSS
   - Default → Tailwind CSS
5. State Management
   - Follow 5-layer strategy from state_manage.md
     - Layer 1: Authentication (NextAuth.js/Auth0/Clerk)
     - Layer 2: Global App State (Context + useReducer/Zustand)
     - Layer 3: Server State (React Query/SWR)
     - Layer 4: Component State (useState/useReducer)
     - Layer 5: Form State (React Hook Form/Formik)
     - Use State Decision Matrix for layer selection
6. API Pattern
   - Forms only → Server Actions
   - Need public API → Server Actions + Route Handlers

7. Deployment
   - User specifies → Use that
   - Default → Vercel

---

### STEP 1: Feature Implementation Map
**Input**: User Stories (US#)  and Architecture Foundation Decisions
**Output**: Feature clusters with task lists

**What to do**:
1. For each US# → Create a "Feature" section
2. Break down user story into implementation tasks:
   - Form interactions → Component tasks (C#)
   - API needs → API tasks (A#)
   - Business logic → Utility tasks (Lib)
3. Number tasks sequentially (C1, C2, A1, A2, etc.)

**Mapping rules**:
- "submit/create" → POST API + Form component
- "view/display" → GET API + Display component  
- "edit/update" → PUT API + Edit form component
- "delete/remove" → DELETE API + Delete action

---

### STEP 2: UI Components, Routes & State
**Input**: User Journey (J#)   and Architecture Foundation Decisions
**Output**: Component hierarchy, routes, state management plan

**What to do**:
1. **For routes**: Look at J# entry/exit points
   - New entry point → New page (P#)
   - Same page flow → Single page with state changes
   
2. **For components**: Look at J# "User sees" statements
   - Each unique UI element → Component (C#)
   - Build component tree showing parent-child relationships

3. **For state**: Track J# state transitions (follow 5-layer strategy)
   - Authentication/session → NextAuth/Auth0 (Layer 1)
   - App-wide shared state → Context/Zustand (Layer 2)
   - Server data/API calls → React Query/SWR (Layer 3)
   - Component UI state → useState/useReducer (Layer 4)
   - Complex forms → React Hook Form/Formik (Layer 5)

4. **For navigation**: Map J# transitions
   - Different contexts → Route change (Link)
   - Same context → State change only

**Component naming**:
- Forms: `{Entity}Form` (e.g., SnippetForm)
- Display: `{Entity}Viewer` or `{Entity}Display`
- Lists: `{Entity}List` or `{Entity}Table`

---

### STEP 3: Test Cases
**Input**: Acceptance Criteria (AC-US#.#)   and Architecture Foundation Decisions
**Output**: Test specifications

**What to do**:
1. For each AC-US#.# → Create test case
2. Parse Given-When-Then format:
   - **Given** → Test setup code
   - **When** → User action (click, type, etc.)
   - **Then** → Assertion (expect...)

3. Choose test type:
   - Single component behavior → Unit test
   - Multiple components → Integration test
   - Full user flow → E2E test

**Test file naming**:
- Unit: `ComponentName.test.tsx`
- Integration: `feature-name.test.tsx`
- E2E: `user-journey.e2e.test.tsx`

---

### STEP 4: API, Validation & Business Logic
**Input**: Functional Requirements (FR-US#.#)   and Architecture Foundation Decisions
**Output**: API endpoints, validation schemas, utility functions

**What to do**:
1. **For APIs**: Look for "System MUST [create/read/update/delete]"
   - Create → POST endpoint
   - Read → GET endpoint
   - Update → PUT/PATCH endpoint
   - Delete → DELETE endpoint

2. **For validation**: Look for constraints (min/max, format, required)
   - Create Zod schema
   - Implement client-side validation (component)
   - Implement server-side validation (API route)

3. **For business logic**: Look for algorithms, calculations, transformations
   - Extract to `lib/` utilities
   - Make reusable and testable

**API structure**:
```
POST /api/[resource]        → Create
GET /api/[resource]/[id]    → Read one
GET /api/[resource]         → Read many (if needed)
PUT /api/[resource]/[id]    → Update
DELETE /api/[resource]/[id] → Delete
```

---

### STEP 5: Database Schema & Types
**Input**: Key Entities (ENT#)   and Architecture Foundation Decisions
**Output**: Prisma schema, TypeScript types, CRUD APIs

**What to do**:
1. **Database schema** (D# task):
   - For each ENT# attribute → Prisma field
   - For each relationship → @relation
   - Add indexes for performance

2. **TypeScript types**:
   - Create interface matching ENT# attributes
   - Create Input types (user-provided fields only)
   - Create Response types (returned to client)

3. **CRUD APIs** (A# tasks):
   - Check ENT# lifecycle:
     - "Created" → POST API
     - "Accessed/Read" → GET API
     - "Modified" → PUT API (if not forbidden)
     - "Deleted" → DELETE API (if not auto-deleted)

4. **UI Components** (C# tasks):
   - Check J# references:
     - "creates" → Form component
     - "views" → Display component
     - "edits" → Edit form component
     - "lists" → List/table component

**Skip if**:
- Business rule says "cannot be modified" → No PUT API
- Lifecycle says "auto-deleted" → No DELETE API
- Not in any journey → Defer to future

---

### STEP 6: Architecture & Infrastructure
**Input**: Non-Functional Requirements (NFR-*)   and Architecture Foundation Decisions
**Output**: Architecture decisions, config files, monitoring setup

**What to do**:
1. **Identify NFR type** from ID (NFR-P = Performance, NFR-S = Security, etc.)

2. **For each NFR**:
   - Choose architecture pattern to achieve it
   - Specify config files needed
   - Define validation test
   - Set up monitoring/alerts

**NFR type mappings**:
- **NFR-P** (Performance): Edge deployment, caching, optimization
- **NFR-S** (Security): Headers, encryption, input sanitization
- **NFR-U** (Usability): Accessibility, responsive design
- **NFR-R** (Reliability): Error handling, retries, fallbacks
- **NFR-SC** (Scalability): Connection pooling, load balancing

---

### STEP 7: Task Dependencies & Coverage
**Input**: Traceability Matrix  
**Output**: Dependency graph, coverage verification

**What to do**:
1. **Build dependency graph**:
   - Database schema (D#) → Must be first
   - APIs (A#) → Depend on D#
   - Components (C#) → Can parallel A#
   - Pages (P#) → Depend on C# + A#
   - Tests (T#) → Depend on code under test
   - Infrastructure (I#) → Last

2. **Verify coverage**:
   - Every US# → Has tasks
   - Every J# → Has pages/components
   - Every AC# → Has tests
   - Every FR# → Has implementation
   - Every ENT# → Has schema
   - Every NFR# → Has architecture decision

3. **Identify critical path**: Longest chain of dependencies

---

### STEP 8: Environment Configuration
**Input**: Deployment requirements   and Architecture Foundation Decisions
**Output**: Environment configs, scripts

**What to do**:
1. Create .env templates for:
   - Development (local dev)
   - Staging (pre-production)
   - Production (live)

2. Document environment-specific:
   - Database URLs
   - API endpoints
   - Feature flags
   - External service keys

3. Create npm scripts for each environment

---

## Output Requirements

### Structure your output using the template file exactly
- Use all section headings from template
- Fill in all placeholders (e.g., [Entity], [Type])
- Include code examples where shown
- Maintain markdown formatting

### Numbering conventions
- D# = Database tasks
- A# = API tasks  
- C# = Component tasks
- P# = Page tasks
- T# = Test tasks
- I# = Infrastructure tasks
- Lib = Utility tasks

### Traceability
- Always reference spec IDs (US#, J#, AC#, FR#, ENT#, NFR#)
- Show which spec item maps to which task
- Explain reasoning for decisions

### Completeness checklist
Before finishing, verify:
- [ ] All spec sections processed (0-7)
- [ ] All user stories have implementation tasks
- [ ] All journeys mapped to UI
- [ ] All acceptance criteria have tests
- [ ] All entities have schemas
- [ ] Dependencies are clear
- [ ] No orphaned tasks

---

## Common Mistakes to Avoid

❌ **Don't** create components not mentioned in journeys  
✅ **Do** only create components for J# "User sees" items

❌ **Don't** add features not in spec scope  
✅ **Do** mark out-of-scope items clearly

❌ **Don't** skip validation layers  
✅ **Do** implement both client and server validation

❌ **Don't** make tasks too granular (e.g., "add button")  
✅ **Do** group related work (e.g., "C1: Build SnippetForm component")

❌ **Don't** forget to trace tasks back to spec  
✅ **Do** include spec references (US#, J#, FR#, etc.)
