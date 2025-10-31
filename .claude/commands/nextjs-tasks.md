---
description: "Generate actionable Next.js tasks from plan.md"
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

---

## Execution Instructions

**Follow the workflow steps sequentially in order 1 → 2 → 3 → 4 → 5 → 6.**

Each step must be completed before proceeding to the next. Do not skip steps or change the order.

---

## Workflow

### 1. Prerequisites Check

Run `.specify/scripts/bash/check-prerequisites.sh --json` from repo root.
Parse: `FEATURE_DIR`, `AVAILABLE_DOCS` (all paths absolute).

**Required docs in FEATURE_DIR:**
- `plan.md` (filled plan template - MUST exist)
- `spec.md` (optional - for user story priorities)

---

### 2. Load Plan Document

Read `FEATURE_DIR/plan.md` and extract all 8 sections:

| Section | Content to Extract | Used For |
|---------|-------------------|----------|
| 0 | Architecture decisions, tech stack, rendering strategy | Phase 1 setup tasks |
| 1 | User stories with C#/A#/Lib# task IDs, purposes, traceability | User story phases structure |
| 2 | Component hierarchy, state management, navigation, routes | Component/Page task details |
| 3 | Test cases, acceptance criteria mapping | Test task specifications |
| 4 | API specs, validation schemas, business logic | API task details |
| 5 | Prisma schemas, TypeScript types, CRUD operations | Database task details |
| 6 | NFRs (performance, security, usability) | Optimization tasks |
| 7 | Dependencies, traceability matrix | Reference only |
| 8 | Environment config | Setup tasks |

**Extraction principle**: Extract once, reference multiple times in task generation.

---

### 3. Generate Task Phases

Use `.specify/templates/nextjs-task-template.md` as structure template.

**CRITICAL**: Replace ALL sample tasks (T001-T014, T015+) with actual tasks generated from plan.md. The template tasks are examples only - do NOT copy them.

#### Phase 1: Setup (T001-T006)
**Source**: Section 0 + Section 8

**Generate** setup tasks based on actual plan decisions:
- Next.js version and router choice (App/Pages from Section 0 "Architecture Pattern Decision")
- Actual dependencies from Section 0 "Technology Stack" (not template examples)
- Actual styling approach (Tailwind/CSS Modules from Section 0)
- Actual environment variables from Section 8 (not generic .env.local)

**Example generation**:
```
If Section 0 says "Next.js 14 with Pages Router" and "Styling: CSS Modules":
✅ T002 Initialize Next.js 14 with Pages Router
✅ T005 Setup CSS Modules configuration
❌ NOT: "Initialize Next.js 15+ with App Router" (template example)
```

#### Phase 2: Database & Infrastructure (T007-T014)
**Source**: Section 5 + Section 4 (base utilities)

**Generate** infrastructure tasks based on actual plan:
- Actual database provider from Section 5 "Database Schema" (PostgreSQL/MySQL/MongoDB/SQLite)
- Actual entities from Section 5 "ENT1, ENT2..." (not generic "core entities")
- Actual validation structure from Section 4 schemas
- Actual middleware needs from Section 6 NFRs

**Example generation**:
```
If Section 5 defines ENT1: Task, ENT2: User, ENT3: Project:
✅ T008 Create Prisma schema with Task, User, Project models
❌ NOT: "Create base Prisma schema with core entities" (too generic)
```

**Critical**: This phase BLOCKS all user story work.

#### Phase 3: User Story Phases (T015+)
**Source**: Section 1 (task structure) + Sections 2-5 (task details)

**For each user story in Section 1:**

1. **Extract task IDs from Section 1**:
   - Component tasks: C1, C2, C3...
   - API tasks: A1, A2, A3...
   - Utility tasks: Lib-name1, Lib-name2...
   - Entity references: ENT1, ENT2...

2. **Lookup specifications in other sections**:
   - **C# tasks** → Section 2: component hierarchy, props, state, navigation
   - **A# tasks** → Section 4: endpoint specs, validation, business logic
   - **ENT# refs** → Section 5: Prisma schema, types, CRUD operations
   - **All tasks** → Section 3: test requirements and acceptance criteria

3. **Generate integrated task descriptions**:
   ```
   Component task format:
   "Build [C#]: [ComponentName] in components/[path].tsx
    - UI: [elements from Section 1]
    - State: [management approach from Section 2]
    - Implements: [J#, FR# from Section 1 traceability]"
   
   API task format:
   "Create [A#]: [METHOD] /api/[endpoint] in app/api/[path]/route.ts
    - Purpose: [from Section 1]
    - Request/Response: [structures from Section 4]
    - Validation: [schema name from Section 4]
    - Implements: [FR#, ENT# from Section 1 traceability]"
   
   Database task format:
   "Add [ENT#]: [EntityName] model to prisma/schema.prisma
    - Fields: [from Section 5 Prisma schema]
    - Lifecycle: [Created/Accessed/Modified/Deleted from Section 5]
    - Types: Create types/[entity].ts with [interface from Section 5]"
   ```

4. **Generate task groups per user story**:
   - Tests (if requested): T###, T###, T### (from Section 3)
   - Database: T###, T### (ENT# models + migrations)
   - API: T###, T### (A# endpoints + validation)
   - UI: T###, T### (C# components + styling)
   - Pages: T###, T### (P# routes with rendering strategy from Section 2)
   - Integration: T###, T### (connect components to APIs)

5. **Apply task rules**:
   - Different files → mark [P] for parallel
   - Same file → sequential (no [P])
   - Add [US#] label to each task
   - Include checkpoint after each story

#### Final Phase: Optimization (T###)
**Source**: Section 6 (NFRs)

Generate optimization tasks for:
- Performance (NFR-P#)
- Security (NFR-S#)
- Usability (NFR-U#)
- Production deployment
- Monitoring

---

### 4. Generate Traceability (Tasks Perspective)

Build **new** traceability matrices in tasks.md:

#### Section → Task Mapping
```
| Plan Section | Task Group | Task Numbers | What It Covers |
|--------------|------------|--------------|----------------|
| Section 0    | Phase 1    | T001-T006    | [from generation] |
| Section 5    | Phase 2    | T007-T014    | [from generation] |
| Section 1    | US Phases  | T015+        | [from generation] |
...
```

#### User Story → Tasks Mapping
```
| User Story | Phase   | DB Tasks | API Tasks | UI Tasks | ... |
|------------|---------|----------|-----------|----------|-----|
| US1        | Phase 3 | T###-### | T###-###  | T###-### | ... |
| US2        | Phase 4 | T###-### | T###-###  | T###-### | ... |
...
```

#### Requirement → Task Traceability
```
| Requirement | ID  | Implementation Tasks | Test Tasks |
|-------------|-----|---------------------|------------|
| US1         | ... | T###-###            | T###-###   |
| FR-US1.1    | ... | T###, T###          | T###       |
| ENT1        | ... | T###, T###, T###    | T###       |
...
```

**Build from**: 
- Plan Section 1 traceability (US → J# → FR#)
- Generated task numbers
- Plan Section 3 acceptance criteria mapping

---

### 5. Apply Next.js Patterns

Ensure all tasks follow Next.js conventions:

- **Paths**: `app/`, `app/api/`, `components/`, `lib/`, `types/`, `prisma/`
- **Rendering**: Explicit CSR/SSR/SSG per route (from Section 2)
- **API**: App Router patterns (`route.ts`)
- **Validation**: Client + Server with Zod (from Section 4)
- **State**: Strategy from Section 2 (useState/Context/Zustand)
- **Styling**: Approach from Section 0 (Tailwind/CSS Modules)

---

### 6. Output & Report

**Write**: `FEATURE_DIR/tasks.md`

**Report summary**:
```
✅ Generated: /path/to/tasks.md

📊 Task Statistics:
- Total tasks: ###
- Phase 1 (Setup): # tasks
- Phase 2 (Infrastructure): # tasks  
- Phase 3+ (User Stories): # tasks across # stories
- Final Phase (Optimization): # tasks

🎯 MVP Scope: User Story 1 only (T###-T###)

⚡ Parallel Opportunities: 
- Phase 2: # tasks can run in parallel
- Each user story: # parallel tasks identified

🔗 Traceability: 
- All tasks mapped to plan sections
- All FRs traced to implementation
- All ACs traced to tests

📋 Critical Path: Setup → Database → US1 → US2 → ... → Optimization
```

