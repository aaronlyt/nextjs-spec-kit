---
description: "Next.js full-stack task list template for LLM implementation"
---

# Tasks: [FEATURE NAME] - Next.js Full-Stack

**Input**: Design documents from `/specs/[###-feature-name]/`
**Prerequisites**: plan.md (required), spec.md (optional - for user story priorities)

**Framework**: Next.js [version] with [App/Pages] Router
**Architecture**: [Rendering strategy from plan]
**Tests**: React Testing Library + Playwright (OPTIONAL - only if explicitly requested)

**Organization**: Tasks organized by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`
- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact Next.js file paths in descriptions

## Next.js Path Conventions
- **App Router**: `app/`, `app/api/`, `components/`, `lib/`, `types/`
- **Pages Router**: `pages/`, `pages/api/`, `components/`, `lib/`, `types/`
- **Database**: `prisma/`, `lib/db.ts`
- **Styles**: `styles/globals.css`, `components/` with CSS Modules or Tailwind

<!--
  ============================================================================
  IMPORTANT: The tasks below are SAMPLE TASKS for illustration purposes only.

  The /speckit.tasks command MUST replace these with actual tasks based on:
  - User stories from spec.md (with their priorities P1, P2, P3...)
  - Architecture decisions from plan.md
  - Components, APIs, and database schema from plan.md
  - Rendering strategies and routing from plan.md

  Tasks MUST be organized by user story so each story can be:
  - Implemented independently
  - Tested independently
  - Delivered as an MVP increment

  DO NOT keep these sample tasks in the generated tasks.md file.
  ============================================================================
-->

## Phase 1: Next.js Project Setup
**References**: Plan Section 0 (Architecture Foundation), Section 8 (Environment Configuration)

**Purpose**: Initialize Next.js project with required dependencies and configuration

<!-- 
  TODO: Generate setup tasks based on plan.md Section 0 and Section 8
  
  Example tasks (REPLACE with actual from plan):
  - [ ] T001 Create Next.js project structure per plan.md Section 0 architecture decisions
  - [ ] T002 Initialize Next.js [VERSION] with [App/Pages] Router (from Section 0)
  - [ ] T003 [P] Install dependencies: [ACTUAL_DEPENDENCIES from Section 0]
  - [ ] T004 [P] Configure TypeScript and ESLint
  - [ ] T005 [P] Setup [ACTUAL_STYLING from Section 0]
  - [ ] T006 [P] Configure environment variables per Section 8
-->

---

## Phase 2: Database & Core Infrastructure
**References**: Plan Section 5 (Database Schema, Types & CRUD), Section 4 (API, Validation, Business Logic & Utilities)

**Purpose**: Setup database schema and core services that ALL user stories depend on

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

<!-- 
  TODO: Generate infrastructure tasks based on plan.md Section 5 and Section 4
  
  Example tasks (REPLACE with actual from plan):
  - [ ] T007 Setup Prisma with [DATABASE_PROVIDER from Section 5]
  - [ ] T008 Create Prisma schema with [ENT1, ENT2, ENT3... from Section 5]
  - [ ] T009 [P] Generate Prisma client and create initial migration
  - [ ] T010 Create database connection utility in lib/db.ts
  - [ ] T011 [P] Setup base API error handling middleware
  - [ ] T012 [P] Create validation schemas structure in lib/validation/
  - [ ] T013 [P] Setup base types structure in types/
  - [ ] T014 Configure Next.js middleware per Section 6 NFRs
-->

**Checkpoint**: Database and core infrastructure ready - user story implementation can now begin

## Phase 3: User Story 1 - [Title] (Priority: P1) 🎯 MVP
**References**: Plan Section 1 (Feature Implementation Map), Section 2 (UI Components, Routes, State & Navigation), Section 3 (Test Cases)

**Goal**: [Brief description of what this story delivers] (US1 from plan.md)
**Rendering Strategy**: [CSR/SSR/SSG] per plan.md Section 2 (Route Mapping)
**Independent Test**: [How to verify this story works on its own] (Journey J# from plan.md)

### Tests for User Story 1 (OPTIONAL - only if tests requested) ⚠️
**References**: Plan Section 3 (Test Cases) - Acceptance Criteria Mapping

**NOTE: Write these tests FIRST, ensure they FAIL before implementation**

- [ ] T015 [P] [US1] Unit test for [ComponentName] in __tests__/components/[ComponentName].test.tsx (AC-US1.#, validates FR-US1.#)
- [ ] T016 [P] [US1] API test for [endpoint] in __tests__/api/[endpoint].test.ts (AC-US1.#, validates A# endpoint)
- [ ] T017 [US1] E2E test for [user journey] in __tests__/e2e/[journey].spec.ts (Journey J#, AC-US1.#)

### Database & Models for User Story 1
**References**: Plan Section 5 (Database Schema, Types & CRUD) - ENT# models and types

- [ ] T018 [P] [US1] Add [Entity1] model to prisma/schema.prisma (ENT1 from plan.md Section 5)
- [ ] T019 [P] [US1] Add [Entity2] model to prisma/schema.prisma (ENT2 from plan.md Section 5)
- [ ] T020 [US1] Generate migration for user story 1 entities (ENT# lifecycle implementation)
- [ ] T021 [P] [US1] Create TypeScript types for [Entity1] in types/[entity1].ts (Plan Section 5: Type Definitions)
- [ ] T022 [P] [US1] Create TypeScript types for [Entity2] in types/[entity2].ts (Plan Section 5: Type Definitions)

### API Endpoints for User Story 1
**References**: Plan Section 4 (API, Validation, Business Logic & Utilities) - A# endpoints and validation

- [ ] T023 [US1] Create [METHOD] /api/[endpoint1] route in app/api/[endpoint1]/route.ts (A1 from plan.md Section 4, implements FR-US1.#)
- [ ] T024 [US1] Create [METHOD] /api/[endpoint2] route in app/api/[endpoint2]/route.ts (A2 from plan.md Section 4, implements FR-US1.#)
- [ ] T025 [P] [US1] Create validation schema for [endpoint1] in lib/validation/[entity1].ts (Plan Section 4: Validation Schemas, FR-US1.#)
- [ ] T026 [P] [US1] Create validation schema for [endpoint2] in lib/validation/[entity2].ts (Plan Section 4: Validation Schemas, FR-US1.#)
- [ ] T027 [US1] Add business logic for [ServiceName] in lib/[service-name].ts (Plan Section 4: Business Logic & Utilities, FR-US1.#)

### UI Components for User Story 1
**References**: Plan Section 2 (UI Components, Routes, State & Navigation) - C# components

- [ ] T028 [P] [US1] Create [ComponentA] in components/[ComponentA].tsx (C1 from plan.md Section 2, implements J#)
- [ ] T029 [P] [US1] Create [ComponentB] in components/[ComponentB].tsx (C2 from plan.md Section 2, implements J#)
- [ ] T030 [P] [US1] Create [ComponentC] in components/[ComponentC].tsx (C3 from plan.md Section 2, implements J#)
- [ ] T031 [P] [US1] Add form validation to [FormComponent] using React Hook Form + Zod (Plan Section 4: Validation Schemas)
- [ ] T032 [P] [US1] Add styling to components using [Tailwind/CSS Modules] (Plan Section 0: Technology Stack)

### Pages/Routes for User Story 1
**References**: Plan Section 2 (UI Components, Routes, State & Navigation) - P# pages and routing

- [ ] T033 [US1] Create [route-1] page in app/[route-1]/page.tsx (P1 from plan.md Section 2, renders: [CSR/SSR/SSG])
- [ ] T034 [US1] Create [route-2] page in app/[route-2]/page.tsx (P2 from plan.md Section 2, renders: [CSR/SSR/SSG])
- [ ] T035 [US1] Implement client-side state management for [route-1] if needed (Plan Section 2: State Management Strategy)
- [ ] T036 [US1] Add navigation between routes using Next.js Link/router (Plan Section 2: Navigation Strategy)
- [ ] T037 [US1] Add loading states and error handling to pages (Plan Section 6: NFR-Performance)

### Integration for User Story 1

- [ ] T038 [US1] Connect [ComponentA] to /api/[endpoint1] with proper error handling
- [ ] T039 [US1] Connect [ComponentB] to /api/[endpoint2] with proper error handling
- [ ] T040 [US1] Implement form submission handling with validation feedback
- [ ] T041 [US1] Add optimistic updates or loading indicators as needed

**Checkpoint**: User Story 1 should be fully functional with API integration and independently testable


## Phase 4: User Story 2 - [Title] (Priority: P2)
**References**: Plan Section 1 (Feature Implementation Map), Section 2 (UI Components, Routes, State & Navigation), Section 3 (Test Cases)

**Goal**: [Brief description of what this story delivers] (US2 from plan.md)
**Rendering Strategy**: [CSR/SSR/SSG] per plan.md Section 2 (Route Mapping)
**Independent Test**: [How to verify this story works on its own] (Journey J# from plan.md)

### Tests for User Story 2 (OPTIONAL - only if tests requested) ⚠️
**References**: Plan Section 3 (Test Cases) - Acceptance Criteria Mapping

- [ ] T042 [P] [US2] Unit test for [ComponentName] in __tests__/components/[ComponentName].test.tsx (AC-US2.#, validates FR-US2.#)
- [ ] T043 [P] [US2] API test for [endpoint] in __tests__/api/[endpoint].test.ts (AC-US2.#, validates A# endpoint)
- [ ] T044 [US2] E2E test for [user journey] in __tests__/e2e/[journey].spec.ts (Journey J#, AC-US2.#)

### Database & Models for User Story 2

- [ ] T045 [P] [US2] Add [Entity3] model to prisma/schema.prisma
- [ ] T046 [US2] Generate migration for user story 2 entities
- [ ] T047 [P] [US2] Create TypeScript types for [Entity3] in types/[entity3].ts

### API Endpoints for User Story 2

- [ ] T048 [US2] Create [METHOD] /api/[endpoint3] route in app/api/[endpoint3]/route.ts
- [ ] T049 [P] [US2] Create validation schema for [endpoint3] in lib/validation/[entity3].ts
- [ ] T050 [US2] Add business logic for [ServiceName2] in lib/[service-name2].ts

### UI Components for User Story 2

- [ ] T051 [P] [US2] Create [ComponentD] in components/[ComponentD].tsx
- [ ] T052 [P] [US2] Create [ComponentE] in components/[ComponentE].tsx
- [ ] T053 [P] [US2] Add form validation to [FormComponent2] using React Hook Form + Zod

### Pages/Routes for User Story 2

- [ ] T054 [US2] Create [route-3] page in app/[route-3]/page.tsx (renders: [CSR/SSR/SSG])
- [ ] T055 [US2] Implement client-side state management for [route-3] if needed
- [ ] T056 [US2] Add navigation to/from existing routes

### Integration for User Story 2

- [ ] T057 [US2] Connect [ComponentD] to /api/[endpoint3] with proper error handling
- [ ] T058 [US2] Integrate with User Story 1 components if needed (shared state, data flow)

**Checkpoint**: User Stories 1 AND 2 should both work independently and together



## Phase 5: User Story 3 - [Title] (Priority: P3)

**Goal**: [Brief description of what this story delivers]
**Rendering Strategy**: [CSR/SSR/SSG] per plan.md
**Independent Test**: [How to verify this story works on its own]

### Tests for User Story 3 (OPTIONAL - only if tests requested) ⚠️

- [ ] T059 [P] [US3] Unit test for [ComponentName] in __tests__/components/[ComponentName].test.tsx
- [ ] T060 [P] [US3] API test for [endpoint] in __tests__/api/[endpoint].test.ts
- [ ] T061 [US3] E2E test for [user journey] in __tests__/e2e/[journey].spec.ts

### Implementation for User Story 3

- [ ] T062 [P] [US3] Add [Entity4] model to prisma/schema.prisma
- [ ] T063 [US3] Generate migration for user story 3 entities
- [ ] T064 [US3] Create /api/[endpoint4] route with validation
- [ ] T065 [P] [US3] Create [ComponentF] and [ComponentG] components
- [ ] T066 [US3] Create [route-4] page with proper rendering strategy
- [ ] T067 [US3] Integrate with existing user stories

**Checkpoint**: All user stories should now be independently functional and integrated



[Add more user story phases as needed, following the same pattern]



## Phase N: Next.js Optimization & Production

**Purpose**: Next.js-specific optimizations and production deployment

- [ ] TXXX [P] Optimize images using Next.js Image component
- [ ] TXXX [P] Implement dynamic imports for code splitting
- [ ] TXXX [P] Add metadata and SEO optimization to pages
- [ ] TXXX [P] Configure caching strategies (ISR, revalidate, etc.)
- [ ] TXXX Setup environment-specific builds (development, staging, production)
- [ ] TXXX Configure deployment platform (Vercel, Netlify, etc.)
- [ ] TXXX Add performance monitoring (Vercel Analytics, etc.)
- [ ] TXXX Run production build validation and Lighthouse audits
- [ ] TXXX [P] Documentation updates in README.md
- [ ] TXXX [P] Additional unit tests (if requested) in __tests__/unit/
- [ ] TXXX Security hardening (CSRF, XSS protection, etc.)




## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately
- **Database & Infrastructure (Phase 2)**: Depends on Setup completion - BLOCKS all user stories
- **User Stories (Phase 3+)**: All depend on Database & Infrastructure phase completion
  - User stories can then proceed in parallel (if staffed)
  - Or sequentially in priority order (P1 → P2 → P3)
- **Optimization (Final Phase)**: Depends on all desired user stories being complete

### Within Each User Story

1. **Database First**: Models and migrations before any other work
2. **Types**: TypeScript interfaces immediately after models
3. **API Before UI**: API routes must exist before components can connect
4. **Components Before Pages**: Build components, then assemble into pages
5. **Integration Last**: Connect everything together with error handling

### Parallel Opportunities Within Each Story

- All tests marked `[P]` can run in parallel (write first, ensure they fail)
- Database models can run in parallel (if no relationships)
- TypeScript types can run in parallel with API development
- Components can run in parallel (if no shared dependencies)
- Different user stories can be worked on in parallel by different team members



## Next.js Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Next.js Setup
2. Complete Phase 2: Database & Infrastructure (CRITICAL)
3. Complete Phase 3: User Story 1 with full Next.js patterns
4. **STOP and VALIDATE**: Test User Story 1 independently
5. Deploy MVP to Vercel/staging

### Incremental Delivery

1. Complete Setup + Database Infrastructure → Foundation ready
2. Add User Story 1 → Test independently → Deploy (MVP!)
3. Add User Story 2 → Test independently → Deploy
4. Add User Story 3 → Test independently → Deploy
5. Each story adds value without breaking previous stories

### Parallel Team Strategy

With multiple developers:

1. Team completes Setup + Database Infrastructure together
2. Once Database Infrastructure is done:
   - Developer A: User Story 1 (Frontend focus)
   - Developer B: User Story 2 (API focus)
   - Developer C: User Story 3 (Database focus)
3. Stories complete and integrate independently



## Next.js-Specific Notes

- **[P] tasks** = different files, no dependencies
- **[Story] label** maps task to specific user story for traceability
- **Rendering Strategy**: Follow the CSR/SSR/SSG decisions from plan.md exactly
- **API Routes**: Use Next.js App Router API patterns (`app/api/*/route.ts`)
- **Database**: Always use Prisma migrations, never manual database changes
- **Validation**: Client + server validation using Zod schemas
- **State Management**: Use useState/Context as decided in plan.md
- **Styling**: Consistent with chosen approach (Tailwind/CSS Modules)
- **Testing**: RTL for components, Jest for API, Playwright for E2E
- **Error Handling**: Next.js error boundaries and proper API error responses
- Each user story should be independently completable and testable
- Commit after each task or logical group
- Stop at any checkpoint to validate story independently
- Avoid: vague tasks, same file conflicts, cross-story dependencies that break independence

---

## Comprehensive Traceability Matrix
**References**: Complete mapping from plan.md sections to implementation tasks

### Plan Section → Task Group Mapping
| Plan Section | Task Group | Task Numbers | What It Covers |
|--------------|------------|--------------|----------------|
| Section 0 (Architecture) | Phase 1: Setup | T001-T006 | Framework, dependencies, styling, environment |
| Section 5 (Database) | Phase 2: Infrastructure | T007-T014 | Schema, models, migrations, types, validation |
| Section 1 (User Stories) | User Story Phases | T015+ | Feature implementation by user story |
| Section 2 (UI/Components) | UI Tasks | T028+, T033+ | Components, pages, routing, state |
| Section 4 (API/Validation) | API Tasks | T023+, T025+ | Endpoints, validation, business logic |
| Section 3 (Tests) | Test Tasks | T015+, T042+ | Unit, integration, E2E tests |
| Section 6 (NFRs) | Optimization | TXXX+ | Performance, security, production |
| Section 7 (Dependencies) | Execution Order | All tasks | Dependency management |
| Section 8 (Environment) | Configuration | T006, TXXX | Environment setup, deployment |

### User Story → Implementation Tasks Mapping
| User Story | Phase | Database Tasks | API Tasks | UI Tasks | Page Tasks | Test Tasks | Integration |
|------------|-------|----------------|-----------|----------|------------|------------|-------------|
| US1 | Phase 3 | T018-T022 | T023-T027 | T028-T032 | T033-T037 | T015-T017 | T038-T041 |
| US2 | Phase 4 | T045-T047 | T048-T050 | T051-T053 | T054-T056 | T042-T044 | T057-T058 |
| US3 | Phase 5 | T062-T063 | T064 | T065 | T066 | T059-T061 | T067 |

### Requirement → Task Traceability
| Requirement Type | ID | Description | Implementation Tasks | Test Tasks |
|------------------|-----|-------------|---------------------|------------|
| User Story | US1 | [From plan.md] | T018-T041 | T015-T017 |
| Functional Req | FR-US1.1 | [From plan.md] | T028, T023 | T015 |
| Entity | ENT1 | [From plan.md] | T018, T021, T023 | T016 |
| Journey | J1 | [From plan.md] | T033, T028 | T017 |
| Acceptance Criteria | AC-US1.1 | [From plan.md] | [Specific task] | T015 |

### Critical Path Analysis
```
Phase 1 (T001-T006)
  ↓
Phase 2 (T007-T014) [CRITICAL - BLOCKS ALL]
  ↓
User Stories (Can run in parallel after Phase 2):
  ├── US1 (T015-T041)
  ├── US2 (T042-T058)
  └── US3 (T059-T067)
  ↓
Optimization & Production (TXXX)
```

### Coverage Verification Checklist
**Before Implementation Start**:
- [ ] All user stories from plan.md have corresponding phases
- [ ] All functional requirements (FR#) have implementation tasks
- [ ] All entities (ENT#) have database and type tasks
- [ ] All journeys (J#) have page and component tasks
- [ ] All acceptance criteria (AC-US#) have test tasks
- [ ] All NFRs have optimization tasks

**During Implementation**:
- [ ] Each task references its plan section and requirement IDs
- [ ] Test tasks explicitly reference what AC they validate
- [ ] No orphaned tasks without plan references
- [ ] Complete traceability from plan to code to tests

**After Implementation**:
- [ ] All tasks completed with plan references documented
- [ ] All tests pass and validate their AC requirements
- [ ] Complete audit trail from user stories to production code
