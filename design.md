# BuildEstimate Pro - Design Document

## 1. Application Architecture

### Tech Stack
- **Frontend:** React 19 + TypeScript + Vite
- **Database:** Supabase (PostgreSQL)
- **AI:** Google Gemini API
- **Styling:** Tailwind CSS

### Project Structure
```
ai/
├── components/           # Reusable UI components
│   ├── BottomNav.tsx    # Mobile bottom navigation
│   ├── ChatRoom.tsx    # Chat messaging component
│   ├── EngineerProfileViewModal.tsx
│   ├── FieldBotModal.tsx
│   ├── ImagePreviewModal.tsx
│   ├── LocationWidget.tsx
│   ├── MyProjectsModal.tsx
│   ├── Navbar.tsx       # Top navigation bar
│   ├── ProfileModal.tsx
│   └── SuccessTick.tsx
├── contexts/
│   └── LanguageContext.tsx  # Internationalization context
├── services/
│   ├── supabase.ts      # Supabase client and operations
│   └── geminiService.ts # Google Gemini AI integration
├── views/
│   ├── AdminDashboard.tsx
│   ├── Auth.tsx         # Login/Register view
│   ├── CustomerDashboard.tsx
│   ├── EngineerDashboard.tsx
│   └── Home.tsx         # Landing page
├── types.ts             # TypeScript interfaces and types
├── constants.ts         # App constants and initial data
├── App.tsx              # Main app component
├── index.tsx            # Entry point
└── .env.local           # Environment variables
```

---

## 2. UI/UX Design

### Color Palette
| Role | Color | Usage |
|------|-------|-------|
| Primary | Slate (#1e293b) | Navbar, footer, text |
| Accent | Construction Yellow (#f59e0b) | CTAs, highlights, borders |
| Background | Slate 50 (#f8fafc) | Page background |
| Success | Green (#22c55e) | Success states |
| Error | Red (#ef4444) | Error states |

### Typography
- **Font:** System default sans-serif
- **Headings:** Bold, uppercase, tracking-wide
- **Body:** Medium weight, standard sizing

### Layout Structure

#### Desktop
- Fixed top navbar
- Full-width content area
- Footer with branding
- No bottom nav

#### Mobile
- Fixed top navbar
- Full-width content
- Fixed bottom navigation (after login)
- Footer hidden on mobile

---

## 3. Component Specifications

### Navbar
- Logo/App name on left
- Navigation links: Home, Dashboard, Login/Logout
- User avatar dropdown when logged in
- Responsive: hamburger menu on mobile

### BottomNav (Mobile Only)
- 3 icons: Profile, FieldBot, Orders
- Active state highlighting
- Fixed at bottom of screen

### Views

#### Home
- Hero section with app branding
- "Get Started" CTA button
- Feature highlights

#### Auth
- Tabbed interface: Login / Register
- Registration includes role selection (Customer/Engineer)
- Form validation
- Error messaging

#### Customer Dashboard
- Project submission form
- My Projects list with status badges
- AI estimate display cards
- Layout preview gallery

#### Engineer Dashboard
- All projects list
- Filter by status
- Estimate submission form
- Project detail view

#### Admin Dashboard
- User management table
- Project overview
- Approve/reject engineers

### Modals

#### ProfileModal
- Edit user information
- Profile image preview
- Save/Cancel buttons

#### MyProjectsModal
- List of user's projects
- Click to view details
- Status indicators

#### FieldBotModal
- Chat interface
- Message input
- AI assistant branding

#### ImagePreviewModal
- Full-size image view
- Close button
- Backdrop click to close

---

## 4. Data Flow

### Authentication Flow
1. User opens app → Check localStorage for auth state
2. If authenticated → Redirect to dashboard
3. If not → Show Home/Auth views
4. Login/Register → Update state → Store in localStorage

### Project Submission Flow
1. Customer fills project form
2. Submit triggers AI cost estimation
3. AI returns 6 estimate tiers
4. Customer can also trigger layout generation
5. AI returns 3 layout options
6. Customer selects preferred layout
7. Project saved to Supabase with status "Submitted"

### Project Review Flow
1. Engineer views all projects
2. Engineer reviews details and AI estimates
3. Engineer submits their own estimate
4. Project status updates to "Approved"
5. Customer can view engineer's estimate
6. On finalization → Status "Finalized & Building"

---

## 5. API Specifications

### Supabase Operations

#### getUsers()
- Fetches all users from `users` table
- Maps snake_case to camelCase

#### upsertUser(user)
- Inserts or updates user record
- Converts camelCase to snake_case

#### getProjects()
- Fetches all projects ordered by created_at DESC
- Maps database fields to Project interface

#### upsertProject(project)
- Inserts or updates project record
- Handles JSONB fields (details, estimates, messages)

### Gemini AI Operations

#### getAiEstimateOptions(details)
- Input: ConstructionDetails object
- Output: Array of 6 AiEstimateOption objects
- Uses gemini-3-flash-preview model
- Returns JSON with tier breakdown

#### generateHouseLayout(details, style)
- Input: ConstructionDetails, style string
- Output: Base64 PNG image data
- Uses gemini-2.5-flash-image model
- Includes staircase in design

#### generateTripleLayouts(details)
- Calls generateHouseLayout 3 times with different styles
- Returns array of {url, style} objects

---

## 6. State Management

### Local State (useState)
- authState: { user, isAuthenticated }
- users: User[]
- projects: Project[]
- currentView: 'home' | 'auth' | 'dashboard'
- Modal states: isFieldBotOpen, isProfileOpen, isOrdersOpen

### Persistent State (localStorage)
- auth: Serialized AuthState object

### Server State (Supabase)
- Users table
- Projects table

---

## 7. Error Handling

### Supabase Errors
- Console.error with descriptive message
- Fallback to local data if fetch fails
- Silent failures for upsert operations

### Gemini API Errors
- Catch and log errors
- Return empty array/default values
- Display user-friendly error messages

### Form Validation
- Required field checks
- Email format validation
- Mobile number format

---

## 8. Security Considerations

- Password stored in database (consider hashing in production)
- API keys in environment variables
- Role-based access control on UI level
- Supabase row-level security recommended for production

---

## 9. Future Enhancements

- Real-time chat with Supabase subscriptions
- Image upload to Supabase Storage
- Payment integration
- Project timeline tracking
- Document management
- Push notifications
- Multi-language support (LanguageContext exists)
