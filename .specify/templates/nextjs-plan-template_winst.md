# Next.js Development Plan Template

## Table of Contents

### Core Planning Sections
1. [Section 0: Feature Overview → Architecture Foundation](#section-0-feature-overview--architecture-foundation)
2. [Section 1: User Stories → Feature Implementation Tasks](#section-1-user-stories--feature-implementation-tasks)
3. [Section 2: User Journey → UI Components, Routes, State & Navigation](#section-2-user-journey--ui-components-routes-state--navigation)
4. [Section 3: Acceptance Criteria → Test Cases](#section-3-acceptance-criteria--test-cases)
5. [Section 4: Functional Requirements → API, Validation, Business Logic & Utilities](#section-4-functional-requirements--api-validation-business-logic--utilities)
6. [Section 5: Key Entities → Database, Types, CRUD Operations & UI](#section-5-key-entities--database-types-crud-operations--ui)
7. [Section 6: Non-Functional Requirements → Architecture, Infrastructure, Config & Monitoring](#section-6-non-functional-requirements--architecture-infrastructure-config--monitoring)
8. [Section 7: Traceability Matrix → Task Dependencies & Coverage](#section-7-traceability-matrix--task-dependencies--coverage)

### Enhanced Development Sections
9. [Section 8: Environment Management → Development, Staging & Production](#section-8-environment-management--development-staging--production)

### Reference Sections
10. [Quick Reference: Section → Output Mapping](#quick-reference-section--output-mapping)
11. [Task Naming Convention](#task-naming-convention)
12. [Validation Checklist for Mapping](#validation-checklist-for-mapping)
13. [Common Mapping Patterns](#common-mapping-patterns)

---

## Section 0: Feature Overview → Architecture Foundation

### From Spec:
```markdown
**Summary**: A text-sharing service that allows users to quickly share
text content via generated links without authentication.

**Scope Boundary**:
- In Scope: Text submission, link generation, link sharing, expiration
- Out of Scope: User accounts, rich text, file uploads, real-time collab
```

### To Plan:

**Architecture Pattern Decision**:
```markdown
## 1. Architecture Overview

**Next.js Pattern**: App Router with Server Components
**Reasoning**:
- No authentication needed (from "without authentication")
- Simple CRUD operations (from "text submission, link generation")
- SEO beneficial for shared links (public content)
- Server Components reduce client bundle

**Rendering Strategy**:
- `/` (Home): CSR - Interactive form with real-time validation
- `/s/[id]` (View): SSR - SEO-friendly, fresh content on each request

**Scope Constraints**:
- No user authentication system (explicitly out of scope)
- No rich text editor (plain text only, from out of scope)
- No file upload handling (text-only, from out of scope)
```

**Decision Logic**:
- "without authentication" → No NextAuth.js/Clerk needed
- "quickly share" → Performance is priority (NFR-P will confirm)
- "text content" + "Out: rich text" → Simple textarea, no WYSIWYG editor
- "Out: real-time collab" → No WebSocket/real-time infrastructure

---

## Section 1: User Stories → Feature Implementation Tasks

### From Spec:
```markdown
**US1**: As a content creator, I want to submit text and instantly receive
a shareable link, so that I can distribute content without email complexity

**US2**: As a content creator, I want confirmation that my link was created
successfully, so that I have confidence the content is accessible

**US3**: As a content recipient, I want to view shared text by clicking a
link, so that I can access content without signing up
```

### To Plan:

**Feature Breakdown**:
```markdown
## 2. Feature Implementation Map

**US1 → Feature 1: Text Submission & Link Generation**
Implementation Tasks:
- C1: Build SnippetForm component (text input UI)
- A1: Create POST /api/snippets endpoint (link generation)
- Validation: Character limits (1-10,000 from FR-US1.1)
- Business Logic: Generate unguessable ID (FR-US1.2)

**US2 → Feature 2: Success Confirmation**
Implementation Tasks:
- C2: Build SnippetResult component (display generated URL)
- UI Elements: Copy button, share options, expiration notice
- Interaction: Toast notification on copy action

**US3 → Feature 3: Content Viewing**
Implementation Tasks:
- P2: Build /s/[id] page (view route)
- A2: Create GET /api/snippets/[id] endpoint (retrieve content)
- C3: Build SnippetViewer component (display content)
- Error Handling: 410 Gone for expired links (Alt-J5.1)
```

**Mapping Logic**:
- Each US# becomes a feature cluster
- "submit text" → Form component (C#)
- "receive shareable link" → API endpoint (A#)
- "view shared text" → Page (P#) + API (A#)
- "without signing up" → No auth middleware needed

---

## Section 2: User Journey → UI Components, Routes, State & Navigation

### From Spec:
```markdown
**J1**: User arrives at app and enters content
*User sees*: Large text input field with placeholder, character counter
*User does*: Pastes or types content

**J2**: User triggers link generation
*User sees*: "Generate Link" button (enabled when text present)
*User does*: Clicks button

**J3**: System displays generated link
*User sees*: Unique URL with "Copy" button, "Share" options, expiration notice
*User does*: Clicks "Copy to Clipboard"

**J4**: User receives confirmation
*User sees*: "✓ Link copied! Valid for 7 days" toast message
*User does*: Closes page or creates another link
```

### To Plan:

**Component Hierarchy**:
```markdown
## 4. Component Architecture

App Layout (app/layout.tsx)
│
├─ Page: / (app/page.tsx) - Journey J1→J2→J3→J4
│  ├─ SnippetForm (components/SnippetForm.tsx) - J1, J2
│  │  ├─ Textarea (ui/textarea.tsx) - J1 "text input field"
│  │  ├─ CharacterCounter - J1 "character counter"
│  │  └─ SubmitButton (ui/button.tsx) - J2 "Generate Link button"
│  │
│  └─ SnippetResult (components/SnippetResult.tsx) - J3, J4
│     ├─ LinkDisplay - J3 "Unique URL"
│     ├─ CopyButton - J3 "Copy button"
│     ├─ ShareOptions - J3 "Share options"
│     ├─ ExpirationNotice - J3 "expiration notice"
│     └─ SuccessToast - J4 "toast message"
│
└─ Page: /s/[id] (app/s/[id]/page.tsx) - Journey J5
   └─ SnippetViewer (components/SnippetViewer.tsx)
```

**Route Mapping**:
```markdown
**Journey J1-J4** → **Route: /** (Home Page)
- Entry Point: User lands on homepage
- Exit Point: User has copied link (J4)
- Components Needed: SnippetForm, SnippetResult
- Route Type: CSR (Client-Side Rendered) - Interactive form with real-time validation

**Journey J5** → **Route: /s/[id]** (View Page)
- Entry Point: User clicks shared link
- Exit Point: User finishes reading content
- Components Needed: SnippetViewer
- Route Type: SSR (Server-Side Rendered) - SEO-friendly, fresh content check

**Alternative Journey Alt-J5.1** → **Same Route /s/[id]**
- Condition: expiresAt < now
- Components Needed: ExpiredMessage (conditional render)
- Error Page: 410 Gone status
```

**State Management Strategy**:
```markdown
**State Analysis from Journey**:

**J1→J2 State Transitions**:
- User enters text → Form content state changes
- Character count updates → Derived state from content
- Button enabled/disabled → Derived state from content.length

**J2→J3 State Transitions**:
- User clicks generate → Loading state (true)
- API returns URL → Generated link state
- Loading completes → Loading state (false), Show result

**J3→J4 State Transitions**:
- User clicks copy → Clipboard action
- Copy succeeds → Toast visible state (true)
- Toast dismisses → Toast visible state (false)

**State Management Choice**:
- **Local Component State (useState)** - Reasoning: Simple linear flow, no shared state across pages
- No global state needed (React Context/Zustand/Redux) - Single page flow, no persistent state

**State Implementation**:
```typescript
// Home Page State
const [content, setContent] = useState('');              // J1: Form content
const [isGenerating, setIsGenerating] = useState(false); // J2: Loading state
const [generatedUrl, setGeneratedUrl] = useState('');    // J3: Result
const [showToast, setShowToast] = useState(false);       // J4: Toast visibility

// Derived State
const charCount = content.length;                        // J1: Character counter
const isButtonEnabled = charCount > 0;                   // J2: Button state
```

**Navigation Implementation**:
```markdown
**Navigation Analysis from Journey**:

**J1→J2→J3→J4**: No navigation (single page flow)
- All steps occur on same page (/)
- State transitions only, no route changes
- Implementation: Conditional rendering based on state

**J4→J1 (Create Another)**: Page refresh or state reset
- Option A: Reset state to initial values
- Option B: Refresh page (window.location.reload())
- Implementation: "Create New Link" button resets state

**External→J5**: Direct navigation via shared URL
- User clicks link in email/chat → Navigate to /s/[id]
- Implementation: Next.js dynamic route [id]
- No auth check needed (public access)

**J5→J1**: Navigation link to create own
- "Create Your Own Link" CTA → Navigate to /
- Implementation: <Link href="/">Create Your Own</Link>

**Navigation Configuration**:
```typescript
// No special navigation setup needed for this simple flow
// Next.js Link component handles client-side navigation

// Example: From View Page to Home
<Link href="/" className="...">
  Create Your Own Link
</Link>

// Example: Reset state instead of navigate (J4→J1)
const handleCreateAnother = () => {
  setContent('');
  setGeneratedUrl('');
  setShowToast(false);
};
```

**Layout Strategy**:
```markdown
**Shared Across All Journeys**:
- App Layout (app/layout.tsx)
  - Meta tags, fonts, global styles
  - No persistent navigation (per feature scope)
  - Simple header with logo/title (optional)

**Page-Specific**:
- Home Page (/): Centered layout, max-width container
- View Page (/s/[id]): Reading-optimized layout, wider max-width

**No Complex Navigation**:
- No navbar (not needed for 2-page app)
- No breadcrumbs (simple structure)
- No sidebar (no multiple sections)
```

**Mapping Logic**:
- Each journey step's "User sees" → UI component
- Each journey step's "User does" → Event handler/action
- Journey flow → Page structure and component composition
- State transitions between steps → useState hooks
- Decision points → Conditional rendering or routing
- Entry/Exit points → Route boundaries
- Same context across steps → Same page, different state
- Different context → Different page/route
- Persistent UI across journeys → Layout component
- Navigation between journeys → Next.js Link or route changes

---

## Section 3: Acceptance Criteria → Test Cases

### From Spec:
```markdown
**AC-US1.1**: Given user has text in clipboard, When user pastes into
input field, Then text appears immediately with character count displayed

**AC-US1.2**: Given valid text (1-10,000 chars) is entered, When user
clicks "Generate Link", Then unique URL is displayed within 2 seconds

**AC-US1.3**: Given empty input field, When user hovers over "Generate Link"
button, Then button is disabled and tooltip shows "Please enter text first"
```

### To Plan:

**Test File Structure**:
```markdown
| AC# | Test Type | Test File | Function Under Test |
|-----|-----------|-----------|---------------------|
| AC-US1.1 | Unit | SnippetForm.test.tsx | Paste handling, counter update |
| AC-US1.2 | Integration | snippet-creation.test.tsx | Full form submission flow |
| AC-US1.3 | Unit | SnippetForm.test.tsx | Button disabled state logic |
```

**Test Implementation Template**:
```typescript
// AC-US1.1 → Unit Test
describe('SnippetForm - AC-US1.1', () => {
  it('shows pasted text immediately with character counter', async () => {
    // Given: user has text in clipboard
    const clipboardText = 'Test content from clipboard';

    // When: user pastes into input field
    render(<SnippetForm />);
    const textarea = screen.getByPlaceholderText('Paste your text here');
    await userEvent.paste(textarea, clipboardText);

    // Then: text appears immediately with character count displayed
    expect(textarea).toHaveValue(clipboardText);
    expect(screen.getByText('27/10,000')).toBeInTheDocument();
  });
});

// AC-US1.2 → Integration Test
describe('Link Generation - AC-US1.2', () => {
  it('displays URL within 2 seconds for valid input', async () => {
    // Given: valid text (1-10,000 chars) is entered
    const validText = 'Valid test content';

    const startTime = Date.now();

    // When: user clicks "Generate Link"
    render(<HomePage />);
    await userEvent.type(screen.getByRole('textbox'), validText);
    await userEvent.click(screen.getByText('Generate Link'));

    // Then: unique URL is displayed within 2 seconds
    const url = await screen.findByTestId('generated-url', {}, { timeout: 2000 });
    const duration = Date.now() - startTime;

    expect(url).toBeInTheDocument();
    expect(duration).toBeLessThan(2000);
  });
});
```

**Mapping Logic**:
- **Given** → Test setup, initial state
- **When** → User action or trigger (click, type, paste)
- **Then** → Assertion on expected outcome
- Each AC-US#.# → One or more test cases
- Edge cases (AC-US#.#-Edge) → Boundary tests
- Error cases (AC-US#.#-Error) → Error handling tests

---

## Section 4: Functional Requirements → API, Validation, Business Logic & Utilities

### From Spec:
```markdown
**FR-US1.1**: System MUST accept text input between 1 and 10,000 characters
*Traces to: US1, J1, AC-US1.1, AC-US1.4-Edge*

**FR-US1.2**: System MUST generate a unique, non-guessable identifier for
each text submission
*Traces to: US1, J2, AC-US1.2*

**FR-US1.4**: System MUST display character count in real-time as user types
*Traces to: US1, J1, AC-US1.1*
```

### To Plan:

**API Endpoint Design**:
```markdown
**POST /api/snippets** (implements FR-US1.1, FR-US1.2)
Purpose: Create new text snippet
Request Body:
  {
    content: string (1-10,000 chars) ← FR-US1.1
  }
Response:
  {
    id: string (unguessable) ← FR-US1.2
    url: string
    expiresAt: string (ISO 8601)
  }
Validation:
  - Zod schema enforcing FR-US1.1 (min: 1, max: 10000)
  - Input sanitization (XSS prevention)
Business Logic:
  - Generate ID with 128-bit entropy (FR-US1.2, NFR-S1)
  - Calculate expiration: now + 7 days (ENT1 lifecycle)
  - Compute content hash for duplicate detection
Error Responses:
  - 400: Invalid input (empty or >10,000 chars)
  - 500: Server error with user-friendly message
```

**Validation Schema Design** (from FR-US1.1):
```markdown
**Validation Requirements**:
- FR-US1.1: Accept 1-10,000 characters
- FR-US1.3: Reject empty input
- FR-US1.5: Complete within 2 seconds (affects validation performance)

**Zod Schema Implementation**:
```typescript
// File: lib/validation/snippet.ts
// Traces to: FR-US1.1, FR-US1.3

import { z } from 'zod';

export const createSnippetSchema = z.object({
  content: z.string()
    .min(1, 'Content cannot be empty')           // FR-US1.3
    .max(10000, 'Content exceeds 10,000 chars')  // FR-US1.1
    .trim(),
});

export type CreateSnippetInput = z.infer<typeof createSnippetSchema>;

// Usage in API route
export function validateSnippetInput(data: unknown): CreateSnippetInput {
  return createSnippetSchema.parse(data); // Throws ZodError if invalid
}
```

**Client-Side Validation** (from FR-US1.1, FR-US1.4):
```markdown
**Purpose**: Immediate user feedback, prevent unnecessary API calls

**Implementation Location**: Component level (C1: SnippetForm)

**Validation Points**:
1. **Real-time character count** (FR-US1.4)
   - Update on every keystroke
   - Display: "X/10,000"
   - Visual warning when approaching limit

2. **Input length enforcement** (FR-US1.1)
   - Prevent typing beyond 10,000 chars
   - Truncate paste content if exceeds limit
   - Show error message

3. **Submit button state** (FR-US1.3)
   - Disable when empty
   - Disable when generating (prevent double-submit)
   - Show tooltip on hover when disabled

**Implementation**:
```typescript
// File: components/SnippetForm.tsx
// Traces to: FR-US1.1, FR-US1.3, FR-US1.4

const MAX_CHARS = 10000;

function SnippetForm() {
  const [content, setContent] = useState('');

  // FR-US1.4: Real-time character count
  const charCount = content.length;
  const isAtLimit = charCount >= MAX_CHARS;
  const isEmpty = charCount === 0;

  const handleChange = (e: React.ChangeEvent<HTMLTextAreaElement>) => {
    // FR-US1.1: Enforce max length client-side
    const newValue = e.target.value.slice(0, MAX_CHARS);
    setContent(newValue);
  };

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();

    // FR-US1.3: Validate not empty before submit
    if (isEmpty) {
      return; // Button should be disabled, but double-check
    }

    // Client-side validation using Zod (optional but recommended)
    try {
      validateSnippetInput({ content });
      await createSnippet(content);
    } catch (error) {
      // Show validation error to user
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <textarea
        value={content}
        onChange={handleChange}
        maxLength={MAX_CHARS} // Browser-level enforcement
      />
      <div className={isAtLimit ? 'text-red-500' : 'text-gray-500'}>
        {charCount}/{MAX_CHARS}
      </div>
      <button
        type="submit"
        disabled={isEmpty || isSubmitting}
      >
        Generate Link
      </button>
    </form>
  );
}
```

**Server-Side Validation** (from FR-US1.1, FR-US1.3):
```markdown
**Purpose**: Security, data integrity, prevent malicious input

**Implementation Location**: API route (A1: POST /api/snippets)

**Validation Points**:
1. **Schema validation** (FR-US1.1, FR-US1.3)
   - Use Zod schema (same as client)
   - Reject invalid requests with 400 status
   - Never trust client-side validation

2. **Input sanitization**
   - Strip potentially harmful content
   - Prevent XSS attacks
   - Normalize whitespace

3. **Business rule validation**
   - Check for duplicate content (via hash)
   - Validate against rate limits
   - Check blacklisted patterns (if moderation enabled)

**Implementation**:
```typescript
// File: app/api/snippets/route.ts
// Traces to: FR-US1.1, FR-US1.2, FR-US1.3

import { NextRequest, NextResponse } from 'next/server';
import { validateSnippetInput } from '@/lib/validation/snippet';
import { generateId } from '@/lib/id-generator';
import { db } from '@/lib/db';

export async function POST(request: NextRequest) {
  try {
    const body = await request.json();

    // FR-US1.1, FR-US1.3: Server-side validation (CRITICAL)
    const validatedData = validateSnippetInput(body);

    // Additional sanitization
    const sanitizedContent = sanitizeInput(validatedData.content);

    // FR-US1.2: Generate unique ID
    const id = generateId();

    // Store in database
    const snippet = await db.textSnippet.create({
      data: {
        id,
        content: sanitizedContent,
        contentHash: computeHash(sanitizedContent),
        expiresAt: new Date(Date.now() + 7 * 24 * 60 * 60 * 1000),
      },
    });

    return NextResponse.json({
      id: snippet.id,
      url: `${baseUrl}/s/${snippet.id}`,
      expiresAt: snippet.expiresAt.toISOString(),
    });

  } catch (error) {
    if (error instanceof z.ZodError) {
      // Validation failed - return 400
      return NextResponse.json(
        { error: 'Invalid input', details: error.errors },
        { status: 400 }
      );
    }

    // Other errors - return 500
    console.error('Snippet creation error:', error);
    return NextResponse.json(
      { error: 'Failed to create snippet' },
      { status: 500 }
    );
  }
}
```

**Utility Functions / Business Logic** (from FR-US1.2):
```markdown
**ID Generation Utility** (implements FR-US1.2, NFR-S1):

```typescript
// File: lib/id-generator.ts
// Traces to: FR-US1.2, NFR-S1

import { randomBytes } from 'crypto';

/**
 * Generate a unique, non-guessable identifier
 * FR-US1.2: Unique identifier requirement
 * NFR-S1: Minimum 128-bit entropy requirement
 */
export function generateId(): string {
  // 16 bytes = 128 bits (meets NFR-S1)
  const buffer = randomBytes(16);

  // Convert to base64url (URL-safe, no padding)
  // Results in 22-character string
  return buffer
    .toString('base64')
    .replace(/\+/g, '-')
    .replace(/\//g, '_')
    .replace(/=/g, '');
}

/**
 * Validate ID format
 * Used for route parameter validation
 */
export function isValidId(id: string): boolean {
  // Check length (22 chars for base64url encoding of 16 bytes)
  if (id.length !== 22) return false;

  // Check characters (base64url alphabet: A-Z, a-z, 0-9, -, _)
  return /^[A-Za-z0-9_-]{22}$/.test(id);
}
```

**Content Hash Utility** (for duplicate detection):

```typescript
// File: lib/hash.ts
// Used by: A1 (Create Snippet API), ENT1 business rules

import { createHash } from 'crypto';

/**
 * Compute SHA-256 hash of content
 * Used for duplicate detection (ENT1 business rule)
 */
export function computeHash(content: string): string {
  return createHash('sha256')
    .update(content)
    .digest('hex');
}
```

**Input Sanitization Utility**:

```typescript
// File: lib/sanitize.ts
// Security layer for user input

/**
 * Sanitize user input to prevent XSS
 * Used in all API routes that accept user content
 */
export function sanitizeInput(input: string): string {
  // Trim whitespace
  let sanitized = input.trim();

  // Remove null bytes
  sanitized = sanitized.replace(/\0/g, '');

  // Normalize whitespace (multiple spaces/newlines to single)
  sanitized = sanitized.replace(/\s+/g, ' ');

  // Additional sanitization as needed
  // For plain text, this is minimal
  // For rich text, would use DOMPurify or similar

  return sanitized;
}
```

**Type Definitions** (from FR-US1.1, FR-US1.2):

```typescript
// File: types/snippet.ts
// API contract types derived from functional requirements

/**
 * Create snippet request body
 * Traces to: FR-US1.1 (content constraints)
 */
export interface CreateSnippetRequest {
  content: string; // 1-10,000 characters
}

/**
 * Create snippet response body
 * Traces to: FR-US1.2 (ID generation), ENT1 (expiration)
 */
export interface CreateSnippetResponse {
  id: string;       // Unique, unguessable identifier (FR-US1.2)
  url: string;      // Full shareable URL
  expiresAt: string; // ISO 8601 timestamp
}

/**
 * Validation error response
 */
export interface ValidationErrorResponse {
  error: string;
  details?: Array<{
    path: string[];
    message: string;
  }>;
}
```

**Mapping Logic**:
- "System MUST [capability]" → API endpoint or business logic function
- Validation requirements (min/max, format) → Zod schema + client validation + server validation
- "real-time" → Client-side state management (useState + onChange)
- "unique identifier" → Utility function (lib/id-generator.ts)
- Input constraints → Three-layer validation (browser, client React, server API)
- Complex algorithms → Utility functions in lib/*
- Reusable logic → Extract to lib/* for testability
- API contracts → TypeScript interfaces in types/*
- Each FR-US#.# → One or more implementation artifacts:
  - Validation rules → Zod schemas
  - System capabilities → API endpoints (A#) or utilities (lib/*)
  - Client interactions → Component logic (C#)
  - Data contracts → Type definitions (types/*)

---


## Section 5: Key Entities → Database, Types & CRUD

### Mapping Rule
```
ENT# (from spec) → Generate:
1. Prisma schema (D# task)
2. TypeScript types (types/*.ts)
3. CRUD API endpoints (A# tasks) - based on lifecycle
4. UI components (C# tasks) - based on journey usage
```

### From Spec Template
```markdown
**ENT1: [Entity Name]**
- Purpose: [What it represents]
- Key Attributes: [List of fields]
- Lifecycle: Created, Accessed, Modified, Deleted
- Relationships: [Links to other entities]
- Business Rules: [Constraints]
```

### To Plan Output

**Step 1: Database Schema (D# task)**
```prisma
// File: prisma/schema.prisma
// Map each attribute → database column
// Map relationships → @relation
// Map business rules → constraints/indexes

model [EntityName] {
  // Attributes from ENT# spec
  id          String   @id @default(cuid())
  [field1]    [Type]
  [field2]    [Type]
  
  // Relationships from ENT# spec
  [related]   [RelatedModel][]
  
  // Indexes for performance
  @@index([field])
}
```

**Step 2: TypeScript Types**
```typescript
// File: types/[entity].ts
// Direct 1:1 mapping from ENT# attributes

export interface [EntityName] {
  id: string;
  [field1]: [Type];
  [field2]: [Type];
  // ... all attributes from ENT#
}

// Input/Output types for API
export interface Create[EntityName]Input {
  // Only user-provided fields
}

export interface [EntityName]Response {
  // Fields returned to client
}
```

**Step 3: CRUD APIs (A# tasks)**
```markdown
Map ENT# Lifecycle → API Endpoints:

| Lifecycle Step | API Endpoint | HTTP Method | Task |
|----------------|--------------|-------------|------|
| Created        | POST /api/[entities] | POST | A# |
| Accessed/Read  | GET /api/[entities]/[id] | GET | A# |
| Modified       | PUT /api/[entities]/[id] | PUT | A# |
| Deleted        | DELETE /api/[entities]/[id] | DELETE | A# |

**Skip endpoints if**:
- "Cannot be modified" in business rules → No PUT
- "Auto-deleted" in lifecycle → No DELETE endpoint (use cron)
- Not in user journey → Defer to future
```

**Step 4: UI Components (C# tasks)**
```markdown
Map ENT# to Components based on Journey usage:

| Journey Reference | Component Type | Task | Purpose |
|-------------------|----------------|------|---------|
| "User creates [entity]" | Form (Create) | C# | Input form for ENT# attributes |
| "User views [entity]" | Display | C# | Read-only display of ENT# |
| "User edits [entity]" | Form (Edit) | C# | Editable form |
| "User lists [entities]" | List/Table | P# | Collection view |

**Only create components for entities used in journey J#**
```

### Complete Example: ENT1 → Implementation

**From Spec**:
```markdown
ENT1: Text Snippet
- Attributes: id, content, createdAt, expiresAt, viewCount
- Lifecycle: Created (J2), Accessed (J5), Deleted (auto)
- Business Rules: Cannot be modified, Auto-deletes after expiration
```

**To Plan**:
```markdown
**D1: Database Schema**
```prisma
model TextSnippet {
  id         String   @id @default(cuid())
  content    String   @db.Text
  createdAt  DateTime @default(now())
  expiresAt  DateTime
  viewCount  Int      @default(0)
  @@index([expiresAt])
}
```

**types/snippet.ts**
```typescript
export interface TextSnippet {
  id: string;
  content: string;
  createdAt: Date;
  expiresAt: Date;
  viewCount: number;
}
```

**A1: POST /api/snippets** (Created at J2)
**A2: GET /api/snippets/[id]** (Accessed at J5)
**No PUT** (business rule: cannot be modified)
**No DELETE** (business rule: auto-deleted via cron)

**C1: SnippetForm** (Create UI for J2)
**C3: SnippetViewer** (Display UI for J5)
**No Edit Form** (cannot be modified)
```

### Decision Logic for LLM

```python
def map_entity_to_implementation(entity):
    # Always generate
    tasks.append(f"D{n}: Prisma schema for {entity.name}")
    tasks.append(f"types/{entity.name.lower()}.ts")
    
    # Lifecycle → APIs
    if "Created" in entity.lifecycle:
        tasks.append(f"A{n}: POST /api/{entity.plural}")
    
    if "Accessed" in entity.lifecycle or "Read" in entity.lifecycle:
        tasks.append(f"A{n}: GET /api/{entity.plural}/[id]")
    
    if "Modified" in entity.lifecycle and "cannot be modified" not in entity.business_rules:
        tasks.append(f"A{n}: PUT /api/{entity.plural}/[id]")
    
    if "Deleted" in entity.lifecycle and "auto-delete" not in entity.business_rules:
        tasks.append(f"A{n}: DELETE /api/{entity.plural}/[id]")
    
    # Journey → UI Components
    for journey_step in entity.journey_references:
        if "creates" in journey_step or "submits" in journey_step:
            tasks.append(f"C{n}: {entity.name}Form (create)")
        
        if "views" in journey_step or "sees" in journey_step:
            tasks.append(f"C{n}: {entity.name}Viewer (display)")
        
        if "edits" in journey_step:
            tasks.append(f"C{n}: {entity.name}EditForm")
    
    return tasks
```

---

## Section 6: Non-Functional Requirements → Architecture & Infrastructure

### Mapping Rule
```
NFR-[Type]# (from spec) → Generate:
1. Architecture decision (how to achieve it)
2. Configuration file (next.config.js, vercel.json)
3. Infrastructure task (I#)
4. Validation test (T-[Type]#)
5. Monitoring setup (I#-Monitor)
6. Alert rule (I#-Alert)
```

### NFR Types & Outputs

| NFR Type | Architecture Output | Config Output | Test Output | Monitoring |
|----------|---------------------|---------------|-------------|------------|
| **NFR-P# (Performance)** | Edge deployment, caching, optimization | next.config.js (optimization), vercel.json (regions) | T-Perf# (load test) | Track p50/p95/p99 latency |
| **NFR-S# (Security)** | Crypto lib, security headers, input sanitization | next.config.js (CSP headers) | T-Sec# (penetration test) | Track security events |
| **NFR-U# (Usability)** | ARIA attributes, keyboard nav, responsive | tailwind.config.js (accessibility) | T-A11y# (axe-core audit) | Track accessibility score |
| **NFR-R# (Reliability)** | Error handling, retries, fallbacks | Error boundaries, monitoring | T-Reliability# (chaos test) | Track error rate, uptime |
| **NFR-SC# (Scalability)** | Connection pooling, horizontal scaling | Database config, load balancer | T-Scale# (stress test) | Track resource usage |

### From Spec Template
```markdown
**NFR-[Type]#**: System MUST [requirement] [target metric]
*Rationale: [Why this matters]*
*Measurement: [How to verify]*
```

### To Plan Output (Structured)

**For Each NFR-[Type]#:**

```markdown
## [NFR-Type#]: [Requirement Statement]

**Architecture Decision**:
- Pattern: [Chosen approach]
- Reasoning: [Why this achieves the NFR]
- Trade-offs: [What we're optimizing for]

**Implementation**:
- Config: [File path] - [What's configured]
- Code: [File path] - [Implementation detail]
- Infrastructure: I# task - [What needs deploying]

**Validation**:
- Test: T-[Type]# - [How to verify the NFR is met]
- Metric: [What to measure]
- Target: [Threshold value from spec]

**Monitoring**:
- Dashboard: [Metric to track]
- Alert: [Condition] → [Action]
```

### Complete Example: NFR-P1

**From Spec**:
```markdown
NFR-P1: Link generation MUST complete within 2 seconds for 95% of requests
Rationale: Users expect instant feedback
Measurement: p95 latency < 2000ms
```

**To Plan**:
```markdown
## NFR-P1: Performance - Link Generation

**Architecture Decision**:
- Pattern: Edge Functions + Connection Pooling
- Reasoning: Edge reduces network latency, pooling reduces DB overhead
- Trade-offs: Optimizing for response time over cost

**Implementation**:
- Config: vercel.json
  ```json
  {
    "regions": ["all"],
    "functions": {
      "app/api/snippets/route.ts": { "memory": 1024 }
    }
  }
  ```
- Config: next.config.js
  ```javascript
  {
    reactStrictMode: true,
    swcMinify: true
  }
  ```
- Infrastructure: I2 - Deploy to Vercel Edge Network

**Validation**:
- Test: T-Perf1 - Load test with 100 concurrent requests
- Metric: p95 response time
- Target: < 2000ms

**Monitoring**:
- Dashboard: Track p50/p95/p99 latency via Vercel Analytics
- Alert: p95 > 2000ms for 5 minutes → Notify ops team
```

### Decision Logic for LLM

```python
def map_nfr_to_implementation(nfr):
    outputs = {
        'architecture': None,
        'config_files': [],
        'infrastructure_tasks': [],
        'tests': [],
        'monitoring': []
    }
    
    # Extract NFR type from ID (e.g., "NFR-P1" → "P" for Performance)
    nfr_type = nfr.id.split('-')[1][0]
    
    # Type-specific mappings
    if nfr_type == 'P':  # Performance
        outputs['architecture'] = 'Edge deployment + caching'
        outputs['config_files'] = ['vercel.json', 'next.config.js']
        outputs['tests'] = [f'T-Perf{n}: Load test for p95 < {nfr.target}']
        outputs['monitoring'] = ['Track p95 latency', 'Alert if > threshold']
    
    elif nfr_type == 'S':  # Security
        outputs['architecture'] = 'Crypto library + security headers'
        outputs['config_files'] = ['next.config.js (CSP headers)']
        outputs['tests'] = [f'T-Sec{n}: Penetration test']
        outputs['monitoring'] = ['Track security events', 'Alert on violations']
    
    elif nfr_type == 'U':  # Usability/Accessibility
        outputs['architecture'] = 'ARIA attributes + keyboard navigation'
        outputs['config_files'] = ['components (aria-* attributes)']
        outputs['tests'] = [f'T-A11y{n}: Axe-core audit']
        outputs['monitoring'] = ['Track accessibility score']
    
    # Always add infrastructure task
    outputs['infrastructure_tasks'].append(f'I{n}: Setup for {nfr.id}')
    
    return outputs
```

### Simplified NFR Template

For LLMs to follow, provide this condensed structure:

```markdown
### NFR-[Type]#: [One-line requirement]

**How to Achieve**: [Architecture pattern]
**Config**: [File] → [Setting]
**Test**: T-[Type]# → [Test type with target]
**Monitor**: [Metric] → Alert if [condition]

---

**Example**:

### NFR-P1: Response time < 2s (p95)

**How to Achieve**: Edge deployment
**Config**: vercel.json → `"regions": ["all"]`
**Test**: T-Perf1 → Load test validates p95 < 2000ms
**Monitor**: p95 latency → Alert if > 2000ms for 5min
```

---


## Section 7: Traceability Matrix → Task Dependencies & Coverage

### From Spec:
```markdown
| User Story | Journey Steps | Acceptance Criteria | Functional Requirements | Entities |
|------------|---------------|---------------------|-------------------------|----------|
| US1 | J1, J2 | AC-US1.1, AC-US1.2, AC-US1.3 | FR-US1.1, FR-US1.2, FR-US1.4 | ENT1 |
| US2 | J3, J4 | AC-US2.1, AC-US2.2 | FR-US2.1, FR-US2.2 | ENT1 |
| US3 | J5 | AC-US3.1 | FR-US3.1 | ENT1, ENT2 |
```

### To Plan:

**Task Dependency Graph**:
```markdown


**From Traceability**:
US1 → J1, J2 → FR-US1.1, FR-US1.2, FR-US1.4 → ENT1

**Implementation Dependencies**:
ENT1 → D1 (Database Schema) ← MUST BE FIRST
FR-US1.2 → A1 (Create API) depends on D1
FR-US1.4 → C1 (Form Component) can be parallel to A1
J1, J2 → P1 (Home Page) depends on C1 + A1
AC-US1.1, AC-US1.2 → T1 (Unit Tests) depends on A1, C1

**Dependency Chain**:
```
D1 (Database Schema)
├─ A1 (Create Snippet API) ──→ T1 (API Unit Tests)
│  └─ P1 (Home Page) ──────→ T2 (Integration Tests)
│     └─ T3 (E2E Tests)
│        └─ I1 (Deploy DB)
│           └─ I2 (Deploy App)
│              └─ I3 (Monitoring)
│
└─ C1 (Form Component) ──────┘
   ├─ C2 (Result Component) ──┘
   └─ C3 (Viewer Component) → P2 (View Page) ──┘
```

**Critical Path**: D1 → A1 → P1 → T2 → T3 → I1 → I2 → I3
**Parallelizable**: C1, C2, C3 can be built while A1 is in progress
```

**Coverage Verification**:
```markdown

### Traceability Reference

**Spec to Implementation Mapping**:

| Spec Item | Implemented In | Files | Tasks | Status |
|-----------|----------------|-------|-------|--------|
| US1 | Create snippet flow | pages/index.tsx, api/snippets.ts | C1, A1, P1 | Pending |
| J1 | Form UI | components/SnippetForm.tsx | C1 | Pending |
| J2 | Submit action | api/snippets/route.ts | A1 | Pending |
| AC-US1.1 | Paste test | __tests__/unit/SnippetForm.test.tsx | T1 | Pending |
| AC-US1.2 | Generation test | __tests__/integration/creation.test.tsx | T2 | Pending |
| FR-US1.1 | Input validation | lib/validation.ts, api/snippets/route.ts | A1 | Pending |
| FR-US1.2 | ID generation | lib/id-generator.ts | A1 | Pending |
| ENT1 | TextSnippet model | prisma/schema.prisma | D1 | Pending |
| NFR-P1 | Performance test | __tests__/performance/api.test.ts | T1 | Pending |

**Coverage Check**:
- ✅ All User Stories (US1-US3) mapped to features
- ✅ All Journey Steps (J1-J5) mapped to pages/components
- ✅ All Acceptance Criteria (AC-US#.#) mapped to tests
- ✅ All Functional Requirements (FR-US#.#) mapped to implementation
- ✅ All Entities (ENT1-ENT2) mapped to database schema
- ✅ All NFRs (NFR-*) addressed in architecture/infrastructure
- ✅ No orphaned tasks (all trace back to spec)
```

**Mapping Logic**:
- Traceability matrix → Task dependency graph
- Forward trace (US→J→AC→FR→ENT) → Implementation order
- Backward trace (ENT→FR→AC→J→US) → Coverage verification
- Missing links → Identify gaps and create tasks
- Parallel paths → Identify tasks that can run concurrently

---

## Section 8: Environment Management → Development, Staging & Production

### Environment Configuration

**Development Environment (.env.development)**:
```bash
# Database
DATABASE_URL="postgresql://user:password@localhost:5432/dev_db"

# Next.js Configuration
NEXT_PUBLIC_APP_URL="http://localhost:3000"
NEXT_PUBLIC_ENV="development"

# Feature Flags
ENABLE_ANALYTICS="false"
ENABLE_MONITORING="false"

# External Services (optional)
REDIS_URL="redis://localhost:6379"
SENTRY_DSN=""
```

**Staging Environment (.env.staging)**:
```bash
# Database
DATABASE_URL="postgresql://user:password@staging-db:5432/staging_db"

# Next.js Configuration
NEXT_PUBLIC_APP_URL="https://staging.your-app.com"
NEXT_PUBLIC_ENV="staging"

# Feature Flags
ENABLE_ANALYTICS="true"
ENABLE_MONITORING="true"

# External Services
REDIS_URL="redis://staging-redis:6379"
SENTRY_DSN="https://your-staging-dsn"

# Testing Config
TEST_MODE="true"
```

**Production Environment (.env.production)**:
```bash
# Database
DATABASE_URL="postgresql://user:password@prod-db:5432/prod_db"

# Next.js Configuration
NEXT_PUBLIC_APP_URL="https://your-app.com"
NEXT_PUBLIC_ENV="production"

# Feature Flags
ENABLE_ANALYTICS="true"
ENABLE_MONITORING="true"

# External Services
REDIS_URL="redis://prod-redis:6379"
SENTRY_DSN="https://your-prod-dsn"

# Security
API_SECRET_KEY="your-api-secret"
JWT_SECRET="your-jwt-secret"
```

**Environment-Specific Scripts**:
```json
// package.json
{
  "scripts": {
    "dev": "next dev --port 3000",
    "dev:db": "npx prisma studio",
    "build": "next build",
    "start": "next start",
    "build:staging": "NODE_ENV=staging next build",
    "start:staging": "NODE_ENV=staging next start",
    "build:prod": "NODE_ENV=production next build",
    "start:prod": "NODE_ENV=production next start",
    "db:migrate": "npx prisma migrate dev",
    "db:migrate:staging": "NODE_ENV=staging npx prisma migrate deploy",
    "db:migrate:prod": "NODE_ENV=production npx prisma migrate deploy",
    "seed:dev": "NODE_ENV=development tsx scripts/seed-dev.ts",
    "seed:staging": "NODE_ENV=staging tsx scripts/seed-staging.ts"
  }
}
```

**Environment Validation**:
```typescript
// lib/env.ts
import { z } from 'zod';

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'staging', 'production']),
  DATABASE_URL: z.string().url(),
  NEXT_PUBLIC_APP_URL: z.string().url(),
  NEXT_PUBLIC_ENV: z.string(),
  REDIS_URL: z.string().url().optional(),
  SENTRY_DSN: z.string().url().optional(),
});

export const env = envSchema.parse(process.env);

// Environment-specific configurations
export const isDevelopment = env.NODE_ENV === 'development';
export const isStaging = env.NODE_ENV === 'staging';
export const isProduction = env.NODE_ENV === 'production';

export const config = {
  app: {
    url: env.NEXT_PUBLIC_APP_URL,
    environment: env.NEXT_PUBLIC_ENV,
  },
  database: {
    url: env.DATABASE_URL,
  },
  features: {
    analytics: !isDevelopment,
    monitoring: !isDevelopment,
  },
  external: {
    redis: env.REDIS_URL,
    sentry: env.SENTRY_DSN,
  },
};
```
---

## Quick Reference: Section → Output Mapping

| Spec Section | Plan Output | Task Prefix | Example |
|--------------|-------------|-------------|---------|
| Section 0: Overview | Architecture decisions | - | Rendering strategy, scope constraints |
| Section 1: User Stories | Feature clusters | - | "US1 → Feature 1: Text Submission" |
| Section 2: Journey Steps | Pages, components, routes | P, C | J1→C1 (Form), J5→P2 (View page) |
| Section 3: Acceptance Criteria | Test cases | T | AC-US1.1→Unit test in T1 |
| Section 4: Functional Req | API endpoints, business logic | A, Lib | FR-US1.2→A1 (API) + lib/id-generator |
| Section 5: Entities | Database schema, models | D | ENT1→D1 (Prisma schema) |
| Section 6: NFRs | Architecture patterns, infra | I, Config | NFR-P1→Edge deployment (I2) |
| Section 7: Traceability | Task dependencies, coverage | - | Dependency graph, coverage table |

---

## Task Naming Convention

```
D#    = Database tasks (schema, migrations, seeds)
A#    = API tasks (routes, endpoints, business logic)
C#    = Component tasks (UI components, client logic)
P#    = Page tasks (route pages, layouts)
T#    = Test tasks (unit, integration, E2E)
I#    = Infrastructure tasks (deployment, monitoring)
M-R#.# = Risk Mitigation tasks (R# from spec)
V-A#  = Assumption Validation tasks (A# from spec)
TD-D# = Dependency Tracking tasks (D# from spec)
Lib   = Utility/library tasks (helpers, utils)
Config = Configuration tasks (env, build, deploy config)
```

---

## Validation Checklist for Mapping

Before considering the plan complete, verify:

**Completeness**:
- [ ] Every US# has implementation tasks (A, C, P)
- [ ] Every J# maps to a page (P) or component (C)
- [ ] Every AC-US#.# has a test case (T)
- [ ] Every FR-US#.# has implementation (A, C, or Lib)
- [ ] Every ENT# has database schema (D)
- [ ] Every NFR-* has architecture decision or infrastructure task (I)
- [ ] Every Risk has mitigation task (M-R)
- [ ] Every Assumption has validation task (V-A)
- [ ] Every Dependency has tracking task (TD-D)

**Traceability**:
- [ ] All tasks trace back to spec items
- [ ] No orphaned tasks (tasks without spec reference)
- [ ] Dependencies reflect spec traceability matrix
- [ ] Critical path identified from dependencies

**Testability**:
- [ ] All acceptance criteria have test implementations
- [ ] All NFR-P (performance) have benchmark tests
- [ ] All user journeys have E2E tests
- [ ] Test coverage matches spec coverage

**Implementability**:
- [ ] Code templates provided for common patterns
- [ ] Technology choices justified by spec requirements
- [ ] Environment configuration documented
- [ ] Deployment steps are concrete and actionable

**Consistency**:
- [ ] Numbering aligns with spec (US#, J#, AC-US#.#, FR-US#.#)
- [ ] Terminology matches spec (entity names, concepts)
- [ ] File structure supports spec organization
- [ ] Traceability comments in code reference spec IDs

---

## Common Mapping Patterns

### Pattern 1: CRUD Entity → Full Stack Implementation

**Spec**:
- ENT#: Entity definition
- FR-US#.#: CRUD operations
- AC-US#.#: Operation validation

**Plan**:
- D#: Prisma schema
- A#-Create: POST /api/resource
- A#-Read: GET /api/resource/[id]
- A#-Update: PUT /api/resource/[id] (if in scope)
- A#-Delete: DELETE /api/resource/[id] (if in scope)
- C#-Form: Create/Edit form component
- C#-Display: View component
- P#-List: List page (if applicable)
- P#-Detail: Detail page
- T#: Test each CRUD operation

### Pattern 2: User Journey → Page Flow

**Spec**:
- J#: Journey steps with user actions

**Plan**:
- P#: Page for each journey entry point
- C#: Component for each major journey step
- Routing: Next.js routes matching journey flow
- State: Context or state management for journey state
- Navigation: Links/redirects between journey steps

### Pattern 3: Validation Requirement → Multi-Layer Validation

**Spec**:
- FR-US#.#: Validation rule (e.g., "1-10,000 chars")
- AC-US#.#-Edge: Boundary test cases

**Plan**:
- Lib: Validation utility (e.g., lib/validation.ts)
- Zod Schema: Type-safe validation
- C#: Client-side validation (immediate feedback)
- A#: Server-side validation (security)
- T#-Unit: Test validation logic
- T#-Edge: Test boundary cases

### Pattern 4: NFR → Architecture + Monitoring

**Spec**:
- NFR-[Type]#: Non-functional requirement with target

**Plan**:
- Architecture Decision: How to achieve target
- I#: Infrastructure setup to support NFR
- Config: Configuration for optimization
- T#-Perf: Performance test validating target
- I#-Monitor: Monitoring to track metric in production
- Alert: Alert configuration for violations

### Pattern 5: Risk → Defense in Depth

**Spec**:
- Risk R#: Identified risk with impact

**Plan**:
- M-R#.1: Primary mitigation (prevention)
- M-R#.2: Secondary mitigation (detection)
- M-R#.3: Tertiary mitigation (recovery)
- Test: Validate mitigation effectiveness
- Monitor: Track risk indicators
- Runbook: Response procedure if risk occurs

---
