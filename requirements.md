# BuildEstimate Pro - Requirements

## Project Overview
**Project Name:** BuildEstimate Pro  
**Type:** Construction Project Management Web Application  
**Core Functionality:** A platform for customers to submit construction projects, receive AI-powered cost estimates and floor plans, and manage projects through engineers and admins.  
**Target Users:** Homeowners (Customers), Construction Engineers, Site Administrators

---

## User Roles

### 1. Customer
- Register and login
- Submit new construction projects with:
  - Plot dimensions (length × breadth) or total plot area
  - Number of floors
  - Floor-wise room configuration (rooms, bathrooms, kitchen type)
  - Parking requirement
  - Budget range preference
  - Timeline (months)
  - Additional notes
- View AI-generated cost estimates (6 tiers: Economy to Ultra-Luxury)
- View AI-generated floor layout designs (3 styles)
- Select preferred layout
- Track project status (Submitted → Approved → Finalized)
- Chat with engineers
- View and update profile

### 2. Engineer
- Login and view assigned projects
- Review customer project submissions
- Submit cost estimates with material quality selection
- Chat with customers
- View and update profile

### 3. Admin
- Manage all users (approve engineers)
- View all projects
- User management capabilities

---

## Technical Stack

### Frontend
- **Framework:** React 19 with TypeScript
- **Build Tool:** Vite 6
- **Styling:** Tailwind CSS (implied by class names)

### Backend Services
- **Database:** Supabase (PostgreSQL)
  - URL: `https://ukbgayoirtpqfhroowix.supabase.co`
  - Tables: `users`, `projects`
- **AI Services:** Google Gemini API
  - Model: `gemini-3-flash-preview` (cost estimation)
  - Model: `gemini-2.5-flash-image` (layout generation)

---

## Database Schema

### Users Table
| Field | Type | Description |
|-------|------|-------------|
| id | UUID | Primary key |
| name | TEXT | Display name |
| first_name | TEXT | First name |
| last_name | TEXT | Last name |
| email | TEXT | Email address |
| password | TEXT | Password (hashed) |
| role | TEXT | CUSTOMER, ENGINEER, or ADMIN |
| is_approved | BOOLEAN | Approval status for engineers |
| experience | INTEGER | Years of experience |
| projects_done | INTEGER | Completed projects count |
| company_name | TEXT | Company name |
| profile_image | TEXT | Profile image URL |
| phone | TEXT | Phone number |
| address | TEXT | Address |

### Projects Table
| Field | Type | Description |
|-------|------|-------------|
| id | UUID | Primary key |
| customer_id | UUID | Foreign key to users |
| customer_name | TEXT | Customer's name |
| first_name | TEXT | First name |
| last_name | TEXT | Last name |
| email | TEXT | Email |
| mobile_number | TEXT | Mobile number |
| details | JSONB | Construction details (see below) |
| status | TEXT | Submitted, Approved, Finalized |
| estimates | JSONB | Array of engineer estimates |
| messages | JSONB | Chat messages |
| created_at | TIMESTAMP | Creation timestamp |
| selected_layout_url | TEXT | Selected floor plan URL |

### Construction Details (JSONB)
```json
{
  "plotArea": number,
  "floors": number,
  "floorConfigs": [
    {
      "floorNumber": number,
      "rooms": number,
      "bathrooms": number,
      "kitchenType": "With Chimney" | "Without Chimney"
    }
  ],
  "parking": boolean,
  "budgetRange": string,
  "timelineMonths": number,
  "notes": string,
  "length": number,
  "breadth": number
}
```

---

## Core Features

### 1. Authentication
- Email/password-based login
- Role-based access control
- Persistent login state (localStorage)

### 2. Project Submission
- Multi-floor configuration builder
- Plot dimension input (length × breadth or total area)
- Budget and timeline selection
- AI cost estimation trigger
- AI layout generation trigger

### 3. AI Cost Estimation
- Generates 6 cost tiers:
  1. Economy
  2. Budget-Friendly
  3. Standard
  4. Premium
  5. Luxury
  6. Ultra-Luxury
- Each tier includes material cost, labor cost, and explanation
- Costs calculated in INR

### 4. AI Floor Layout Generation
- Generates 2D technical floor plans
- 3 style options: Modernist Edge, Classic Heritage, Eco Minimal
- Includes staircase on every floor
- Shows door swings, windows, structural columns
- Outputs as base64 PNG images

### 5. Chat System
- Real-time messaging between customers and engineers
- Support for text and media (images/files)
- Timestamped messages

### 6. Profile Management
- View and edit profile information
- Profile image upload capability

---

## API Integration

### Google Gemini API
- **Cost Estimation Endpoint:** `models.generateContent`
  - Model: `gemini-3-flash-preview`
  - Response: JSON array of 6 estimate tiers
- **Layout Generation Endpoint:** `models.generateContent`
  - Model: `gemini-2.5-flash-image`
  - Output: Base64 PNG image

### Supabase
- Project CRUD operations
- User CRUD operations
- Real-time subscriptions (if needed)

---

## UI/UX Requirements

### Responsive Design
- Mobile-first approach
- Bottom navigation on mobile after login
- Desktop navigation via navbar

### Visual Theme
- Construction-themed color palette
- Primary: Slate/Blue-gray tones
- Accent: Yellow (construction yellow)
- Professional, industrial aesthetic

### Views/Pages
1. **Home** - Landing page with CTA
2. **Auth** - Login/Register view
3. **Customer Dashboard** - Project submission and tracking
4. **Engineer Dashboard** - Project review and estimation
5. **Admin Dashboard** - User and project management
6. **Modals** - Profile, My Projects, FieldBot chat

---

## Environment Variables
```
GEMINI_API_KEY=your_google_gemini_api_key
```

---

## Success Criteria
- [ ] Users can register and login with role selection
- [ ] Customers can submit construction projects
- [ ] AI generates 6 cost estimate tiers
- [ ] AI generates floor layout images
- [ ] Engineers can review and approve projects
- [ ] Chat system enables communication
- [ ] Admin can manage users
- [ ] Responsive UI works on mobile and desktop
- [ ] Data persists in Supabase database
