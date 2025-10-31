# Implementation Plan: [FEATURE]

**Branch**: `[###-feature-name]` | **Date**: [DATE] | **Spec**: [link]
**Input**: Feature specification from `/specs/[###-feature-name]/spec.md`

**Note**: This template is filled in by the `/nextjs.plan` command. 

---

## Section 0: Architecture Foundation


# Architecture Foundation

### Tech Stack
| Layer | Choice | Why |
|-------|--------|-----|
| Framework | Next.js 14 App Router | Modern, Server Components |
| Database | PostgreSQL + Prisma | Complex relations in ENT1-3 |
| Styling | Tailwind CSS | User preference in $ARGUMENTS |
| API | Server Actions + Route Handlers | Forms + public endpoints |
| Deployment | Local | Fast setup, team has no DevOps |

### state manage strategy

| State Type | Layer | Tool | Persistence | Use When |
|------------|-------|------|-------------|----------|
| User session | 1 | NextAuth/Auth0 | HTTP-only cookie | Authentication needed |
| App theme | 2 | Context | LocalStorage | UI preferences across app |
| Current workspace | 2 | Context | SessionStorage | Cross-route coordination |
| User profile data | 3 | React Query | Cache | Server data, multiple components |
| Project list | 3 | React Query | Cache | Server data, needs caching |
| Modal open/close | 4 | useState | None | Local UI interaction |
| Search input | 4 | useState | None | Simple input, no validation |
| Registration form | 5 | React Hook Form | None | Complex validation needed |
| File upload | 5 | React Hook Form | None | Progress tracking, validation |
  

### Rendering Strategy
| Feature | Pattern | Why |
|---------|---------|-----|
| US1: Landing | SSG | Public, SEO |
| US2: Dashboard | SSR | User-specific |
| US3: Editor | CSR | Interactive |

### Out of Scope
- No mobile app
- No real-time collaboration
- No payment processing

---

## Section 1: Feature Implementation Map

### US1: [User Story Title]
**From Spec**: "[Full user story text]"

**Implementation Tasks**:
- **C1**: Build [ComponentName] component
  - Purpose: [What it does]
  - Implements: [J#, FR#]
  - UI Elements: [Input fields, buttons, etc.]
  
- **A1**: Create [METHOD] /api/[endpoint]
  - Purpose: [What it does]
  - Implements: [FR#, ENT#]
  - Request: [Request body structure]
  - Response: [Response structure]
  - Validation: [Zod schema name]
  - Business Logic: [Key operations]

- **Lib-[name]**: [Utility function name]
  - Purpose: [What it does]
  - Implements: [FR#]
  - Input: [Parameters]
  - Output: [Return value]

**Traceability**: US1 → J[#,#] → FR[#,#] → C1, A1

---

### US2: [Next User Story]
[Repeat structure above]

---

## Section 2: UI Components, Routes, State & Navigation

### Component Hierarchy

```
App Layout (app/layout.tsx)
│
├─ Page: /[route-1] (app/[route-1]/page.tsx)
│  ├─ [ComponentA] (components/[ComponentA].tsx)
│  │  ├─ [SubComponentA1]
│  │  └─ [SubComponentA2]
│  └─ [ComponentB] (components/[ComponentB].tsx)
│
└─ Page: /[route-2] (app/[route-2]/page.tsx)
   └─ [ComponentC] (components/[ComponentC].tsx)
```

**Component Details**:

| Component | Type | Purpose | Used In Journey | Props | State |
|-----------|------|---------|-----------------|-------|-------|
| [ComponentA] | Form | [Purpose] | J# | [Props] | [State] |
| [ComponentB] | Display | [Purpose] | J# | [Props] | [State] |
| [ComponentC] | List | [Purpose] | J# | [Props] | [State] |

---

### Route Mapping

#### **Route: `/[route-1]`**
- **Journey**: J[#] → J[#]
- **Entry Point**: [How users arrive]
- **Exit Point**: [How users leave]
- **Rendering**: [CSR/SSR/SSG]
- **Components**: [List of C#]
- **APIs Called**: [List of A#]

#### **Route: `/[route-2]`**
[Repeat structure above]

---

### State Management Strategy

**Analysis from User Journeys**:

**J[#] → J[#] State Transitions**:
- [Action 1] → [State change]
- [Action 2] → [State change]

**State Management Choice**: [useState / Context / Zustand / Redux]

**Reasoning**: [Why this choice based on complexity, sharing needs]

**State Implementation**:
```typescript
// Page/Component: [Name]
const [stateName1, setStateName1] = useState<Type>(initialValue); // J#: [Purpose]
const [stateName2, setStateName2] = useState<Type>(initialValue); // J#: [Purpose]

// Derived State
const derivedValue = computeFrom(stateName1); // J#: [Purpose]
```

---

### Navigation Strategy

**J[#] → J[#]**: [No navigation / State reset / Route change]
- Implementation: [How it works]
- Trigger: [User action]

**Navigation Configuration**:
```typescript
// Navigation between pages
<Link href="/[target]">[Label]</Link>

// Programmatic navigation (if needed)
router.push('/[target]');

// State reset (if needed)
const handleReset = () => { /* reset state */ };
```

---

## Section 3: Test Cases

### Test File Structure

| AC# | Test Type | Test File | Component/API Tested |
|-----|-----------|-----------|----------------------|
| AC-US#.# | Unit | [file.test.tsx] | [Component/Function] |
| AC-US#.# | Integration | [feature.test.tsx] | [Feature flow] |
| AC-US#.# | E2E | [journey.e2e.test.tsx] | [Full user journey] |

---

### Test Specifications

#### **AC-US#.#: [Acceptance Criteria Title]**
```typescript
// File: __tests__/[category]/[file].test.tsx
// Traces to: AC-US#.#, [Related specs]

describe('[Component/Feature] - AC-US#.#', () => {
  it('[test description based on acceptance criteria]', async () => {
    // Given: [Initial state from AC]
    [setup code]

    // When: [User action from AC]
    [action code]

    // Then: [Expected outcome from AC]
    [assertion code]
    
    expect([actual]).toBe([expected]);
  });
});
```

**Edge Cases**:
```typescript
describe('[Component] - Edge Cases', () => {
  it('[boundary condition 1]', () => { /* test */ });
  it('[boundary condition 2]', () => { /* test */ });
});
```

#### **AC-US#.#: [Acceptance Criteria Title]**
[Repeat structure above]


---

## Section 4: API, Validation, Business Logic & Utilities

### API Endpoints

####  **A1: [METHOD] /api/[endpoint]**
```typescript
// File: app/api/[path]/route.ts
// Implements: FR-[#.#], ENT#
// Traces to: US#, J#

Purpose: [What this endpoint does]

Request:
{
  [field]: [type], // [Description, constraints]
}

Response (Success):
{
  [field]: [type], // [Description]
}

Response (Error):
{
  error: string,
  details?: object
}

Validation: [Schema name]
Business Logic:
  - [Key operation 1]
  - [Key operation 2]

Error Handling:
  - 400: [When and why]
  - 404: [When and why]
  - 500: [When and why]
```
####  **A2: [METHOD] /api/[endpoint]**
[Repeat structure above]

---

### Validation Schemas

#### **Client & Server Validation**

```typescript
// File: lib/validation/[entity].ts
// Implements: FR-[#.#]
// Traces to: [Spec requirements]

import { z } from 'zod';

export const [entityName]Schema = z.object({
  [field]: z.[type]()
    .[constraint]('[error message]'), // FR-[#.#]
});

export type [EntityName]Input = z.infer<typeof [entityName]Schema>;

// Usage
export function validate[EntityName](data: unknown): [EntityName]Input {
  return [entityName]Schema.parse(data);
}
```
#### **Client-Side Validation** (in component):
```typescript
// File: components/[ComponentName].tsx
// Real-time validation, immediate feedback

const handleChange = (e) => {
  // Enforce constraints client-side
  // Update error states
  // Enable/disable submit button
};
```
#### **Server-Side Validation** (in API):
```typescript
// File: app/api/[path]/route.ts
// Security layer, never trust client

export async function [METHOD](request: NextRequest) {
  try {
    const data = validate[EntityName](await request.json());
    // Process validated data
  } catch (error) {
    // Return 400 with validation errors
  }
}
```

---

### Business Logic & Utilities

#### **Lib-[UtilityName]: [Purpose]**
```typescript
// File: lib/[utility-name].ts
// Implements: FR-[#.#]
// Traces to: [Requirements]

/**
 * [Function description]
 * @param [param] - [description]
 * @returns [description]
 */
export function [functionName]([params]): [returnType] {
  // Implementation
}
```
#### **Lib-[UtilityName]: [Purpose]**
[Repeat structure above]

####  **Common Utilities**:
- `lib/id-generator.ts`: Generate unique IDs (FR-[#.#])
- `lib/hash.ts`: Content hashing (FR-[#.#])
- `lib/sanitize.ts`: Input sanitization (Security)
- `lib/date-utils.ts`: Date calculations (FR-[#.#])

---

### Type Definitions

```typescript
// File: types/[entity].ts
// API contracts and interfaces

/**
 * [Entity description]
 * Traces to: ENT#
 */
export interface [EntityName] {
  [field]: [type]; // [Description]
}

/**
 * Request/Response types
 */
export interface Create[EntityName]Request {
  [field]: [type];
}

export interface [EntityName]Response {
  [field]: [type];
}
```

---

## Section 5: Database Schema, Types & CRUD

### ENT1: [Entity Name]
**From Spec**: [Entity description and purpose]


#### **Database Schema (Task D1)**
```prisma
// File: prisma/schema.prisma
// Implements: ENT#
// Traces to: [Related requirements]

model [EntityName] {
  // Primary Key
  id          String   @id @default(cuid())
  
  // Attributes from ENT# spec
  [field1]    [Type]   [constraints]
  [field2]    [Type]   [constraints]
  
  // Timestamps
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  
  // Relationships (if any)
  [related]   [RelatedModel][]  @relation("[RelationName]")
  
  // Indexes for performance
  @@index([field])
  @@index([field1, field2])
}
```


#### **TypeScript Types**
```typescript
// File: types/[entity].ts
// Matches Prisma schema

export interface [EntityName] {
  id: string;
  [field1]: [Type];
  [field2]: [Type];
  createdAt: Date;
  updatedAt: Date;
}

// Input type (user-provided fields only)
export interface Create[EntityName]Input {
  [field1]: [Type];
  [field2]: [Type];
}

// Response type (returned to client)
export interface [EntityName]Response {
  id: string;
  [field1]: [Type];
  [field2]: [Type];
  createdAt: string; // ISO 8601
}
```

#### **CRUD API Endpoints**

Based on ENT# lifecycle: [Created, Accessed, Modified, Deleted]

| Operation | Endpoint | Method | Task | Notes |
|-----------|----------|--------|------|-------|
| Create | /api/[entities] | POST | A# | Implements "Created" lifecycle |
| Read One | /api/[entities]/[id] | GET | A# | Implements "Accessed" lifecycle |
| Read Many | /api/[entities] | GET | A# | [If needed for lists] |
| Update | /api/[entities]/[id] | PUT | A# | [Only if "Modified" in lifecycle] |
| Delete | /api/[entities]/[id] | DELETE | A# | [Only if manual deletion needed] |

**Skipped endpoints**:
- [X] No PUT - Business rule: "[Cannot be modified]"
- [X] No DELETE - Business rule: "[Auto-deleted after expiration]"


#### **UI Components**

Based on Journey references:

| Journey | Component Type | Task | Purpose |
|---------|---------------|------|---------|
| J# "creates [entity]" | [Entity]Form | C# | Input form for creating |
| J# "views [entity]" | [Entity]Viewer | C# | Display component |
| J# "edits [entity]" | [Entity]EditForm | C# | Edit form |
| J# "lists [entities]" | [Entity]List | C# | Table/list view |

**Skipped components**:
- [X] No EditForm - Business rule: "[Cannot be modified]"

---

### ENT2: [Next Entity]
[Repeat structure above]

---

## Section 6: Non-Functional Requirements

### NFR-P1: Performance - [Requirement]
**From Spec**: [Full NFR text and target metric]

**Architecture Decision**:
- **Pattern**: [Chosen approach]
- **Reasoning**: [How this achieves the NFR]
- **Trade-offs**: [What we're optimizing vs compromising]

**Implementation**:
```json
// File: vercel.json (or next.config.js)
{
  "[config-key]": "[value]",
  // [Comment explaining why]
}
```

**Validation**:
- **Test**: T-Perf1 - [Test description]
- **Metric**: [What to measure]
- **Target**: [Threshold from spec]

**Monitoring**:
- **Dashboard**: Track [metric name]
- **Alert**: If [condition] for [duration] → [Action]

---

### NFR-S1: Security - [Requirement]
**Architecture Decision**:
- **Pattern**: [Security approach]
- **Implementation**: [Where and how]

```typescript
// File: [relevant file]
// Security implementation
```

**Validation**:
- **Test**: T-Sec1 - [Security test]
- **Coverage**: [What's tested]

---

### NFR-U1: Usability - [Requirement]
**Architecture Decision**:
- **Pattern**: [Accessibility/UX approach]
- **Implementation**: [ARIA attributes, keyboard nav, etc.]

**Validation**:
- **Test**: T-A11y1 - Axe-core audit
- **Target**: [Accessibility score]

---

## Section 7: Task Dependencies & Coverage

### Dependency Graph

```
D1 (Database Schema - ENT#)
├─ A1 (Create API) ────────────┐
│  └─ T1 (API Unit Tests)      │
│                               ├─ P1 (Home Page)
├─ C1 (Form Component) ────────┤  └─ T2 (Integration Tests)
│  └─ T3 (Component Tests)     │     └─ T4 (E2E Tests)
│                               │        └─ I1 (Deploy Database)
├─ C2 (Display Component) ─────┘           └─ I2 (Deploy App)
│  └─ P2 (Detail Page)                         └─ I3 (Monitoring)
│
└─ A2 (Read API) ──────────────────────────────┘
```

**Critical Path**: D1 → A1 → P1 → T2 → T4 → I1 → I2 → I3

**Parallelizable Tasks**:
- C1, C2 (can be built while A1 is in progress)
- T1, T3 (can run independently)


### Traceability Matrix

| Spec Item | Implemented In | Files | Tasks | Status |
|-----------|----------------|-------|-------|--------|
| US1 | [Feature name] | [file list] | [task list] | Pending |
| J# | [Page/Component] | [file list] | [task list] | Pending |
| AC-US#.# | [Test file] | [file list] | [task list] | Pending |
| FR-US#.# | [API/Component] | [file list] | [task list] | Pending |
| ENT# | [Prisma model] | [file list] | [task list] | Pending |
| NFR-* | [Architecture] | [config files] | [task list] | Pending |

### **Coverage Verification**
- [] All User Stories (US1-US#) mapped to features
- [] All Journey Steps (J1-J#) mapped to pages/components
- [] All Acceptance Criteria (AC-*) mapped to tests
- [] All Functional Requirements (FR-*) mapped to implementation
- [] All Entities (ENT1-ENT#) mapped to database schema
- [] All NFRs (NFR-*) addressed in architecture
- [] No orphaned tasks

---

## Section 8: Environment Configuration

### Development Environment
```bash
# .env.development
DATABASE_URL="[local database URL]"
NEXT_PUBLIC_APP_URL="http://localhost:3000"
NEXT_PUBLIC_ENV="development"
# [Other dev-specific vars]
```

### Staging Environment
```bash
# .env.staging
DATABASE_URL="[staging database URL]"
NEXT_PUBLIC_APP_URL="https://staging.[domain]"
NEXT_PUBLIC_ENV="staging"
# [Other staging-specific vars]
```

### Production Environment
```bash
# .env.production
DATABASE_URL="[production database URL]"
NEXT_PUBLIC_APP_URL="https://[domain]"
NEXT_PUBLIC_ENV="production"
# [Other production-specific vars]
```

### Environment Scripts
```json
// package.json scripts
{
  "dev": "next dev",
  "build": "next build",
  "start": "next start",
  "build:staging": "NODE_ENV=staging next build",
  "build:prod": "NODE_ENV=production next build",
  "db:migrate": "npx prisma migrate dev",
  "db:migrate:prod": "npx prisma migrate deploy"
}
```

### Environment Validation
```typescript
// File: lib/env.ts
// Validate environment variables at startup

import { z } from 'zod';

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'staging', 'production']),
  DATABASE_URL: z.string().url(),
  NEXT_PUBLIC_APP_URL: z.string().url(),
  // [Other required vars]
});

export const env = envSchema.parse(process.env);
```

---

## Quick Reference

### Task Naming Conventions
- **D#** = Database tasks (schema, migrations)
- **A#** = API tasks (routes, endpoints)
- **C#** = Component tasks (UI components)
- **P#** = Page tasks (route pages)
- **T#** = Test tasks (unit, integration, E2E)
- **I#** = Infrastructure tasks (deployment, monitoring)
- **Lib** = Utility/library tasks (helpers, utils)
- **Config** = Configuration tasks (env, build config)

### Section → Output Mapping

| Spec Section | Plan Output | What to Generate |
|--------------|-------------|------------------|
| Section 0: Overview | Architecture decisions | Rendering strategy, tech stack |
| Section 1: User Stories | Feature clusters | C#, A#, Lib tasks |
| Section 2: Journey Steps | Pages, components, routes | P#, C#, state, navigation |
| Section 3: Acceptance Criteria | Test cases | T# with Given-When-Then |
| Section 4: Functional Req | API, validation, logic | A#, validation schemas, utilities |
| Section 5: Entities | Database, types, CRUD | D#, TypeScript types, A#, C# |
| Section 6: NFRs | Architecture, infrastructure | Config files, I#, monitoring |
| Section 7: Traceability | Dependencies, coverage | Dependency graph, traceability table |
| Section 8: Environment | Config files | .env templates, scripts |

---