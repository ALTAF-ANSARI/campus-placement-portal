# Campus Placement & Referral Portal with Auto-Resume Matcher
## REST API Architecture & Interface Specification (v1)

> **Document Classification:** Technical Design Specification — Design Only  
> **Target Engine:** Node.js (v20+ LTS) / Express.js / TypeScript  
> **API Version Namespace:** `/api/v1`  
> **Authoritative Persistence:** PostgreSQL (ACID) via Prisma ORM  
> **Document Status:** Authoritative Architectural Blueprint (Day 2 — Step 7)

---

## 1. API Design Principles & Architecture Boundary

The Application Programming Interface (API) represents the authoritative operational boundary of the Campus Placement & Referral Portal. Every external interaction—whether initiated by the Next.js client, administrative tools, or background automation—is governed by the following core architectural laws:

1. **RESTful Resource-Oriented Architecture:** The API models recruitment domains as discrete, noun-based resources (`/jobs`, `/applications`, `/interviews`, `/offers`, `/resumes`). Actions are mapped cleanly to standard HTTP verbs (`GET`, `POST`, `PATCH`, `DELETE`).
2. **Unified Data Interchange Envelope:** All request and response bodies utilize standard JSON (`application/json`) unless explicitly specified otherwise (such as multipart binary file uploads or streaming CSV/XLSX export downloads).
3. **Versioned API Namespace:** All public and authenticated endpoints are rooted strictly beneath `/api/v1/`. Breaking changes to request schemas or response payloads must be introduced under a new version root (`/api/v2/`) to maintain contract stability.
4. **Authoritative State vs. Identity Context:**
   - **JSON Web Tokens (JWT):** Bearer tokens are used exclusively for stateless cryptographic verification of user identity (`sub`), global role (`role`), and token lifecycle (`iat`, `exp`).
   - **PostgreSQL Database:** PostgreSQL is the **sole authoritative source of truth** for all mutable business, security, and verification states (`is_debarred`, `verification_status`, `placement_status`, drive approval status, and application lifecycles). Client tokens are never trusted for mutable authorization decisions.
5. **Mandatory Server-Side Boundary Validation:** Incoming request payloads (headers, route parameters, query strings, and request bodies) are parsed and strictly validated at the controller boundary using typed **Zod schemas**. Unwhitelisted attributes are stripped automatically to prevent parameter pollution and injection attacks.
6. **Explicit Finite State Machine Enforcement:** State-changing endpoints never permit arbitrary mutation of status columns. Status transitions must pass strict server-side state machine validators, ensuring that only explicitly defined lifecycle transitions are permitted. Invalid transitions are deterministically rejected with `HTTP 409 Conflict` or `HTTP 422 Unprocessable Entity`.
7. **Canonical UUID Resource Addressing:** All database entities are identified by universally unique identifiers (UUIDv4). Monotonically increasing sequential integer IDs are prohibited at the API boundary to prevent sequential enumeration exploits.
8. **Universal UTC Timestamps:** All timestamps transmitted or received via the API adhere to ISO 8601 extended format in Coordinated Universal Time (`YYYY-MM-DDTHH:mm:ss.sssZ`). Presentation-tier conversion to Indian Standard Time (IST / `Asia/Kolkata`) is performed strictly at the client boundary.
9. **Architectural Separation of Concerns:**
   - **Authentication:** Verifies cryptographic identity proof (`401 Unauthorized` if invalid or expired).
   - **Authorization:** Verifies role permissions and resource ownership boundaries (`403 Forbidden` if unauthorized).
   - **Validation:** Verifies schema structure and field constraints (`422 Unprocessable Entity` or `400 Bad Request`).
   - **Business Rules & State Engines:** Verifies domain rules and state machine invariants (`409 Conflict`).
   - **Persistence & Audit:** Executes atomic mutations inside transactional boundaries and produces append-only audit records.

---

## 2. API Conventions & Standard Response Envelopes

### 2.1 Base Path
All endpoints documented herein are relative to:
```
/api/v1
```

### 2.2 HTTP Method Semantics
The API enforces standard HTTP semantics across all routes:
- **`GET`:** Safe and idempotent. Used exclusively for resource retrieval and read-only query aggregations. Must never cause state mutations.
- **`POST`:** Non-idempotent. Used for resource creation, complex state transitions (`/transition`), pre-application evaluations, authentication actions, and RPC-style domain commands (`/approve`, `/reject`, `/withdraw`).
- **`PATCH`:** Used for partial updates to existing resource attributes (e.g., updating non-status profile fields or editing draft job details).
- **`PUT`:** Restricted strictly to complete idempotent replacements of document resources where genuinely required (e.g., replacing master profile configuration).
- **`DELETE`:** Restricted strictly to explicit resource retraction or cancellation where permitted by institutional governance (e.g., cancelling an interview slot).

### 2.3 Standard Architectural Response Envelopes
To provide deterministic contract predictability for frontend consumption, all API responses adhere to unified envelope structures:

#### Success Response Envelope (`HTTP 200 OK`, `HTTP 201 Created`):
```json
{
  "success": true,
  "data": {
    "id": "c7a8b3d2-4e1f-4b8a-9c2d-1e5f8a3b4c5d",
    "status": "ACTIVE",
    "createdAt": "2026-09-24T18:30:00.000Z"
  }
}
```

#### Paginated List Success Envelope:
```json
{
  "success": true,
  "data": [
    { "id": "uuid-1", "title": "Software Engineer" },
    { "id": "uuid-2", "title": "Data Analyst" }
  ],
  "meta": {
    "page": 1,
    "pageSize": 20,
    "totalCount": 42,
    "totalPages": 3,
    "hasNextPage": true,
    "hasPrevPage": false
  }
}
```

#### Error Response Envelope (`HTTP 4xx`, `HTTP 5xx`):
```json
{
  "success": false,
  "error": {
    "code": "INVALID_STATE_TRANSITION",
    "message": "Cannot transition application directly from APPLIED to ACCEPTED.",
    "details": {
      "currentStatus": "APPLIED",
      "attemptedStatus": "ACCEPTED",
      "allowedNextStatuses": ["ATS_SHORTLISTED", "REJECTED", "WITHDRAWN", "AUTO_WITHDRAWN"]
    }
  }
}
```

---

## 3. Authentication API Boundary

Authentication endpoints provide secure credential verification, issuance of signed stateless JWT tokens, and inspection of current session identity.

### 3.1 Student Registration
- **Route:** `POST /api/v1/auth/register/student`
- **Purpose:** Onboard an enrolled student account.
- **Actor:** Public / Prospective Student.
- **Auth Requirement:** None (Public).
- **Request Payload:**
  ```json
  {
    "email": "student@institution.edu",
    "password": "SecurePassword123!",
    "full_name": "Aarav Sharma",
    "roll_number": "2022CS104",
    "department": "Computer Science & Engineering",
    "graduation_year": 2026,
    "cgpa": 8.75,
    "active_backlogs": 0,
    "tenth_percentage": 92.5,
    "twelfth_percentage": 89.0
  }
  ```
- **Validation & Business Rules:**
  - `email` must match institutional email domain pattern (`@institution.edu`).
  - `password` is verified for minimum length and complexity before hashing with memory-hard Argon2id or bcrypt. Passwords are **never** stored in plaintext.
  - Automatically creates base `User` record (`role: STUDENT`) and linked `StudentProfile` (`verification_status: UNVERIFIED`, `is_debarred: false`, `placement_status: UNPLACED`).
  - Executed inside an atomic PostgreSQL transaction.
- **Responses:**
  - `201 Created`: Student registered successfully. Returns sanitized user record and initial JWT.
  - `409 Conflict`: Institutional email or roll number already registered (`DUPLICATE_RESOURCE`).
  - `422 Unprocessable Entity`: Zod validation failure on academic fields or password complexity.

### 3.2 Recruiter Registration
- **Route:** `POST /api/v1/auth/register/recruiter`
- **Purpose:** Register a corporate recruiter or alumni hiring partner.
- **Actor:** Public / Prospective Recruiter.
- **Auth Requirement:** None (Public).
- **Request Payload:**
  ```json
  {
    "email": "recruiter@enterprise.com",
    "password": "CorporatePassword456!",
    "full_name": "Priya Nair",
    "designation": "Lead Campus Talent Partner",
    "company_id": "c1a2b3c4-d5e6-7f8a-9b0c-1d2e3f4a5b6c"
  }
  ```
- **Validation & Business Rules:**
  - `company_id` must reference an existing approved `Company` record.
  - Creates base `User` (`role: RECRUITER`) and linked `RecruiterProfile` (`approval_status: PENDING`).
  - **Recruiter Security Invariant:** Unapproved recruiters (`PENDING` or `REJECTED`) are forbidden from posting jobs or accessing student pipelines.
- **Responses:**
  - `201 Created`: Recruiter registered; profile submitted for administrative approval.
  - `409 Conflict`: Corporate email already registered.
  - `422 Unprocessable Entity`: Invalid company reference or validation error.

### 3.3 User Login
- **Route:** `POST /api/v1/auth/login`
- **Purpose:** Authenticate credentials and issue a signed stateless JWT.
- **Actor:** Registered `STUDENT`, `RECRUITER`, or `TPC_ADMIN`.
- **Auth Requirement:** None (Public, rate-limited).
- **Request Payload:**
  ```json
  {
    "email": "user@institution.edu",
    "password": "UserPassword123!"
  }
  ```
- **Validation & Business Rules:**
  - Verifies submitted password against hashed credentials in database.
  - Generates minimal signed JWT containing `{ sub, role, iat, exp }`. Optional non-sensitive identity claims such as `email` may be included only when required.
  - Rate-limited to prevent credential stuffing (e.g., 5 attempts per IP per minute).
  - Password hashes, salts, and internal security tokens are **never** returned in responses.
- **Responses:**
  - `200 OK`: Successful authentication. Returns `{ token, user: { id, email, role, fullName } }`.
  - `401 Unauthorized`: Invalid email or password (`AUTH_INVALID`).
  - `429 Too Many Requests`: Rate limit exceeded.

### 3.4 Current Authenticated Session Inspection
- **Route:** `GET /api/v1/auth/me`
- **Purpose:** Resolve the active user's identity and live database profile context.
- **Actor:** Authenticated User (`STUDENT`, `RECRUITER`, `TPC_ADMIN`).
- **Auth Requirement:** `Authorization: Bearer <token>`.
- **Validation & Business Rules:**
  - Extracts `sub` from verified JWT.
  - Re-queries PostgreSQL live to fetch authoritative profile flags (`is_debarred`, `verification_status`, `placement_status`, `approval_status`).
- **Responses:**
  - `200 OK`: Returns authenticated user entity, global role, and full profile context.
  - `401 Unauthorized`: Token missing, expired, or cryptographically invalid.

---

## 4. User & Profile APIs

Profile endpoints allow users to manage their authorized personal information while strictly walling off administrative flags.

### 4.1 Student Profile
- **`GET /api/v1/students/me`**
  - **Actor:** `STUDENT`.
  - **Rules:** Returns the authenticated student's academic profile, current verification status (`UNVERIFIED` / `VERIFIED`), debarment status, and institutional placement status (`UNPLACED` / `PLACED`).
  - **Ownership:** Scoped strictly to `req.user.sub`. Returns `404 Not Found` or `403 Forbidden` if a student attempts to query another student's profile.
- **`PATCH /api/v1/students/me`**
  - **Actor:** `STUDENT`.
  - **Allowed Fields:** Contact phone, personal bio, LinkedIn URL, portfolio link.
  - **Restricted Fields:** Academic metrics (`cgpa`, `active_backlogs`, `tenth_percentage`, `twelfth_percentage`), `verification_status`, `is_debarred`, and `placement_status` **MUST NOT** be client-editable. Attempting to pass restricted fields causes them to be stripped or rejected with `422 Unprocessable Entity`.

### 4.2 Recruiter Profile
- **`GET /api/v1/recruiters/me`**
  - **Actor:** `RECRUITER`.
  - **Rules:** Returns recruiter professional details, assigned company entity, and live institutional approval status (`PENDING`, `APPROVED`, `REJECTED`).
- **`PATCH /api/v1/recruiters/me`**
  - **Actor:** `RECRUITER`.
  - **Allowed Fields:** Designation, contact phone.
  - **Restricted Fields:** `approval_status`, `company_id`, and administrative clearance flags are immutable to the recruiter.

### 4.3 Administrative Profile Governance
- **`GET /api/v1/admin/students`**
  - **Actor:** `TPC_ADMIN`.
  - **Rules:** Institution-wide paginated search of student academic profiles. Supports filtering by department, graduation year, verification status, and debarment flag.
- **`GET /api/v1/admin/recruiters`**
  - **Actor:** `TPC_ADMIN`.
  - **Rules:** Institution-wide list of recruiter accounts with approval status and organization details.

---

## 5. Recruiter Governance & Approval APIs

All recruiter accounts require explicit institutional clearance before participating in campus recruitment drives.

### 5.1 Endpoints
- **`GET /api/v1/admin/recruiters/pending`**
  - **Actor:** `TPC_ADMIN`.
  - **Purpose:** Retrieve the queue of recruiter registrations awaiting institutional clearance (`approval_status: PENDING`).
- **`POST /api/v1/admin/recruiters/:recruiterId/approve`**
  - **Actor:** `TPC_ADMIN`.
  - **Purpose:** Grant institutional approval (`approval_status: APPROVED`).
  - **Rules:** Target recruiter must currently be in `PENDING` or `REJECTED` status. Emits an append-only entry to `audit_logs` capturing the approving admin ID, target recruiter ID, and timestamp.
- **`POST /api/v1/admin/recruiters/:recruiterId/reject`**
  - **Actor:** `TPC_ADMIN`.
  - **Request Payload:** `{ "rejection_reason": "Unable to verify corporate credentials." }`
  - **Purpose:** Deny institutional clearance (`approval_status: REJECTED`). Requires an administrative reason string. Logs audit trail.

---

## 6. Job Posting APIs & Governance Lifecycle

Job postings progress through strict administrative oversight to guarantee institutional compliance before student visibility.

### 6.1 Authoritative Job Statuses
The API supports exactly these 6 job statuses:
- `DRAFT`: Authoring phase. Private to recruiter.
- `PENDING_APPROVAL`: Submitted to TPC for review. Invisible to students.
- `ACTIVE`: Approved and published. Open for student applications before deadline.
- `REJECTED_BY_TPC`: Denied by TPC with feedback. Invisible to students.
- `CLOSED`: Application window concluded. Zero new applications accepted.
- `ARCHIVED`: Finalized historical drive record.

### 6.2 Recruiter Job Endpoints
- **`POST /api/v1/jobs`**
  - **Actor:** `RECRUITER` (approved company scope) or `TPC_ADMIN`.
  - **Request Payload:** Job title, description, required skills array, compensation (CTC/stipend), eligibility criteria thresholds (`min_cgpa`, `max_active_backlogs`, `eligible_departments`, `target_graduation_year`, `min_tenth_percentage`, `min_twelfth_percentage`), and `application_deadline`.
  - **Rules:**
    - Recruiter-created jobs are initialized strictly as `DRAFT` or submitted directly as `PENDING_APPROVAL`.
    - Recruiter jobs **NEVER** enter `ACTIVE` upon creation.
    - `TPC_ADMIN` may create institutional drives directly as `ACTIVE`.
- **`GET /api/v1/jobs/mine`**
  - **Actor:** `RECRUITER`.
  - **Rules:** Lists all postings authored by the recruiter's approved organization across all statuses (`DRAFT`, `PENDING_APPROVAL`, `ACTIVE`, etc.).
- **`GET /api/v1/jobs/:jobId`**
  - **Actor:** `RECRUITER` (must own job), `TPC_ADMIN` (any job), or `STUDENT` (only if `status === 'ACTIVE'`).
  - **IDOR Protection:** If a student requests a job in `DRAFT`, `PENDING_APPROVAL`, or `REJECTED_BY_TPC`, the API returns `404 Not Found`.
- **`PATCH /api/v1/jobs/:jobId`**
  - **Actor:** `RECRUITER` (owner) or `TPC_ADMIN`.
  - **Rules:**
    - If edited while `status === 'DRAFT'`, status remains `DRAFT`.
    - If edited while `status === 'PENDING_APPROVAL'`, status **remains strictly `PENDING_APPROVAL`**. `updated_at` is refreshed and TPC is notified. No additional Job status is created.
    - If edited while `status === 'ACTIVE'`, core eligibility thresholds are locked if applications already exist to preserve compliance integrity.
- **`POST /api/v1/jobs/:jobId/close`**
  - **Actor:** `RECRUITER` (owner) or `TPC_ADMIN`.
  - **Rules:** Transitions job `ACTIVE` $\rightarrow$ `CLOSED`. Closes drive to all further submissions.

### 6.3 Student Job Feed Endpoints
- **`GET /api/v1/jobs`**
  - **Actor:** `STUDENT`.
  - **Rules:** Returns paginated list of recruitment drives. The backend unconditionally filters results to return only drives where status is `ACTIVE` and `application_deadline` is in the future. Students can **never** discover unapproved, closed, or draft drives.

### 6.4 TPC Administrative Job Governance Endpoints
- **`GET /api/v1/admin/jobs/pending`**
  - **Actor:** `TPC_ADMIN`.
  - **Purpose:** Queue of job postings in `PENDING_APPROVAL` awaiting administrative review.
- **`POST /api/v1/admin/jobs/:jobId/approve`**
  - **Actor:** `TPC_ADMIN`.
  - **Rules:** Validates current status is `PENDING_APPROVAL`. Transitions job $\rightarrow$ `ACTIVE`. Drive is now live for students. Emits audit log.
- **`POST /api/v1/admin/jobs/:jobId/reject`**
  - **Actor:** `TPC_ADMIN`.
  - **Request Payload:** `{ "rejection_reason": "Minimum CTC does not meet institutional placement policy." }`
  - **Rules:** Transitions job `PENDING_APPROVAL` $\rightarrow$ `REJECTED_BY_TPC`. Recruiter can inspect feedback, revert job to `DRAFT`, amend details, and resubmit.

---

## 7. Eligibility Verification API (Pre-Application Gate)

Eligibility is architected as an **independent, pre-application evaluation gate**. It is evaluated before application creation and is **NEVER** an application lifecycle status.

### 7.1 Endpoint
- **`GET /api/v1/jobs/:jobId/eligibility`**
- **Actor:** Authenticated `STUDENT`.
- **Purpose:** Evaluate whether the requesting student satisfies institutional placement policies and drive-specific academic criteria.
- **Authoritative Data Evaluated (Live PostgreSQL Query):**
  1. `student_profiles.verification_status === 'VERIFIED'`
  2. `student_profiles.is_debarred === false`
  3. `student_profiles.placement_status === 'UNPLACED'`
  4. `student_profiles.cgpa >= job_postings.min_cgpa`
  5. `student_profiles.active_backlogs <= job_postings.max_active_backlogs`
  6. `student_profiles.department IN job_postings.eligible_departments`
  7. `student_profiles.graduation_year === job_postings.target_graduation_year`
  8. `student_profiles.tenth_percentage >= job_postings.min_tenth_percentage`
  9. `student_profiles.twelfth_percentage >= job_postings.min_twelfth_percentage`
  10. Job is currently `ACTIVE` and `application_deadline > now()`.

### 7.2 Response Contract
- **Eligible Outcome (`200 OK`):**
  ```json
  {
    "success": true,
    "data": {
      "verdict": "ELIGIBLE",
      "canApply": true,
      "reasons": []
    }
  }
  ```
- **Ineligible Outcome (`200 OK` or `403 Forbidden` on application submission):**
  ```json
  {
    "success": true,
    "data": {
      "verdict": "INELIGIBLE",
      "canApply": false,
      "reasons": [
        { "criteria": "CGPA", "required": 8.0, "actual": 7.4 },
        { "criteria": "ACTIVE_BACKLOGS", "maximumAllowed": 0, "actual": 1 }
      ]
    }
  }
  ```

### 7.3 Critical Eligibility Rules
1. **Zero Database Records on Ineligibility:** An ineligible attempt creates **ZERO** rows in `job_applications`.
2. **Server-Side Re-Check Mandatory:** Client-side eligibility indicators are UX conveniences only. The application creation endpoint re-evaluates the complete eligibility rule set inside an atomic transaction immediately before application insertion.

---

## 8. Application Management APIs

Manages candidate applications through their complete lifecycle.

### 8.1 Authoritative Application Statuses
The API supports exactly these 9 application statuses:
- `APPLIED`: Application submitted and point-in-time snapshot committed.
- `ATS_SHORTLISTED`: Recruiter or system advances applicant based on match review.
- `INTERVIEW_SCHEDULED`: Candidate allocated to interview round(s).
- `OFFER_EXTENDED`: Corporate job offer extended.
- `ACCEPTED`: Candidate accepted offer (**Terminal Success State**).
- `REJECTED`: Disqualified or interview failed (**Terminal State**).
- `WITHDRAWN`: Candidate voluntarily retracted application (**Terminal State**).
- `DECLINED`: Candidate turned down extended offer (**Terminal State**).
- `AUTO_WITHDRAWN`: Cascaded withdrawal due to placement policy lock (**Terminal State**).

*(Note: `PLACED`, `ELIGIBLE`, `INELIGIBLE`, `ATS_FAILED`, `ATS_PROCESSING`, `MATCHED`, and `SHORTLIST_PENDING` are strictly prohibited as application statuses).*

### 8.2 Application Endpoints
- **`POST /api/v1/jobs/:jobId/applications`**
  - **Actor:** `STUDENT`.
  - **Request Payload:** `{ "resume_id": "r1a2b3c4-d5e6-7f8a-9b0c-1d2e3f4a5b6c" }`
  - **Server-Side Enforcement Sequence (Atomic Transaction):**
    1. Authenticate student identity from JWT (`sub`).
    2. Query live database state: verify `verification_status === 'VERIFIED'`, `is_debarred === false`, `placement_status === 'UNPLACED'`.
    3. Query job posting: verify `status === 'ACTIVE'` and `application_deadline > now()`.
    4. Re-verify complete academic eligibility criteria. If ineligible, rollback and return `403 Forbidden` (`ELIGIBILITY_FAILED`).
    5. Verify duplicate application check (`student_id` + `job_id` unique constraint). If duplicate, return `409 Conflict` (`DUPLICATE_APPLICATION`).
    6. Verify `resume_id` belongs to the authenticated student and has extracted text available.
    7. Execute ATS matcher to compute deterministic score and skill breakdown.
    8. Insert `JobApplication` record (`status: APPLIED`).
    9. Insert immutable `ApplicationSnapshot` capturing academic metrics, resume text, ATS score, and job-side frozen metadata (`job_title_snapshot`, `company_name_snapshot`, `job_description_snapshot`, `ctc_snapshot`, `stipend_snapshot`).
    10. Insert `ApplicationStageHistory` record (`from_status: NULL`, `to_status: APPLIED`).
  - **Responses:**
    - `201 Created`: Application created successfully.
    - `403 Forbidden`: Student ineligible or debarred.
    - `409 Conflict`: Application already submitted for this job posting.
    - `422 Unprocessable Entity`: Invalid resume reference or missing required input.
- **`GET /api/v1/applications`**
  - **Actor:** `STUDENT` (lists own applications), `TPC_ADMIN` (lists institutional applications with filters).
- **`GET /api/v1/applications/:applicationId`**
  - **Actor:** Student owner, authorized job recruiter, or `TPC_ADMIN`.
  - **Returns:** Application status, submitted snapshot, ATS match breakdown, interview details, and offer status.
- **`POST /api/v1/applications/:applicationId/withdraw`**
  - **Actor:** `STUDENT` (must own application).
  - **Rules:** Permitted strictly when `status IN ('APPLIED', 'ATS_SHORTLISTED')`. Once interviews are scheduled or offers extended, voluntary withdrawal is disabled; candidate must decline or coordinate with TPC. Transitions application $\rightarrow$ `WITHDRAWN`.

---

## 9. Application State Transition API

State transitions are governed by an explicit RPC-style transition endpoint to prevent arbitrary field mutations.

### 9.1 Endpoint
- **`POST /api/v1/applications/:applicationId/transition`**
- **Actor:** Authorized Recruiter, TPC Admin, or Student (according to transition authority).
- **Request Payload:**
  ```json
  {
    "target_status": "ATS_SHORTLISTED",
    "reason": "Top 10 percentile ATS match score and verified technical background.",
    "metadata": {}
  }
  ```

### 9.2 Server-Side Execution & Validation Pipeline
1. **Load Current State:** Fetch target `JobApplication` row from PostgreSQL with row lock (`FOR UPDATE`).
2. **Actor Authorization:** Verify requesting actor is authorized to trigger the specific transition (see Section 18 Authorization Matrix).
3. **Finite State Machine Validation:** Validate that `target_status` is an explicitly allowed next state from `current_status`:
   - `APPLIED` $\rightarrow$ `ATS_SHORTLISTED`, `REJECTED`, `WITHDRAWN`, `AUTO_WITHDRAWN`
   - `ATS_SHORTLISTED` $\rightarrow$ `INTERVIEW_SCHEDULED`, `REJECTED`, `WITHDRAWN`, `AUTO_WITHDRAWN`
   - `INTERVIEW_SCHEDULED` $\rightarrow$ `OFFER_EXTENDED`, `REJECTED`, `AUTO_WITHDRAWN`
   - `OFFER_EXTENDED` $\rightarrow$ `ACCEPTED`, `DECLINED`
4. **Terminal State Defense:** If `current_status` is terminal (`ACCEPTED`, `REJECTED`, `WITHDRAWN`, `DECLINED`, `AUTO_WITHDRAWN`), reject transition immediately.
5. **Atomic State Mutation:**
   - Update `job_applications.status = target_status`.
   - Insert row into `application_stage_histories` capturing `{ from_status: current_status, to_status: target_status, changed_by: actor_id, reason }`.
   - Emit audit log record.
6. **Error Mapping:**
   - `409 Conflict`: Attempted transition is invalid in the FSM (e.g., `APPLIED` $\rightarrow$ `ACCEPTED`).
   - `403 Forbidden`: Actor does not possess institutional permission for transition.
   - `422 Unprocessable Entity`: Request body violates schema requirements.

---

## 10. ATS & Resume Management APIs

Governs resume ingestion, private file storage, text extraction, and deterministic Jaccard similarity evaluation.

### 10.1 Resume Management Endpoints
- **`POST /api/v1/resumes`**
  - **Actor:** `STUDENT`.
  - **Content-Type:** `multipart/form-data` (`file` field).
  - **Validation & Security Controls:**
    1. Size limit: Strictly capped at 5 MB (`FILE_TOO_LARGE`).
    2. Magic-byte header verification: Reads initial bytes for `%PDF-` (`0x25504446`). Disguised executables are rejected with `422 Unprocessable Entity` (`RESUME_INVALID`).
    3. Private storage: File is written to isolated storage volume using UUID-named path (`uploads/resumes/{student_id}/{uuid}.pdf`). Never placed in public web root.
    4. Text extraction: PDF parser extracts Unicode text stream. Sanitizes whitespace.
    5. Stores `Resume` record and extracted text cache in database.
  - **Responses:**
    - `201 Created`: Resume uploaded and text parsed successfully.
    - `422 Unprocessable Entity`: Corrupted PDF or parsing failure (`RESUME_PARSE_FAILED`).
- **`GET /api/v1/resumes`**
  - **Actor:** `STUDENT`. Lists authenticated student's uploaded resumes.
- **`GET /api/v1/resumes/:resumeId`**
  - **Actor:** Student owner, recruiter reviewing candidate application, or `TPC_ADMIN`.
  - **Rules:** Proxied through authenticated Express stream with ownership verification. Returns raw PDF binary with `Content-Type: application/pdf`. Direct public URLs are **never** exposed.

### 10.2 ATS Evaluation Endpoints
- **`POST /api/v1/applications/:applicationId/ats/evaluate`**
  - **Actor:** `RECRUITER` (job owner) or `TPC_ADMIN`.
  - **Purpose:** Re-evaluate or manually trigger deterministic ATS similarity matching for a specific application.
  - **Mathematical Algorithm:**
    $$J(A, B) = \frac{|A \cap B|}{|A \cup B|}$$
    Where $A$ is the normalized canonical resume skill set and $B$ is the normalized job requirement skill set.
    - Range: `0.0 <= score <= 1.0` (or `0.0%` to `100.0%`).
    - Zero-union boundary: If $|A \cup B| == 0$, score is strictly `0.0`.
    - No fuzzy matching, embeddings, LLM scoring, or frequency weighting.
  - **Threshold Policy:** The MVP architecture does **not** hardcode an ATS shortlist threshold.
- **`GET /api/v1/applications/:applicationId/ats`**
  - **Actor:** Recruiter (job owner), `TPC_ADMIN`, or Student owner.
  - **Response Payload:**
    ```json
    {
      "success": true,
      "data": {
        "score": 0.60,
        "scorePercentage": "60.00%",
        "matchedSkills": ["javascript", "react", "node.js"],
        "missingSkills": ["express"],
        "evaluatedAt": "2026-09-24T18:35:00.000Z"
      }
    }
    ```

---

## 11. Interview Management APIs

Coordinates interview assessment rounds between corporate panels and shortlisted candidates.

### 11.1 Authoritative Interview Statuses
- `SCHEDULED`: Interview session confirmed.
- `COMPLETED`: Interview completed; feedback submitted.
- `CANCELLED`: Slot cancelled.
- `RESCHEDULED`: Slot modified to a new time window.

### 11.2 Endpoints
- **`POST /api/v1/applications/:applicationId/interviews`**
  - **Actor:** `RECRUITER` (job owner) or `TPC_ADMIN`.
  - **Rules:** Target application must be in `ATS_SHORTLISTED` or `INTERVIEW_SCHEDULED`. Server performs temporal collision detection across candidate and recruiter schedules. Automatically transitions application to `INTERVIEW_SCHEDULED` if not already set.
- **`GET /api/v1/applications/:applicationId/interviews`**
  - **Actor:** Candidate student, authorized recruiter, or `TPC_ADMIN`.
  - **Returns:** List of interview rounds, scheduled times (UTC), meeting links, and interviewer feedback.
- **`PATCH /api/v1/interviews/:interviewId`**
  - **Actor:** `RECRUITER` (owner) or `TPC_ADMIN`.
  - **Rules:** Updates interview timing or panel link. Sets status $\rightarrow$ `RESCHEDULED` and logs change history in audit logs.
- **`POST /api/v1/interviews/:interviewId/cancel`**
  - **Actor:** `RECRUITER` (owner) or `TPC_ADMIN`.
  - **Rules:** Sets interview status $\rightarrow$ `CANCELLED`. Cancellation of an interview slot does **not** automatically disqualify or reject the application. Application status and interview status remain strictly decoupled.

---

## 12. Job Offer & Placement Execution APIs

Governs formal employment offers and coordinates the institutional placement policy cascade.

### 12.1 Authoritative Offer Statuses
- `EXTENDED`: Formal offer issued by organization.
- `ACCEPTED`: Candidate formally accepted offer.
- `DECLINED`: Candidate formally turned down offer.
- `WITHDRAWN`: Recruiter retracted offer prior to candidate decision.

### 12.2 Endpoints
- **`POST /api/v1/applications/:applicationId/offers`**
  - **Actor:** `RECRUITER` (job owner) or `TPC_ADMIN`.
  - **Rules:** Application must be in `INTERVIEW_SCHEDULED`. Transitions application $\rightarrow$ `OFFER_EXTENDED`. Inserts `JobOffer` record (`status: EXTENDED`).
- **`GET /api/v1/applications/:applicationId/offers`**
  - **Actor:** Student candidate, authorized recruiter, or `TPC_ADMIN`.
- **`POST /api/v1/offers/:offerId/accept`**
  - **Actor:** `STUDENT` (must own target application).
  - **Atomic Transactional Placement Cascade (Database Transaction):**
    1. Verify `JobOffer.status === 'EXTENDED'` and offer deadline unexpired.
    2. Update target `JobOffer.status = 'ACCEPTED'`.
    3. Update target `JobApplication.status = 'ACCEPTED'`.
    4. Update `StudentProfile.placement_status = 'PLACED'`.
    5. Query all other active applications for the student (`status IN ('APPLIED', 'ATS_SHORTLISTED', 'INTERVIEW_SCHEDULED')`) and cascade their status to `AUTO_WITHDRAWN`.
    6. Cancel upcoming interview slots associated with auto-withdrawn applications (`status = 'CANCELLED'`).
    7. Insert immutable `PlacementRecord` capturing CTC, role, company, and date.
    8. Write audit log entries and notify affected organizations.
- **`POST /api/v1/offers/:offerId/decline`**
  - **Actor:** `STUDENT` (must own target application).
  - **Rules:** Updates `JobOffer.status = 'DECLINED'`. Transitions target `JobApplication.status = 'DECLINED'`. Student remains `UNPLACED` and eligible for other drives.
- **`POST /api/v1/offers/:offerId/withdraw`**
  - **Actor:** `RECRUITER` (job owner) or `TPC_ADMIN`.
  - **Rules:** Updates `JobOffer.status = 'WITHDRAWN'`. Recruiter offer withdrawal is recorded strictly on the `JobOffer` entity. The Application FSM does **not** automatically mutate to an undocumented state in MVP.

---

## 13. Institutional Placement Governance APIs

Administrative endpoints for institutional placement oversight and policy lock configurations.

### 13.1 Endpoints
- **`GET /api/v1/admin/placements`**
  - **Actor:** `TPC_ADMIN`.
  - **Returns:** Paginated institutional placement records with salary metrics, branch-wise distribution, and company hiring numbers.
- **`POST /api/v1/admin/students/:studentId/placement-override`**
  - **Actor:** `TPC_ADMIN`.
  - **Request Payload:** `{ "reason": "Granted Dream Company / Tier-1 upgrade exception.", "exempt": true }`
  - **Rules:** TPC Admin may grant institutional policy exceptions. Students **never** have access to client endpoints that allow self-mutation of `placement_status`.

---

## 14. Notification APIs

Provides operational updates across student, recruiter, and administrator dashboards.

### 14.1 Endpoints
- **`GET /api/v1/notifications`**
  - **Actor:** Authenticated User (`STUDENT`, `RECRUITER`, `TPC_ADMIN`).
  - **Rules:** Scoped strictly to `req.user.sub`. Returns paginated in-app notifications with unread counts.
- **`POST /api/v1/notifications/:notificationId/read`**
  - **Actor:** Notification owner. Marks specific notification as read.
- **`POST /api/v1/notifications/read-all`**
  - **Actor:** Notification owner. Marks all notifications for current user as read.

*(Note: Notifications are generated asynchronously from domain events. Creating a notification is never a substitute for modifying authoritative domain state).*

---

## 15. Analytics APIs

Provides read-only operational intelligence derived from PostgreSQL state.

### 15.1 Endpoints
- **`GET /api/v1/admin/analytics/placements`**
  - **Actor:** `TPC_ADMIN`.
  - **Metrics:** Total placed percentage, average/median/highest CTC, department-wise placement rates, gender-wise distribution.
- **`GET /api/v1/admin/analytics/drives`**
  - **Actor:** `TPC_ADMIN`.
  - **Metrics:** Active recruitment drives, total participating companies, average turnaround time from posting to offer.
- **`GET /api/v1/admin/analytics/applications`**
  - **Actor:** `TPC_ADMIN`.
  - **Metrics:** Total applications submitted, funnel conversion drop-offs (Applied $\rightarrow$ Shortlisted $\rightarrow$ Interviewed $\rightarrow$ Offered $\rightarrow$ Accepted).
- **`GET /api/v1/recruiters/analytics`**
  - **Actor:** `RECRUITER`.
  - **Metrics:** Scoped strictly to jobs authored by the recruiter's approved company: applicant counts, ATS score distribution, interview completion rates.

---

## 16. Institutional Export APIs

Facilitates regulatory, accreditation, and administrative data exports.

### 16.1 Endpoints
- **`GET /api/v1/admin/exports/applications`**
  - **Actor:** `TPC_ADMIN`.
  - **Query Parameters:** `?jobId=uuid&format=csv` (or `xlsx`).
  - **Security & Integrity Controls:**
    - Streaming file generation.
    - **Formula Injection Defense:** Every string cell is inspected and sanitized. Cells beginning with formula triggers (`=`, `+`, `-`, `@`, `\t`, `\r`) are prepended with an apostrophe (`'`) to prevent remote code execution in spreadsheet software.
- **`GET /api/v1/admin/exports/placements`**
  - **Actor:** `TPC_ADMIN`.
  - **Returns:** Comprehensive institutional accreditation report for NAAC/NIRF compliance.

---

## 17. Append-Only Audit Log API

Provides verifiable institutional governance and regulatory traceability.

### 17.1 Endpoint
- **`GET /api/v1/admin/audit-logs`**
- **Actor:** `TPC_ADMIN` strictly.
- **Rules:**
  - Read-only paginated access to `audit_logs` table.
  - Audit records are **append-only**; no update or delete routes exist.
  - Query filters: `?actorId=uuid`, `?action=ACTION_NAME`, `?entityType=job_postings`, `?startDate=ISO&endDate=ISO`.

---

## 18. Comprehensive Role-Capability Authorization Matrix

The matrix defines explicit access capabilities across the three authoritative institutional roles:

| Domain / Resource Action | STUDENT | RECRUITER | TPC_ADMIN | Authorization & Ownership Scope Rule |
| :--- | :---: | :---: | :---: | :--- |
| **Auth / Register Student** | Public | ❌ | ❌ | Unauthenticated public registration route |
| **Auth / Register Recruiter** | ❌ | Public | ❌ | Unauthenticated public registration route |
| **Auth / Login** | Public | Public | Public | Standard rate-limited credential verification |
| **Student Profile (Read)** | ✅ (Own) | ❌ | ✅ (All) | Scoped strictly to authenticated student ID |
| **Student Profile (Update)** | ✅ (Non-academic) | ❌ | ✅ (All) | Academic metrics locked to student editing |
| **Recruiter Profile (Read)** | ❌ | ✅ (Own) | ✅ (All) | Scoped strictly to recruiter's organization |
| **Recruiter Governance (Approve/Reject)**| ❌ | ❌ | ✅ | Institutional clearance authority only |
| **Jobs (Create)** | ❌ | ✅ (Draft/Pending) | ✅ (Direct Active) | Recruiter scoped strictly to assigned company |
| **Jobs (Read Active)** | ✅ | ✅ | ✅ | Active drives only for students |
| **Jobs (Read Draft/Pending)** | ❌ | ✅ (Own) | ✅ (All) | Invisible to students |
| **Jobs (Approve/Reject)** | ❌ | ❌ | ✅ | TPC Admin governance authority only |
| **Eligibility (Evaluate)** | ✅ (Self) | ❌ | ✅ | Pre-application gate; live database check |
| **Applications (Create)** | ✅ | ❌ | ❌ | Must pass server-side eligibility check |
| **Applications (Read Own)** | ✅ | ❌ | ✅ | Scoped strictly to student applicant |
| **Applications (Read Drive Pipeline)** | ❌ | ✅ (Authored jobs) | ✅ (All) | Recruiter scoped strictly to company jobs |
| **Applications (Transition Status)** | ⚠️ (Withdraw/Accept/Decline) | ✅ (Pipeline) | ✅ (All) | Governed strictly by Application FSM |
| **Resumes (Upload)** | ✅ | ❌ | ❌ | 5 MB max, magic-byte verified PDF |
| **Resumes (Read / Stream)** | ✅ (Own) | ✅ (Applicant) | ✅ (All) | Protected Express stream; zero public URLs |
| **ATS (Evaluate / View Score)** | ⚠️ (Own score) | ✅ (Applicants) | ✅ (All) | Deterministic Jaccard calculation |
| **Interviews (Schedule / Update)**| ❌ | ✅ (Authored jobs) | ✅ (All) | Collision-checked; UTC timestamps |
| **Offers (Extend / Retract)** | ❌ | ✅ (Authored jobs) | ✅ (All) | Recruiter extends; records on Offer entity |
| **Offers (Accept / Decline)** | ✅ (Own) | ❌ | ⚠️ (Emergency Override) | Acceptance triggers atomic placement cascade |
| **Placement Records (Read)** | ⚠️ (Own record) | ⚠️ (Own hires) | ✅ (Full Institutional)| Full analytics restricted to TPC Admin |
| **Notifications (Read / Mark)**| ✅ (Own) | ✅ (Own) | ✅ (Own) | Scoped strictly to authenticated user |
| **Exports (CSV / XLSX)** | ❌ | ⚠️ (Own drive CSV)| ✅ (Full Institutional)| Formula injection defense applied |
| **Audit Logs (Read)** | ❌ | ❌ | ✅ | Append-only institutional audit ledger |

---

## 19. HTTP Status Code Contract

The API enforces unambiguous, deterministic HTTP status code mappings across all endpoints:

| Status Code | Meaning | Architectural Context & Usage |
| :--- | :--- | :--- |
| **`200 OK`** | Success | Standard response for successful `GET`, `PATCH`, or non-creation RPC actions. |
| **`201 Created`** | Resource Created | Successful resource creation (`POST`), returning the created resource envelope. |
| **`204 No Content`** | Success (No Body) | Operation succeeded with no content returned (e.g., marking notifications read). |
| **`400 Bad Request`** | Malformed Syntax | JSON syntax error, malformed headers, or unparseable query parameters. |
| **`401 Unauthorized`** | Authentication Failure | Missing, expired, or cryptographically invalid `Authorization: Bearer <token>`. |
| **`403 Forbidden`** | Authorization Violation | Authenticated user lacks permission for action, failed IDOR check, or debarred student. |
| **`404 Not Found`** | Resource Not Found | Target UUID does not exist, or is hidden to prevent resource enumeration. |
| **`409 Conflict`** | State / Uniqueness Conflict | Invalid FSM transition, duplicate application submission, or unique constraint violation. |
| **`422 Unprocessable Entity`** | Validation / Semantic Error| Schema constraint violation (Zod failure), corrupted PDF, or business rule failure. |
| **`429 Too Many Requests`** | Rate Limit Exceeded | Exceeded allowed threshold for authentication, upload, or intensive operations. |
| **`500 Internal Server Error`** | Server / Database Fault | Unhandled exception, database connection loss, or unexpected runtime crash. |

---

## 20. Standard Application Error Catalogue

To ensure semantic consistency across client error handling, the API emits stable error codes inside error envelopes:

| Error Code | HTTP Status | Description |
| :--- | :---: | :--- |
| `AUTH_REQUIRED` | `401` | Missing `Authorization: Bearer <token>` header. |
| `AUTH_INVALID` | `401` | Token signature invalid, expired, or invalid credentials supplied. |
| `FORBIDDEN` | `403` | User role lacks permission for the requested endpoint. |
| `ACCOUNT_DEBARRED` | `403` | Student account is debarred from placement participation. |
| `RECRUITER_NOT_APPROVED` | `403` | Recruiter profile is pending or rejected; actions restricted. |
| `RESOURCE_NOT_FOUND` | `404` | Requested entity ID does not exist or access is denied. |
| `VALIDATION_FAILED` | `422` | Zod schema validation failed on request body or parameters. |
| `INVALID_STATE_TRANSITION`| `409` | Requested lifecycle transition violates the Finite State Machine. |
| `ELIGIBILITY_FAILED` | `403` | Student fails one or more academic eligibility criteria. |
| `JOB_NOT_ACTIVE` | `409` | Job is in draft, pending approval, closed, or deadline has passed. |
| `DUPLICATE_APPLICATION` | `409` | Candidate has already submitted an application for this drive. |
| `ALREADY_PLACED` | `403` | Student is already placed; institutional policy locks further applications. |
| `RESUME_INVALID` | `422` | File is not a valid PDF or fails magic-byte header verification. |
| `FILE_TOO_LARGE` | `422` | Uploaded document exceeds the 5 MB ceiling. |
| `RESUME_PARSE_FAILED` | `422` | PDF parser encountered corrupted data or unreadable text stream. |
| `ATS_PROCESSING_FAILED` | `422` | ATS engine encountered an evaluation failure (zero score fabricated). |
| `INTERVIEW_COLLISION` | `409` | Candidate or panel has an overlapping scheduled interview slot. |
| `OFFER_EXPIRED` | `409` | Acceptance attempted past the explicit offer expiration timestamp. |
| `RATE_LIMIT_EXCEEDED` | `429` | Request volume exceeded security threshold. |
| `INTERNAL_SERVER_ERROR` | `500` | Unhandled internal exception; logged to administrative monitoring. |

---

## 21. Pagination, Filtering & Sorting Standards

All list endpoints (`/jobs`, `/applications`, `/admin/students`, `/notifications`, `/audit-logs`) implement standardized, server-side pagination:

### 2.1 Query Parameters
- **`page`:** 1-based page number (default: `1`).
- **`pageSize`:** Number of items per page (default: `20`, maximum: `100`).
- **`sortBy`:** Field name to order by (e.g., `createdAt`, `cgpa`, `application_deadline`).
- **`sortOrder`:** Direction (`asc` or `desc`, default: `desc`).
- **Filtering:** Explicitly whitelisted filter keys (e.g., `status=ACTIVE`, `department=CSE`).

### 21.2 Execution Rule
Pagination is executed strictly at the database layer using database query limit and offset parameters. Fetching full result sets into server memory for in-process slicing is strictly prohibited.

---

## 22. Resource Ownership & IDOR Protection Rules

Insecure Direct Object Reference (IDOR) vulnerabilities are prevented through mandatory, database-scoped tenant checks inside domain services:

1. **Student Resource Ownership:**
   - The server verifies that the requested resource belongs to the authenticated student.
   - Ownership is checked against authoritative PostgreSQL data.
   - UUID knowledge alone never grants access.
   - Unauthorized or intentionally hidden resources return the appropriate `403 Forbidden` or `404 Not Found` response.

2. **Recruiter Organizational Ownership:**
   - The server verifies that the requested job belongs to the recruiter's authorized organization/company scope.
   - This verification uses authoritative PostgreSQL data.
   - UUID knowledge alone never grants access.

3. **UUIDs Are Not Authorization:** The unguessability of UUIDv4 does not substitute for ownership checks. Knowing a target UUID must never bypass role or tenancy verification.

---

## 23. Rate Limiting & File Upload Boundaries

To defend against Denial of Service (DoS) and brute-force credential stuffing:
- **Authentication Endpoints (`/auth/login`, `/auth/register/*`):** Rate-limited to 5 requests per IP per minute.
- **Resume Uploads (`/resumes`):** Rate-limited to 10 uploads per student per hour; payload capped strictly at 5 MB.
- **State Transition & Bulk Endpoints:** Rate-limited to 30 requests per minute per authenticated user.

---

## 24. Transactional Boundaries (Database Transactions)

Multi-entity operations that mandate relational ACID guarantees must execute within explicit database transactions:

1. **Application Creation:**
   - Verify live eligibility + Insert `JobApplication` + Insert `ApplicationSnapshot` + Insert initial `ApplicationStageHistory` (`NULL` $\rightarrow$ `APPLIED`).
2. **Offer Acceptance & Placement Lock:**
   - Update target `JobOffer` (`ACCEPTED`) + Update target `JobApplication` (`ACCEPTED`) + Update `StudentProfile` (`placement_status = 'PLACED'`) + Cascade other applications (`AUTO_WITHDRAWN`) + Cancel interviews (`CANCELLED`) + Insert `PlacementRecord` + Emit Audit Logs.
3. **Job Approval / Activation:**
   - Update `JobPosting.status = 'ACTIVE'` + Log `AuditLog` entry.

---

## 25. API Security Boundary & Defense-in-Depth

1. **Zero Client Trust:** The backend never trusts client-supplied roles, placement flags, eligibility claims, or status overrides.
2. **Password & Secret Isolation:** Database connection strings and JWT secrets are managed via system environment variables. Password hashes must never be included in API responses or exposed to application-layer consumers.
3. **Private File Delivery:** Resumes are never served statically. Access requires authenticated Express stream handlers verifying student/recruiter ownership.
4. **Input Sanitization:** Incoming payloads are parsed through strict Zod schemas with unknown keys stripped.
5. **Formula Injection Sanitization:** All spreadsheet and CSV exports neutralize dangerous formula triggers (`=`, `+`, `-`, `@`).

---

## 26. API Versioning & Future Compatibility

All endpoints documented herein are housed under the `/api/v1` namespace.
- **Backward-Compatible Changes:** Adding optional response fields or optional query parameters can occur within `/api/v1`.
- **Breaking Changes:** Removing fields, renaming fields, altering error semantics, or changing state machine rules requires a new version namespace (`/api/v2`).
- **No v2 Implementation in MVP:** The MVP boundary is strictly confined to `/api/v1`.

---

## 27. Day 2 API Implementation Boundary

> [!IMPORTANT]
> **Documentation-Only Notice:**  
> This specification defines the complete architectural boundary, endpoint routes, actor permissions, request/response schemas, validation rules, state transitions, and error semantics for Day 2.
>
> The following items are strictly prohibited from implementation during Day 2:
> - Express route definitions (`express.Router()`).
> - Controller implementations.
> - Service layer code.
> - Middleware code.
> - Prisma schema (`schema.prisma`) or database migrations.
> - Frontend API client libraries or React components.
>
> Full software implementation begins strictly on Day 3.

---

## 28. Comprehensive Architectural Consistency Audit

This specification has been thoroughly audited and verified against [`docs/requirements.md`](file:///c:/Users/happy/Desktop/campus-placement-portal/docs/requirements.md), [`docs/architecture.md`](file:///c:/Users/happy/Desktop/campus-placement-portal/docs/architecture.md), and [`docs/database-design.md`](file:///c:/Users/happy/Desktop/campus-placement-portal/docs/database-design.md):

- **Institutional Roles:** Strictly 3 roles (`STUDENT`, `RECRUITER`, `TPC_ADMIN`).
- **Job Statuses:** Exactly 6 statuses (`DRAFT`, `PENDING_APPROVAL`, `ACTIVE`, `REJECTED_BY_TPC`, `CLOSED`, `ARCHIVED`).
- **Application Statuses:** Exactly 9 statuses (`APPLIED`, `ATS_SHORTLISTED`, `INTERVIEW_SCHEDULED`, `OFFER_EXTENDED`, `ACCEPTED`, `REJECTED`, `WITHDRAWN`, `DECLINED`, `AUTO_WITHDRAWN`).
- **Student Placement Status:** Decoupled as `UNPLACED` / `PLACED` on `StudentProfile`.
- **Eligibility Verdicts:** Evaluated pre-application as `ELIGIBLE` / `INELIGIBLE`.
- **Interview Statuses:** Exactly 4 statuses (`SCHEDULED`, `COMPLETED`, `CANCELLED`, `RESCHEDULED`).
- **Offer Statuses:** Exactly 4 statuses (`EXTENDED`, `ACCEPTED`, `DECLINED`, `WITHDRAWN`).
- **Initial History Transition:** Strictly `NULL` $\rightarrow$ `APPLIED`.
- **Zero Forbidden Enums:** Verified zero occurrences of `PLACED` as application status, `ELIGIBLE`/`INELIGIBLE` as application status, or any invented ATS application statuses (`ATS_PROCESSING`, `ATS_FAILED`, `MATCHED`, `SHORTLIST_PENDING`). No additional Job statuses exist beyond the authoritative six.
- **Recruiter Job Edits in Review:** Status remains strictly `PENDING_APPROVAL`.
- **Deterministic ATS:** Pure Jaccard similarity ($0.0 \le J \le 1.0$), zero-union $= 0.0$, no invented thresholds, no fuzzy matching, no embeddings, no LLM scoring.
- **Append-Only Audit Log:** Fully preserved.
- **No Executable Code:** No executable TypeScript, JavaScript, Prisma, or framework implementation snippets remain.
- **Documentation-Only Scope:** API design remains strictly architectural and documentation-only.
- **No Route Implementation:** Zero Express route handlers or router registrations exist.
- **No Service / Controller Implementation:** Zero backend controller or service layer implementations exist.
- **No Schema / Migration Implementation:** Zero Prisma schemas (`schema.prisma`) or database migrations exist.
- **No Frontend Implementation:** Zero React/Next.js frontend client components or pages exist.
