---
description: "Execute Next.js full-stack implementation from  plan.md and tasks.md"
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

---

## Outline

1. **Prerequisites Check**: Run `.specify/scripts/bash/check-prerequisites.sh --json --require-tasks --include-tasks` from repo root and parse FEATURE_DIR and AVAILABLE_DOCS list. All paths must be absolute. For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\''m Groot' (or double-quote if possible: "I'm Groot").

2. **Load Implementation Context**:
   - **REQUIRED**: Read `tasks.md` for the complete task list and execution plan
   - **REQUIRED**: Read `plan.md` for architecture decisions, tech stack, and specifications
   - **IF EXISTS**: Read `spec.md` for user story priorities and requirements

4. **Parse Task Structure and Extract**:
   - **Task phases**: Identify all phases (Setup, Database & Infrastructure, User Story phases, Optimization)
   - **Task details**: Extract ID, description, file paths, parallel markers [P], story labels [US#]
   - **Task dependencies**: Sequential vs parallel execution rules from tasks.md
   - **Execution flow**: Phase blocking rules (e.g., Database phase blocks all user stories)
   - **Checkpoints**: Independent validation criteria per user story from tasks.md
   - **Traceability**: Map tasks to plan.md sections and requirements

5. **Execute Implementation Following Task Plan**:
   - **Phase-by-phase execution**: Complete each phase defined in tasks.md before moving to next
   - **Respect dependencies**: Run sequential tasks in order, parallel tasks [P] can run together
   - **Follow TDD approach**: If tasks.md includes tests, execute test tasks before their corresponding implementation tasks (tests should fail initially)
   - **File-based coordination**: Tasks affecting the same files must run sequentially even if marked [P]
   - **Apply Next.js patterns**: Use architecture decisions from plan.md (Router type, rendering strategy, tech stack, validation approach, state management)
   - **Reference specifications**: For each task, consult plan.md sections for complete specifications:
     * Components → plan.md Section 2 (hierarchy, props, state, navigation)
     * APIs → plan.md Section 4 (endpoints, validation, business logic)
     * Database → plan.md Section 5 (schema, types, CRUD operations)
     * Tests → plan.md Section 3 (acceptance criteria, test specifications)
   - **Validation checkpoints**: Verify each phase completion per tasks.md checkpoint criteria before proceeding

6. **Implementation Execution Rules**:
   - **Database infrastructure first**: Complete database setup and core infrastructure before any user story work (as specified in tasks.md Phase 2)
   - **User story independence**: Each user story should be implementable and testable independently
   - **Story completion validation**: After each user story, verify it works standalone per tasks.md checkpoint
   - **Tests validation**: If tests included, ensure they fail initially (TDD), then pass after implementation
   - **Next.js conventions**: Follow framework patterns for file organization, API routes, components, and rendering strategies from plan.md Section 0

7. **Progress Tracking and Error Handling**:
   - Report progress after each completed task
   - **IMPORTANT**: For completed tasks, mark them as `[X]` in the tasks.md file
   - Halt execution if any non-parallel task fails
   - For parallel tasks [P], continue with successful tasks, report failed ones at end of parallel group
   - Provide clear error messages with context:
     ```
     ❌ Task T### failed: [Error message]
     
     Context:
     - Task: [Description]
     - File: [File path]
     - Phase: [Current phase]
     - Error: [Full error details]
     
     Suggested fixes:
     1. [Fix option 1]
     2. [Fix option 2]
     ```
   - For test failures during implementation (after TDD phase), stop and report which tests failed with details
   - Suggest next steps if implementation cannot proceed

8. **Completion Validation**:
   - Verify all required tasks in tasks.md are marked `[X]`
   - Check that implemented features match specifications in plan.md:
     * All user stories (Section 1) implemented
     * All functional requirements (Section 4) met
     * All entities (Section 5) in database
     * All NFRs (Section 6) addressed
     * All test cases (Section 3) passing (if tests included)
   - Validate technical implementation:
     * Next.js build succeeds: `npm run build`
     * TypeScript compilation clean: `npx tsc --noEmit`
     * All tests pass: `npm test` (if tests included)
     * Database migrations applied successfully
     * Environment variables configured per plan.md Section 8
   - Generate final status report:
     ```
     🎉 Implementation Complete!
     
     📊 Summary:
     - Total tasks: ### (###% complete)
     - Phases completed: #/#
     - User stories: #/#
     - Files created: ###
     - Files modified: ###
     - Tests passing: ###/### (if applicable)
     
     ✅ Completed Features:
     [List user stories from plan.md Section 1]
     
     📁 Key Files:
     [List created files by category: Database, API, Components, Pages]
     
     🔍 Validation Status:
     - Build: [Status]
     - TypeScript: [Status]
     - Tests: [Status] (if applicable)
     - Database: [Status]
     
     🚀 Next Steps:
     1. Review implementation against spec.md
     2. Perform manual testing of user journeys from plan.md Section 1
     3. Run E2E tests if available
     4. Deploy to staging environment
     5. Update documentation
     
     📝 Notes:
     [Any warnings, todos, or follow-up items]
     ```
   - Confirm the implementation follows the technical plan from plan.md

---

## Next.js-Specific Implementation Notes

### File Organization Reference
When implementing tasks, follow Next.js conventions from plan.md Section 0:

**App Router structure** (if chosen):
```
app/
├── api/              # API routes
├── [route]/          # Page routes
└── layout.tsx        # Root layout
```

**Pages Router structure** (if chosen):
```
pages/
├── api/              # API routes
└── [route].tsx       # Page routes
```

**Common structure** (both routers):
```
components/           # React components
lib/
├── db.ts            # Database connection
├── validation/      # Zod schemas
└── [services].ts    # Business logic
types/               # TypeScript types
prisma/
└── schema.prisma    # Database schema
```

### Code Pattern Guidelines

Refer to plan.md for specific patterns:
- **API routes**: Section 4 (endpoints, validation, error handling)
- **Validation**: Section 4 (Zod schemas, client + server validation)
- **Components**: Section 2 (state management, props, event handlers)
- **Database**: Section 5 (Prisma models, types, CRUD operations)
- **Rendering**: Section 2 (CSR/SSR/SSG per route)

### Critical Implementation Principles

1. **Sequential execution**: Respect task order and dependencies from tasks.md
2. **Parallel opportunities**: Execute [P] tasks simultaneously when safe (different files, no dependencies)
3. **Test-driven**: If tasks.md includes tests, write and run tests before implementation (TDD)
4. **Checkpoint validation**: Verify each phase/story meets criteria from tasks.md before proceeding
5. **Error recovery**: Provide clear error context and suggested fixes
6. **Progress visibility**: Update tasks.md with `[X]` and report status regularly
7. **Plan adherence**: Follow all architecture decisions and specifications from plan.md
8. **Type safety**: Ensure TypeScript compilation succeeds at each phase
9. **Database integrity**: Validate Prisma schema and migrations before proceeding
10. **User story independence**: Each story from plan.md Section 1 should work standalone

---
