

## **SECTION 1: UPDATED TECHNOLOGY STACK (3.5.4)**

Replace the existing Technology Stack section with this:

---

### **3.5.4 Technology Stack Used in the Prototype**

The EVOTAR system is built using a comprehensive modern technology stack that ensures security, scalability, and optimal user experience:

#### **Frontend Technologies:**

**Core Framework & Language:**
- **React 18.3.1** - Component-based JavaScript library for building interactive user interfaces with virtual DOM for optimal performance
- **TypeScript 5.5.3** - Strongly-typed superset of JavaScript providing static type checking, enhanced IDE support, and improved code maintainability
- **Vite 5.4.1** - Next-generation frontend build tool offering near-instant server startup, lightning-fast Hot Module Replacement (HMR), and optimized production builds

**Routing & Navigation:**
- **React Router DOM 6.26.2** - Client-side routing library enabling seamless navigation between pages without full page reloads

**UI Framework & Styling:**
- **TailwindCSS 3.4.11** - Utility-first CSS framework for rapid, responsive design development
- **shadcn/ui** - Pre-built, customizable component library built on TailwindCSS for consistent design system
- **Radix UI** - Unstyled, accessible component primitives ensuring WCAG compliance and keyboard navigation support
- **Lucide React 0.462.0** - Open-source icon library providing 1000+ scalable SVG icons

**State Management & Data Fetching:**
- **TanStack Query (React Query) 5.56.2** - Powerful data synchronization library for server-state management, automatic caching, background refetching, and optimistic updates
- **React Hook Form 7.53.0** - Performant form library with built-in validation and minimal re-renders
- **Zod 3.23.8** - TypeScript-first schema validation library for runtime data validation and type inference

**Document Generation & QR Codes:**
- **jsPDF 3.0.1** - Client-side PDF generation library used for creating downloadable voting receipts
- **QRCode 1.5.4** - JavaScript library for generating QR codes embedded in voting receipts for verification

**Data Visualization:**
- **Recharts 3.1.2** - Composable charting library built on React components for visualizing voting analytics and statistics

**Security & Authentication:**
- **OTPAuth 9.4.0** - Implementation of Time-based One-Time Password (TOTP) algorithm for two-factor authentication (RFC 6238 compliant)

**Theming:**
- **next-themes 0.4.6** - Lightweight library for managing dark mode and light mode themes with system preference detection

#### **Backend Technologies:**

**Backend-as-a-Service (BaaS):**
- **Supabase** - Open-source Firebase alternative providing comprehensive backend services:
  - **PostgreSQL Database** - Powerful relational database with JSON support, full-text search, and geospatial queries
  - **Supabase Auth** - JWT-based authentication with email verification, password reset, and social login support
  - **Row Level Security (RLS)** - Database-level security policies ensuring users can only access authorized data
  - **Supabase Storage** - Scalable file storage with three private buckets:
    - `student-ids` - Encrypted student ID images for verification
    - `candidate-profiles` - Candidate profile photos
    - `election-covers` - Election cover images
  - **Real-time Subscriptions** - WebSocket-based live data synchronization for instant updates
  - **Edge Functions** - Serverless TypeScript functions for server-side logic execution

#### **Deployment & Infrastructure:**

**Hosting & CDN:**
- **Vercel** - Cloud platform for serverless deployment with:
  - Global Edge Network for low-latency content delivery
  - Automatic HTTPS/SSL certificate management
  - Instant rollbacks and preview deployments
  - Git integration for continuous deployment

**DNS & Security:**
- **Cloudflare** - Content delivery network and security platform providing:
  - DNS management with 1.1.1.1 resolver
  - DDoS protection and Web Application Firewall (WAF)
  - SSL/TLS encryption
  - Global CDN with 300+ data centers

#### **Additional Libraries:**

- **class-variance-authority 0.7.1** - Utility for managing component variants
- **clsx 2.1.1** - Utility for constructing className strings conditionally
- **date-fns 3.6.0** - Modern JavaScript date utility library
- **embla-carousel-react 8.3.0** - Lightweight carousel library
- **sonner 1.5.0** - Opinionated toast notification component
- **tailwind-merge 2.5.2** - Utility for merging Tailwind CSS classes

---

## **SECTION 2: COMPLETE DATABASE SCHEMA**

Add this new section after the Technology Stack:

---

### **3.5.5 Database Architecture and Schema**

The EVOTAR system utilizes a PostgreSQL database with 17 interconnected tables, ensuring data integrity, security, and efficient querying. All tables implement Row-Level Security (RLS) policies to enforce role-based access control at the database level.

#### **1. User Authentication & Profile Tables**

**Table: `profiles`**
- **Purpose:** Stores comprehensive user profile information and authentication settings
- **Columns (17):**
  - `id` (UUID) - Primary key
  - `user_id` (UUID, UNIQUE) - Foreign key to Supabase Auth users
  - `student_id` (TEXT, UNIQUE) - College-issued student ID number
  - `full_name` (TEXT) - User's complete name
  - `email` (TEXT) - Email address for correspondence
  - `course` (TEXT) - Academic program (e.g., BSIT, BSEd)
  - `year_level` (TEXT) - Current year (1st, 2nd, 3rd, 4th)
  - `gender` (TEXT) - Gender identity
  - `role` (ENUM: user_role) - User role (Voter, Staff, Administrator)
  - `registration_status` (TEXT) - Status (Pending, Approved, Rejected)
  - `id_image_url` (TEXT) - Path to encrypted student ID image in storage
  - `two_factor_enabled` (BOOLEAN) - Whether 2FA is active
  - `two_factor_secret` (TEXT, ENCRYPTED) - TOTP secret key
  - `two_factor_recovery_codes` (TEXT[], ENCRYPTED) - 10 one-time recovery codes
  - `created_at` (TIMESTAMP) - Account creation timestamp
  - `updated_at` (TIMESTAMP) - Last profile update timestamp

- **RLS Policies:**
  - Users can view and insert their own profile
  - Staff can view all voter profiles
  - Administrators can view and update all profiles

**Table: `user_roles`**
- **Purpose:** Separate table for role management to prevent privilege escalation attacks
- **Columns (3):**
  - `id` (UUID) - Primary key
  - `user_id` (UUID) - Foreign key to profiles
  - `role` (ENUM: user_role) - Assigned role
- **RLS Policies:**
  - Users can view their own roles
  - Only Administrators can insert, update, or delete roles
- **Security Note:** Separating roles into a dedicated table prevents direct manipulation through profile updates

#### **2. Election System Tables**

**Table: `elections`**
- **Purpose:** Stores election configurations and metadata
- **Columns (11):**
  - `id` (UUID) - Primary key
  - `title` (TEXT) - Election name
  - `description` (TEXT) - Detailed election information
  - `election_type` (TEXT) - Type (Student Council, Department, Organization, Special)
  - `cover_image_url` (TEXT) - Path to election banner image
  - `eligible_voters` (TEXT) - Eligibility criteria (e.g., "All Courses", "BSIT Only")
  - `start_date` (TIMESTAMP) - Voting start date and time
  - `end_date` (TIMESTAMP) - Voting end date and time
  - `status` (TEXT) - Current status (Upcoming, Active, Completed)
  - `show_results_to_voters` (BOOLEAN) - Whether results are publicly visible
  - `created_at`, `updated_at` (TIMESTAMP) - Audit timestamps
- **RLS Policies:**
  - All authenticated users can view elections
  - Staff and Administrators can create, update, and delete elections
- **Automatic Status Updates:** Database function `auto_update_election_statuses()` transitions elections between statuses based on current time

**Table: `positions`**
- **Purpose:** Defines positions available in each election
- **Columns (6):**
  - `id` (UUID) - Primary key
  - `election_id` (UUID) - Foreign key to elections
  - `title` (TEXT) - Position name (e.g., President, Vice President)
  - `description` (TEXT) - Position responsibilities
  - `max_candidates` (INTEGER) - Maximum candidates allowed (default: 10)
  - `created_at` (TIMESTAMP)
- **RLS Policies:** Same as elections table

**Table: `candidates`**
- **Purpose:** Stores candidate information and profiles
- **Columns (13):**
  - `id` (UUID) - Primary key
  - `election_id` (UUID) - Foreign key to elections
  - `position_id` (UUID) - Foreign key to positions
  - `full_name` (TEXT) - Candidate's full name
  - `bio` (TEXT) - Biography and platform
  - `partylist` (TEXT) - Party affiliation (if applicable)
  - `image_url` (TEXT) - Path to candidate profile photo
  - `jhs_school` (TEXT) - Junior high school attended
  - `jhs_graduation_year` (INTEGER) - JHS graduation year
  - `shs_school` (TEXT) - Senior high school attended
  - `shs_graduation_year` (INTEGER) - SHS graduation year
  - `why_vote_me` (TEXT) - Campaign statement
  - `created_at` (TIMESTAMP)
- **RLS Policies:** Same as elections table

#### **3. Voting & Anonymization Tables**

**Table: `voting_sessions`**
- **Purpose:** Creates anonymous voting layer separating voter identity from votes
- **Columns (7):**
  - `id` (UUID) - Primary key
  - `voter_id` (UUID) - Foreign key to profiles (the only link to voter identity)
  - `election_id` (UUID) - Foreign key to elections
  - `session_token` (TEXT, UNIQUE) - Cryptographically secure random token
  - `has_voted` (BOOLEAN) - Whether user completed voting
  - `expires_at` (TIMESTAMP) - Session expiration (24 hours from creation)
  - `created_at` (TIMESTAMP)
- **RLS Policies:**
  - Users can create, view, and update their own sessions
  - Staff and Administrators can view all sessions for monitoring
- **Anonymization Process:** Once `has_voted = true`, the session token becomes the only identifier, breaking the link between voter and votes

**Table: `votes`**
- **Purpose:** Stores actual vote records without direct voter identification
- **Columns (6):**
  - `id` (UUID) - Primary key
  - `voter_id` (UUID) - Links to voting_sessions (not directly to user)
  - `election_id` (UUID) - Foreign key to elections
  - `position_id` (UUID) - Foreign key to positions
  - `candidate_id` (UUID) - Foreign key to candidates
  - `created_at` (TIMESTAMP)
- **RLS Policies:**
  - Users can insert and view ONLY their own votes
  - **No UPDATE or DELETE allowed** - votes are immutable once cast
- **Anonymization:** Votes are cast via RPC function `cast_multiple_anonymous_votes()` which uses session tokens instead of direct user IDs

**Table: `vote_receipts`**
- **Purpose:** Generates cryptographic proof of vote submission for user verification
- **Columns (8):**
  - `receipt_id` (TEXT, UNIQUE) - Generated ID format: `{election_id}-{random_string}`
  - `election_id` (UUID) - Foreign key to elections
  - `election_title` (TEXT) - Election name (denormalized for receipt)
  - `selected_candidates` (JSONB) - Array of {position, candidate} pairs
  - `voting_date` (TIMESTAMP) - When vote was cast
  - `verification_token` (TEXT, UNIQUE) - Secure random string for QR code
  - `receipt_hash` (TEXT) - SHA-256 hash of entire receipt content
  - `created_at` (TIMESTAMP)
- **RLS Policies:**
  - System can insert receipts (via RPC function)
  - **No direct SELECT access** - users retrieve receipts via RPC with verification token
- **Security:** Receipt hash prevents tampering; verification token proves authenticity

#### **4. Blockchain & Verification Tables**

**Table: `receipt_blockchain_record`**
- **Purpose:** Stores blockchain transaction hashes for immutable vote verification
- **Columns (4):**
  - `id` (BIGINT) - Primary key
  - `receipt_id` (TEXT) - Foreign key to vote_receipts
  - `tx_hash` (TEXT) - Blockchain transaction hash
  - `created_at` (TIMESTAMP)
- **RLS Policies:**
  - All users can view blockchain records
  - System can insert records
- **Current Status:** Infrastructure ready; full blockchain integration (smart contracts, IPFS) is a future enhancement

**Table: `election_blockchain_proofs`**
- **Purpose:** Stores blockchain proofs for entire election results
- **Columns (6):**
  - `id` (UUID) - Primary key
  - `election_id` (UUID) - Foreign key to elections
  - `results_hash` (TEXT) - Hash of complete election results
  - `tx_hash` (TEXT) - Blockchain transaction hash
  - `ipfs_cid` (TEXT) - InterPlanetary File System content identifier
  - `created_at` (TIMESTAMP)
- **RLS Policies:** No policies yet (future implementation)
- **Current Status:** Table structure prepared for future blockchain integration

#### **5. Administration & Workflow Tables**

**Table: `pending_actions`**
- **Purpose:** Stores staff requests requiring administrator approval
- **Columns (10):**
  - `id` (UUID) - Primary key
  - `action_type` (TEXT) - Type of action (e.g., "create_election", "delete_user")
  - `action_data` (JSONB) - Full request data including election details, reason, etc.
  - `requested_by` (UUID) - Foreign key to profiles (staff member)
  - `requested_at` (TIMESTAMP) - When request was submitted
  - `status` (TEXT) - Current status (Pending, Approved, Rejected)
  - `reviewed_by` (UUID) - Foreign key to profiles (administrator)
  - `reviewed_at` (TIMESTAMP) - When action was reviewed
  - `admin_notes` (TEXT) - Administrator's comments
  - `created_at`, `updated_at` (TIMESTAMP)
- **RLS Policies:**
  - Staff can create and view pending actions
  - Only Administrators can update or delete
- **Use Case:** Staff members must request administrator approval before creating elections

**Table: `profile_update_requests`**
- **Purpose:** Manages user requests to update their profile information
- **Columns (11):**
  - `id` (UUID) - Primary key
  - `user_id` (UUID) - Foreign key to profiles
  - `current_email` (TEXT) - User's existing email
  - `requested_email` (TEXT) - New email requested
  - `current_year_level` (TEXT) - Current year level
  - `requested_year_level` (TEXT) - New year level requested
  - `status` (TEXT) - Request status (Pending, Approved, Rejected)
  - `requested_at` (TIMESTAMP) - Request submission time
  - `reviewed_by` (UUID) - Foreign key to profiles (staff/admin)
  - `reviewed_at` (TIMESTAMP) - Review completion time
  - `admin_notes` (TEXT) - Reviewer's comments
  - `created_at`, `updated_at` (TIMESTAMP)
- **RLS Policies:**
  - Users can create and view their own requests
  - Staff and Administrators can review and approve/reject
- **Workflow:** Changes are applied automatically upon approval

**Table: `notifications`**
- **Purpose:** System-wide notification delivery to users and role groups
- **Columns (12):**
  - `id` (UUID) - Primary key
  - `type` (TEXT) - Notification category (info, success, warning, error)
  - `title` (TEXT) - Notification headline
  - `message` (TEXT) - Notification content
  - `recipient_user_id` (UUID) - Specific user recipient (if applicable)
  - `target_roles` (user_role[]) - Array of roles to notify (for broadcasts)
  - `priority` (TEXT) - Urgency level (low, medium, high)
  - `link_url` (TEXT) - Optional action link
  - `read_at` (TIMESTAMP) - When user marked as read
  - `data` (JSONB) - Additional structured data
  - `created_by` (UUID) - Foreign key to profiles (sender)
  - `created_at` (TIMESTAMP)
- **RLS Policies:**
  - Users view notifications sent to them or their role
  - Staff and Administrators can create notifications
  - Only Administrators can delete notifications
- **Types:** User-specific (approval, rejection) and role-based broadcasts (election announcements)

**Table: `audit_logs`**
- **Purpose:** Comprehensive, immutable logging of all system actions for security and compliance
- **Columns (10):**
  - `id` (UUID) - Primary key
  - `actor_id` (UUID) - User who performed the action
  - `actor_role` (ENUM: user_role) - Role at time of action
  - `action` (TEXT) - Action performed (login, vote_cast, user_approved, etc.)
  - `resource_type` (TEXT) - Type of resource affected (user, election, vote)
  - `resource_id` (UUID) - Specific resource identifier
  - `details` (JSONB) - Full action context (before/after values, parameters)
  - `ip_address` (INET) - Client IP address
  - `user_agent` (TEXT) - Browser/device information
  - `timestamp` (TIMESTAMP) - Action timestamp
  - `notified` (BOOLEAN) - Whether action triggered notifications
- **RLS Policies:**
  - Voters can view only their own logs
  - Staff can view voter and staff logs
  - Administrators can view all logs
  - **No UPDATE or DELETE allowed** - audit trail is immutable
- **Retention:** All actions logged indefinitely for compliance and forensic analysis

#### **6. Advanced Security Tables**

**Table: `step_up_verifications`**
- **Purpose:** Enforces re-authentication for sensitive operations
- **Columns (7):**
  - `id` (UUID) - Primary key
  - `user_id` (UUID) - Foreign key to profiles
  - `session_token` (TEXT, UNIQUE) - Verification session identifier
  - `action_type` (TEXT) - Action requiring verification (vote, enable_2fa, change_password)
  - `verified_at` (TIMESTAMP) - When verification completed
  - `expires_at` (TIMESTAMP) - Expiration time (15 minutes from verification)
  - `created_at` (TIMESTAMP)
- **RLS Policies:**
  - Users can create and view their own verifications
  - **No UPDATE or DELETE** - verifications are one-time use
- **Security Benefit:** Prevents session hijacking by requiring recent authentication proof for critical actions

#### **7. Views and Aggregated Data**

**View: `election_results`**
- **Purpose:** Real-time aggregated vote counts for display
- **Columns:**
  - `election_id`, `election_title`
  - `position_id`, `position_title`
  - `candidate_id`, `candidate_name`
  - `vote_count` (BIGINT) - Total votes received
- **Source:** Aggregates data from `votes`, `elections`, `positions`, and `candidates` tables
- **Usage:** Displayed on Results page when `show_results_to_voters = true`

**Table: `_vote_receipts_storage`**
- **Purpose:** Internal duplicate storage for vote receipts (backup)
- **Columns:** Same as `vote_receipts` table
- **RLS Policies:** No direct access (internal use only)

#### **Database Functions (RPC)**

The system includes 10+ PostgreSQL functions for complex operations:

1. **`create_voting_session_safe(p_election_id UUID)`**
   - Checks voter eligibility
   - Creates anonymous voting session
   - Returns session token

2. **`cast_multiple_anonymous_votes(p_session_token TEXT, p_votes JSONB[])`**
   - Validates session token
   - Inserts votes anonymously
   - Marks session as completed
   - Generates vote receipt

3. **`can_user_vote_in_election(p_user_id UUID, p_election_id UUID)`**
   - Checks if user is eligible
   - Verifies user hasn't voted yet
   - Returns boolean result

4. **`approve_user_registration(p_user_id UUID, p_admin_id UUID)`**
   - Updates registration_status to "Approved"
   - Sends approval notification
   - Logs action in audit_logs

5. **`toggle_election_results_visibility(p_election_id UUID, p_visible BOOLEAN)`**
   - Controls public result display
   - Administrator-only function

6. **`auto_update_election_statuses()`**
   - Scheduled function (runs every minute)
   - Transitions elections: Upcoming → Active → Completed based on timestamps

7. **`get_election_results(p_election_id UUID)`**
   - Returns formatted results with vote counts and percentages

8. **`get_voting_analytics_by_demographics(p_election_id UUID)`**
   - Provides course and gender breakdown

9. **`approve_profile_update_request(p_request_id UUID, p_reviewer_id UUID)`**
   - Applies approved profile changes
   - Sends confirmation notification

10. **`submit_user_appeal(p_student_id TEXT, p_email TEXT, p_appeal_message TEXT)`**
    - Allows rejected users to appeal decision

---

## **SECTION 3: UPDATED USER WORKFLOWS**

Replace existing workflow sections with these:

---

### **3.7.1 Complete Voter Registration Workflow**

The voter registration process ensures identity verification and administrator approval before granting access to the voting system.

**Step 1: Multi-Step Account Creation**

1. Navigate to `www.evotar.xyz` and click "Sign Up"
2. Complete 4-step registration form:

   **Personal Information (Step 1/4):**
   - Full Name (as appears on student ID)
   - Email Address (institutional or personal)
   - Student ID Number (unique identifier)

   **Academic Details (Step 2/4):**
   - Course (dropdown selection: BSIT, BSEd, BSBA, etc.)
   - Year Level (1st Year, 2nd Year, 3rd Year, 4th Year)
   - Gender (Male, Female, Other, Prefer not to say)

   **Account Security (Step 3/4):**
   - Password (minimum 8 characters, must include uppercase, lowercase, number)
   - Confirm Password

   **Verification (Step 4/4):**
   - Upload Student ID Photo
   - File requirements: JPEG/PNG, max 5MB, clearly visible text
   - System automatically uploads to secure `student-ids` storage bucket

3. Click "Create Account"

**Step 2: Email Confirmation ⚠️ CRITICAL**

1. System displays toast message: "Your account has been created successfully. Please confirm your email address to complete registration and wait for administrator approval."
2. Navigate to your email inbox
3. Look for email from **"Evotar Support"** via Supabase
   - Subject: "Confirm Your Signup"
   - **Important:** Check spam/junk folder if not in inbox within 5 minutes
4. Click the blue "Confirm your mail" button in the email
   - Confirmation link expires in **24 hours**
5. You will be redirected to a confirmation success page
6. Your account status changes from "Unconfirmed" to "Pending"

**Troubleshooting Email Confirmation:**
- If email not received after 10 minutes, contact system administrator
- Administrator can resend confirmation email from User Management panel
- Ensure your email provider allows emails from `@supabase.co` domain

**Step 3: Administrator Approval**

1. Your profile enters "Pending" status visible to administrators
2. Administrator reviews:
   - Uploaded student ID image for authenticity
   - Profile information accuracy
   - Eligibility criteria
3. Administrator decision:
   - **Approved:** You receive email notification; can now log in
   - **Rejected:** You receive notification with reason; can submit appeal
4. Typical review time: 24-48 hours during business days

**Step 4: Appeal Process (If Rejected)**

1. If registration is rejected, you'll see a notification with rejection reason
2. Click "Submit Appeal" on login page
3. Fill out appeal form:
   - Student ID (for identification)
   - Email address
   - Appeal message explaining circumstances
4. Submit appeal → Creates notification for administrators
5. Administrator can resubmit your profile for approval
6. You'll receive email notification of appeal decision

**Step 5: First Login**

1. After approval, return to `www.evotar.xyz/login`
2. Enter your Student ID and Password
3. System may prompt for Two-Factor Authentication setup (optional but recommended)
4. You'll be directed to your personalized Voter Dashboard

---

### **3.7.2 Voter Dashboard and Voting Process**

**Dashboard Overview:**

Upon logging in, voters see a mobile-responsive dashboard with four main tabs:

**Elections Tab:**
- View all active and upcoming elections
- Search and filter by title, eligible voters, or description
- Each election card displays:
  - Election title and cover image
  - Eligible voter criteria (e.g., "All Courses", "BSIT Only")
  - Start and end dates/times
  - Current status badge (Upcoming, Active, Completed)
  - "View Candidates" button (always available)
  - "Cast Vote" button (only during active period and if eligible)
  - "Already Voted" badge (if user has voted)

**Candidates Tab:**
- View all candidates across all elections
- Filter by election and position
- Each candidate card shows:
  - Profile photo
  - Full name and position
  - Partylist affiliation
  - Educational background (JHS/SHS schools and graduation years)
  - Biography
  - Campaign statement ("Why Vote Me")
- Click candidate card for expanded profile view

**Results Tab:**
- View published election results
- **Only visible if administrator enables result visibility**
- For each election:
  - Overall turnout statistics (e.g., "58 of 59 eligible voters voted (98.3% turnout)")
  - Results organized by position
  - Winner highlighted with badge
  - Vote counts and percentages for each candidate
  - "View Analytics" button for demographic breakdown
- Can verify results on blockchain (if blockchain integration active)

**Settings Tab:**
- **Profile Information:**
  - View current student ID, name, email, course, year level, gender
  - Cannot directly edit - must request profile updates
  - "Request Profile Update" button opens request form

- **Security Settings:**
  - Enable/Disable Two-Factor Authentication (2FA)
  - Generate recovery codes (10 codes, download as text file)
  - Change password (requires step-up verification)

- **Activity History:**
  - View your audit logs (login history, votes cast, profile changes)
  - Shows IP address, device, timestamp for each action

**Anonymous Voting Process:**

1. **Select Election:**
   - Navigate to Elections tab
   - Click "Cast Vote" on an active election
   - System checks eligibility based on `eligible_voters` field

2. **Step-Up Verification:**
   - If 2FA is enabled, system checks for recent verification (< 15 minutes)
   - If verification expired, prompt for TOTP code or recovery code
   - Enter 6-digit code from authenticator app
   - System creates `step_up_verification` record with 15-minute expiration

3. **Create Voting Session:**
   - System calls RPC function: `create_voting_session_safe(election_id)`
   - Validates:
     - Election is currently active
     - User is eligible to vote (matches `eligible_voters` criteria)
     - User has not already voted in this election
   - Creates record in `voting_sessions` table with:
     - Unique session_token
     - Link to voter_id (only connection to user identity)
     - 24-hour expiration time

4. **View Digital Ballot:**
   - System displays all positions for the election
   - For each position:
     - Shows all candidates with photos, names, partylist
     - User can click candidate card to view full profile
     - Single-select for most positions (President, Vice President, etc.)
     - Multi-select for representative positions (up to max specified in position settings)
   - Users can select one candidate per position
   - "No selection" option available if user wants to abstain for a position

5. **Review Selections:**
   - System shows confirmation screen with all selections
   - Lists: Position → Candidate Name → Partylist
   - "Change Selection" button returns to ballot
   - "Confirm & Submit" button proceeds to final submission

6. **Cast Anonymous Votes:**
   - User clicks "Confirm & Submit"
   - System calls RPC function: `cast_multiple_anonymous_votes(session_token, votes[])`
   - Function performs atomic transaction:
     - Inserts one record per position into `votes` table
     - Uses session_token instead of direct user_id (anonymization)
     - Marks `voting_sessions.has_voted = true`
     - Generates cryptographic receipt with:
       - Unique receipt_id
       - SHA-256 receipt_hash
       - Secure verification_token
       - Selected candidates list (JSONB)
   - If any step fails, entire transaction rolls back (prevents partial votes)

7. **Receipt Generation & Download:**
   - System calls `generateVotingReceipt()` from `receiptGenerator.ts`
   - Generates PDF using jsPDF library with:
     - EVOTAR logo and branding
     - Election title and voting timestamp
     - List of selected candidates by position
     - Unique receipt ID
     - QR code encoding: `receiptId + verification_token`
     - Important notice: "This receipt does not reveal your identity. It only confirms your vote was recorded correctly."
     - Instructions for verification
   - PDF automatically downloads to user's device
   - Filename format: `vote_receipt_{election_title}_{timestamp}.pdf`

8. **Post-Vote Experience:**
   - User returns to dashboard
   - Election card now shows "Already Voted" badge
   - "Cast Vote" button disabled
   - User can still view candidates and (if published) results
   - Toast notification: "Your vote has been cast successfully! Receipt downloaded."

**Receipt Verification Process:**

1. Open downloaded PDF receipt
2. Scan QR code using any QR code reader or smartphone camera
3. Redirected to: `www.evotar.xyz/verify?receiptId={id}&token={token}`
4. System calls `verify_vote_receipt()` RPC function
5. Verification page displays:
   - "Vote Verified" confirmation badge
   - Election name and ID
   - Voting date and time
   - Receipt ID
   - List of selected candidates by position
   - Blockchain transaction hash (if blockchain integration active)
   - Verification timestamp
6. Green checkmark confirms: "Your vote is secure and anonymous. This confirmation proves your selections were recorded accurately without exposing your identity."

---

### **3.7.3 Profile Update Request Workflow**

Users cannot directly modify their profile information after registration to maintain data integrity. Instead, they must submit update requests for staff/administrator approval.

**Requesting Profile Update:**

1. Navigate to Dashboard → Settings Tab → Profile section
2. Click "Request Profile Update" button
3. Fill out profile update request form:

   **Email Change:**
   - Current Email: (auto-filled, read-only)
   - New Email: Enter new email address
   - Must confirm new email is valid and accessible

   **Year Level Change:**
   - Current Year Level: (auto-filled, read-only)
   - New Year Level: Select from dropdown (1st, 2nd, 3rd, 4th Year)
   - Typically used when advancing to next academic year

   **Note:** Cannot change: Student ID, Full Name, Course, Gender
   - These require contacting administrator directly with supporting documents

4. Click "Submit Request"
5. System creates record in `profile_update_requests` table with status "Pending"
6. User receives toast notification: "Profile update request submitted. You'll be notified when reviewed."
7. Request appears in user's Settings page under "Pending Requests" section

**Staff/Administrator Review Process:**

1. **Staff Dashboard:**
   - Navigate to Dashboard → "Profile Update Requests" tab
   - View list of all pending requests showing:
     - Student name and ID
     - Current vs. Requested email
     - Current vs. Requested year level
     - Request submission date
   - Click "Review" button on any request

2. **Review Dialog:**
   - Displays side-by-side comparison:
     - Left column: Current profile information
     - Right column: Requested changes (highlighted in yellow)
   - Shows student ID image for identity verification
   - Provides context: "Request submitted on {date} at {time}"

3. **Decision Options:**

   **Option A: Approve Request**
   - Click "Approve" button
   - System calls RPC: `approve_profile_update_request(request_id, reviewer_id)`
   - Function automatically:
     - Updates `profiles` table with new values
     - Sets request status to "Approved"
     - Records reviewer_id and reviewed_at timestamp
     - Sends notification to user: "Your profile update request has been approved"
     - Logs action in `audit_logs` table

   **Option B: Reject Request**
   - Click "Reject" button
   - Required: Enter admin notes explaining rejection reason
   - Example: "Email domain not recognized. Please use institutional email (@sjc.edu.ph)"
   - System calls RPC: `reject_profile_update_request(request_id, reviewer_id, notes)`
   - Function:
     - Sets request status to "Rejected"
     - Stores admin notes
     - Records reviewer_id and reviewed_at timestamp
     - Sends notification to user with rejection reason
     - Logs action in audit_logs

4. **User Notification:**
   - User receives real-time notification on dashboard
   - If approved: Profile immediately reflects changes
   - If rejected: User can view rejection reason and resubmit with corrections

**Profile Update Request Limitations:**

- Users can have only **one pending request at a time**
- If request is rejected, must wait for rejection notification before resubmitting
- Email changes require re-verification (confirmation email sent to new address)
- Year level changes are typically processed at start of academic semester
- For name corrections or course changes, users must contact administrator directly

---

### **3.7.4 Two-Factor Authentication (2FA) Setup**

EVOTAR implements TOTP (Time-based One-Time Password) 2FA for enhanced account security.

**Enabling 2FA:**

1. Navigate to Dashboard → Settings → Security Settings
2. Click "Enable Two-Factor Authentication" button
3. **Step-Up Verification Required:**
   - Re-enter your password
   - System validates current password
   - Creates step-up verification token (15-minute validity)

4. **QR Code Generation:**
   - System generates TOTP secret using OTPAuth library
   - Creates QR code encoding: `otpauth://totp/EVOTAR:{student_id}?secret={secret}&issuer=EVOTAR`
   - Displays QR code on screen

5. **Authenticator App Setup:**
   - Open authenticator app (Google Authenticator, Authy, Microsoft Authenticator, etc.)
   - Scan QR code with app
   - App displays 6-digit code that refreshes every 30 seconds
   - Alternatively, manually enter secret key (displayed below QR code)

6. **Verification:**
   - Enter 6-digit code from authenticator app
   - System validates code using `OTPAuth.TOTP.validate()` function
   - Must match within 30-second window

7. **Recovery Codes Generation:**
   - Upon successful verification, system generates 10 recovery codes
   - Each code is:
     - Cryptographically secure random 10-character string
     - One-time use only
     - Encrypted before storage in database
   - Codes displayed on screen: "Save these recovery codes in a secure location!"
   - User can:
     - Copy codes to clipboard
     - Download as text file (`evotar_recovery_codes.txt`)
     - Print codes
   - **Critical warning:** "If you lose access to your authenticator app and recovery codes, you will be locked out of your account!"

8. **Confirmation:**
   - System updates `profiles.two_factor_enabled = true`
   - Stores encrypted `two_factor_secret` and `two_factor_recovery_codes`
   - User returns to Security Settings page
   - Badge now shows: "2FA Enabled ✓"
   - Logs action in audit_logs

**Using 2FA on Login:**

1. Enter Student ID and Password (first factor)
2. If 2FA enabled, system presents second verification screen:
   - "Enter 6-digit code from your authenticator app"
   - Input field for code
   - "Use Recovery Code" link (if lost access to app)
3. Enter current code from authenticator app
4. System validates code:
   - Checks if code matches expected value
   - Verifies code hasn't been used already (replay attack prevention)
   - Allows 30-second time skew tolerance
5. Upon successful validation:
   - User granted access to dashboard
   - Login action logged in audit_logs

**Using Recovery Codes:**

1. On 2FA prompt, click "Use Recovery Code" link
2. Enter one of your 10 recovery codes
3. System validates:
   - Code exists in user's encrypted recovery code list
   - Code hasn't been used before
4. Upon successful validation:
   - Code is marked as used (cannot be reused)
   - User granted access
   - **Recommendation displayed:** "You have used a recovery code. Please disable and re-enable 2FA to generate new recovery codes."
5. If all 10 codes exhausted:
   - User must contact administrator to disable 2FA
   - Administrator can reset 2FA status after identity verification

**Disabling 2FA:**

1. Navigate to Security Settings
2. Click "Disable Two-Factor Authentication"
3. **Step-Up Verification Required:**
   - Enter current password
   - If 2FA currently enabled, also enter current 6-digit code
4. System confirms: "Are you sure? This will reduce your account security."
5. Upon confirmation:
   - `profiles.two_factor_enabled = false`
   - `two_factor_secret` and `two_factor_recovery_codes` deleted
   - User can re-enable 2FA at any time (generates new secret and codes)
   - Action logged in audit_logs
