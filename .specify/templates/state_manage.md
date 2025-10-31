# Next.js Full-Stack State Management Strategy

## The 5-Layer State Architecture

A comprehensive guide for managing state in modern Next.js applications with clear separation of concerns and optimal performance.

---

## Layer 1: Authentication State (NextAuth.js / Auth0 / Clerk)
**Responsibility**: User identity and session management

```typescript
// NextAuth.js example
const { data: session, status } = useSession()

// Typical user session structure:
{
  user: {
    id: string
    email: string
    name: string
    image?: string
    role?: string
  }
  expires: string // ISO timestamp
}
```

**Usage**:
- User login/logout status
- Protected route guards
- API authentication headers
- User profile display
- Role-based access control

**Persistence**: JWT token in HTTP-only cookie or localStorage

**When to use**:
✅ User authentication and authorization
✅ Session management across the app
✅ Protected routes and API calls
❌ NOT for business logic state
❌ NOT for UI preferences (use Layer 2)

---

## Layer 2: Global Application State (React Context + useReducer)
**Responsibility**: Cross-component shared state that needs coordination

```typescript
// Generic AppContext pattern
const { currentProject, theme, sidebarOpen, setCurrentProject } = useAppContext()

// Common global state structure:
{
  currentProject: Project | null    // Active project/workspace
  currentView: string | null        // Current app section/mode
  theme: 'light' | 'dark'          // UI theme preference
  sidebarOpen: boolean             // Layout preferences
  notifications: Notification[]    // Global notifications
  isLoading: boolean               // Global loading state
}
```

**Usage**:
- Active project/workspace context
- App-wide UI preferences (theme, layout)
- Global notifications/alerts
- Cross-route navigation state
- Feature flags and app configuration

**Persistence**: 
- SessionStorage (session-only preferences)
- LocalStorage (persistent preferences)

**When to use**:
✅ State needed by 3+ components across routes
✅ State that coordinates multiple features
✅ UI preferences that should persist
✅ Global app configuration
❌ NOT for server data (use Layer 3)
❌ NOT for component-local UI (use Layer 4)

---

## Layer 3: Server State (TanStack Query / SWR)
**Responsibility**: Remote data fetching, caching, and synchronization

```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'

// Data fetching examples
const { data: projects, isLoading, error } = useQuery({
  queryKey: ['projects', userId],
  queryFn: () => fetchUserProjects(userId),
  staleTime: 5 * 60 * 1000,  // Consider stale after 5 min
  cacheTime: 10 * 60 * 1000, // Keep in cache for 10 min
  refetchOnWindowFocus: true
})

// Conditional fetching
const { data: projectDetails } = useQuery({
  queryKey: ['project', projectId],
  queryFn: () => fetchProjectDetails(projectId),
  enabled: !!projectId // Only fetch when projectId exists
})

// Mutations with optimistic updates
const queryClient = useQueryClient()
const updateProject = useMutation({
  mutationFn: updateProjectData,
  onMutate: async (newData) => {
    await queryClient.cancelQueries(['project', projectId])
    const previous = queryClient.getQueryData(['project', projectId])
    queryClient.setQueryData(['project', projectId], newData)
    return { previous }
  },
  onError: (err, variables, context) => {
    queryClient.setQueryData(['project', projectId], context.previous)
  },
  onSettled: () => {
    queryClient.invalidateQueries(['project', projectId])
  }
})
```

**Common Use Cases**:
- User data and profiles
- Project/workspace data
- Dashboard lists and collections
- Content management (posts, articles, etc.)
- Real-time data that needs synchronization
- File uploads and media management

**Persistence**: In-memory cache + automatic background refetch

**Benefits**:
✅ Automatic caching and deduplication
✅ Background refetching and synchronization
✅ Optimistic updates for better UX
✅ Built-in loading and error states
✅ Request deduplication
✅ Offline support capabilities

**When to use**:
✅ Any data from APIs or databases
✅ Data that multiple components need
✅ Data that can become stale
✅ Lists, collections, and paginated data
❌ NOT for local UI state
❌ NOT for form inputs (use Layer 5)

---

## Layer 4: Component State (useState / useReducer)
**Responsibility**: Local component UI and transient state

```typescript
// Simple UI interactions
const [isModalOpen, setIsModalOpen] = useState(false)
const [selectedTab, setSelectedTab] = useState('overview')
const [showDropdown, setShowDropdown] = useState(false)
const [searchQuery, setSearchQuery] = useState('')

// Complex local state with useReducer
const [state, dispatch] = useReducer(componentReducer, initialState)

// Transient form inputs (simple forms)
const [email, setEmail] = useState('')
const [message, setMessage] = useState('')

// Component-specific loading states
const [isSubmitting, setIsSubmitting] = useState(false)
const [uploadProgress, setUploadProgress] = useState(0)
```

**Common Use Cases**:
- Modal and dialog open/close states
- Dropdown and menu toggles
- Tab and accordion selections
- Search and filter inputs
- Component-specific loading states
- Temporary UI feedback (success/error messages)
- Local form inputs (simple forms)
- Animation and transition states

**Persistence**: None (intentionally ephemeral)

**When to use**:
✅ State used by only 1-2 closely related components
✅ Temporary UI interactions and feedback
✅ Simple form inputs without complex validation
✅ Component-specific loading/error states
✅ Animation and transition states
❌ NOT for data that needs to survive unmount
❌ NOT for data shared across routes
❌ NOT for complex forms (use Layer 5)

---

## Layer 5: Form State (React Hook Form / Formik)
**Responsibility**: Complex form validation, submission, and state management

```typescript
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'

// Schema-based validation
const UserSchema = z.object({
  email: z.string().email('Invalid email'),
  password: z.string().min(8, 'Password must be at least 8 characters'),
  confirmPassword: z.string()
}).refine(data => data.password === data.confirmPassword, {
  message: "Passwords don't match",
  path: ["confirmPassword"]
})

// Form with validation
const { 
  register, 
  handleSubmit, 
  formState: { errors, isSubmitting, isDirty },
  watch,
  setValue,
  reset
} = useForm({
  resolver: zodResolver(UserSchema),
  mode: 'onBlur',
  defaultValues: {
    email: '',
    password: '',
    confirmPassword: ''
  }
})

// Advanced form patterns
const watchedEmail = watch('email')
const onSubmit = async (data) => {
  try {
    await submitForm(data)
    reset() // Clear form on success
  } catch (error) {
    // Handle submission errors
  }
}
```

**Common Use Cases**:
- User registration and login forms
- Profile editing and settings forms
- Content creation forms (posts, comments, etc.)
- Multi-step wizards and surveys
- Forms with complex validation rules
- Forms with dependent fields
- File upload forms with progress tracking

**Features**:
✅ Schema-based validation (Zod, Yup)
✅ Real-time validation feedback
✅ Dirty state tracking
✅ Form submission handling
✅ Field dependencies and conditional logic
✅ File upload handling
✅ Multi-step form support

**Persistence**: Form state in library's internal state

**When to use**:
✅ Forms with validation requirements
✅ Multi-field forms with dependencies
✅ Forms that need submission handling
✅ Forms with file uploads
✅ Multi-step forms or wizards
❌ NOT for simple single-input forms (use Layer 4)
❌ NOT for search inputs (use Layer 4)

---

## State Decision Matrix

Choose the right layer for your state based on these criteria:

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

---

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────┐
│                    Next.js App                          │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌──────────────────────────────────────────────────┐  │
│  │  Layer 1: Authentication (NextAuth/Auth0/Clerk)  │  │
│  │  • User identity & session                       │  │
│  │  • JWT tokens & cookies                          │  │
│  │  • Role-based access control                     │  │
│  └──────────────────────────────────────────────────┘  │
│                          ↓                              │
│  ┌──────────────────────────────────────────────────┐  │
│  │  Layer 2: Global App State (Context)             │  │
│  │  • Current workspace/project                     │  │
│  │  • UI preferences (theme, layout)                │  │
│  │  • Global notifications                          │  │
│  │  • Cross-route coordination                      │  │
│  └──────────────────────────────────────────────────┘  │
│                          ↓                              │
│  ┌──────────────────────────────────────────────────┐  │
│  │  Layer 3: Server State (React Query/SWR)         │  │
│  │  • API data caching                              │  │
│  │  • Background synchronization                    │  │
│  │  • Optimistic updates                            │  │
│  │  • Request deduplication                         │  │
│  └──────────────────────────────────────────────────┘  │
│                          ↓                              │
│  ┌──────────────────────────────────────────────────┐  │
│  │  Layer 4: Component State (useState/useReducer)  │  │
│  │  • Local UI interactions                         │  │
│  │  • Temporary states                              │  │
│  │  • Simple form inputs                            │  │
│  │  • Animation states                              │  │
│  └──────────────────────────────────────────────────┘  │
│                          ↓                              │
│  ┌──────────────────────────────────────────────────┐  │
│  │  Layer 5: Form State (React Hook Form/Formik)    │  │
│  │  • Complex form validation                       │  │
│  │  • Multi-step forms                              │  │
│  │  • File uploads                                  │  │
│  │  • Schema validation                             │  │
│  └──────────────────────────────────────────────────┘  │
│                                                         │
└─────────────────────────────────────────────────────────┘
                          ↕
            ┌─────────────────────────┐
            │   Server (Next.js API)   │
            │   • Server Actions       │
            │   • Route Handlers       │
            │   • Database             │
            │   • External APIs        │
            └─────────────────────────┘
```

## Decision Tree

Use this flowchart to choose the right state management approach:

```
Is it user authentication/session data?
├─ YES → Layer 1 (NextAuth/Auth0/Clerk)
└─ NO ↓

Is it data from an API/database?
├─ YES → Layer 3 (React Query/SWR)
└─ NO ↓

Is it needed by 3+ components across routes?
├─ YES → Layer 2 (Context)
└─ NO ↓

Is it a complex form with validation?
├─ YES → Layer 5 (React Hook Form)
└─ NO → Layer 4 (useState)
```

---

## Common Patterns & Examples

### Pattern 1: Dashboard with Filters

```typescript
// Layer 2: Global filter state (shared across components)
const { filters, setFilters } = useAppContext()

// Layer 3: Server data with filters
const { data: projects, isLoading } = useQuery({
  queryKey: ['projects', filters],
  queryFn: () => fetchProjects(filters),
  keepPreviousData: true // Smooth transitions
})

// Layer 4: Local search input
const [searchInput, setSearchInput] = useState('')

// Debounced search to avoid excessive API calls
useEffect(() => {
  const timer = setTimeout(() => {
    setFilters(prev => ({ ...prev, search: searchInput }))
  }, 300)
  return () => clearTimeout(timer)
}, [searchInput])
```

### Pattern 2: Real-time Data with Optimistic Updates

```typescript
// Layer 3: Real-time data
const { data: messages } = useQuery({
  queryKey: ['messages', chatId],
  queryFn: () => fetchMessages(chatId),
  refetchInterval: 5000 // Poll every 5 seconds
})

// Optimistic message sending
const sendMessage = useMutation({
  mutationFn: sendMessageToServer,
  onMutate: async (newMessage) => {
    await queryClient.cancelQueries(['messages', chatId])
    const previous = queryClient.getQueryData(['messages', chatId])
    
    queryClient.setQueryData(['messages', chatId], old => [
      ...old,
      { ...newMessage, id: 'temp-' + Date.now(), status: 'sending' }
    ])
    
    return { previous }
  },
  onSuccess: (data) => {
    queryClient.setQueryData(['messages', chatId], old =>
      old.map(msg => 
        msg.id.startsWith('temp-') ? data : msg
      )
    )
  },
  onError: (err, variables, context) => {
    queryClient.setQueryData(['messages', chatId], context.previous)
  }
})
```

### Pattern 3: Multi-step Form with Persistence

```typescript
// Layer 2: Form progress (survives page refresh)
const { formStep, setFormStep } = useAppContext()

// Layer 5: Form validation
const { register, handleSubmit, watch, trigger } = useForm({
  resolver: zodResolver(StepSchema[formStep]),
  mode: 'onChange'
})

// Persist form data to sessionStorage
const formData = watch()
useEffect(() => {
  sessionStorage.setItem('formData', JSON.stringify(formData))
}, [formData])

// Load persisted data on mount
useEffect(() => {
  const saved = sessionStorage.getItem('formData')
  if (saved) {
    const data = JSON.parse(saved)
    Object.keys(data).forEach(key => {
      setValue(key, data[key])
    })
  }
}, [])
```

---
