# Requirements Specification: Campus Placement & Referral Portal with Auto-Resume Matcher

---

## 1. Problem Statement & Project Objectives

### 1.1 Context & Inefficiencies in Traditional Campus Placement Systems
Campus recruitment in higher education institutions typically operates under high volume, strict deadlines, and rigid institutional policies. In traditional setups, placement management relies on fragmented tools: Google Forms for data collection, shared spreadsheets for eligibility filtering, email/WhatsApp groups for circular announcements, and manual resume collections via shared cloud folders.

This fragmented operational model introduces severe systemic bottlenecks:
- **Data Inconsistency & Desynchronization:** Student academic metrics (CGPA, active backlogs, cleared backlogs) shift across semesters. Spreadsheets quickly become desynchronized, leading to ineligible students applying or eligible students being mistakenly excluded.
- **High Administrative Burden:** The Training & Placement Cell (TPC) must manually verify academic criteria for hundreds or thousands of students across dozens of concurrent corporate recruitment drives.
- **Process Opacity:** Students and recruiters lack visibility into application progress, shortlisting status, and interview rounds.
- **Decoupled Alumni Referrals:** Alumni referral opportunities are distributed informally across messaging channels and social media, lacking institutional tracking, eligibility guardrails, or application status visibility.

---

### 1.2 Persona-Specific Challenges

#### A. Student Challenges
1. **Application Clutter & Information Asymmetry:** Information regarding drive deadlines, company criteria, salary breakdowns (fixed vs. variable CTC), and interview stages is scattered across emails, circulars, and instant messaging channels.
2. **Eligibility Ambiguity:** Students often submit applications without knowing whether they meet nuanced company criteria (e.g., minimum 10th/12th percentages, maximum active backlog counts, or allowed branch specializations), only to be disqualified late in the process.
3. **Black-Box Selection Process:** After submitting a resume, students rarely receive diagnostic feedback on whether their profile aligns with the job description or why they failed an initial screening round.
4. **Lack of Stage Visibility:** Students have no unified portal to track the real-time status of multiple simultaneous applications (e.g., Applied vs. Shortlisted vs. Interview Round 1 vs. Offer Extended).

#### B. Recruiter & Alumni Challenges
1. **Resume Overload Without Domain Filtering:** Recruiters receiving hundreds of unstructured resume PDFs must manually parse candidate credentials to verify whether candidates possess mandatory technical competencies.
2. **Inaccurate or Unverified Candidate Claims:** On generic platforms, candidate-submitted CGPAs, branch affiliations, and backlog statuses are self-reported and often unverified by institutional authorities.
3. **Inefficient Shortlisting Workflow:** Recruiters lack automated tooling to rank applicant resumes against specific Job Descriptions (JDs) based on extractable skills and relevant profile attributes.
4. **Alumni Referral Friction:** Alumni who wish to refer students from their alma mater lack a structured channel to post requirements, verify student standing through the placement cell, and evaluate matched candidate profiles.

#### C. TPC / Administrative Staff Challenges
1. **High-Stress Manual Verification:** TPC staff spend substantial hours manually cross-checking student-submitted details against official academic records before sending shortlists to companies.
2. **Policy Enforcement Bottlenecks:** Enforcing university placement policies—such as the "One Student, One Job" rule, CTC upgrade tiers (e.g., Core vs. Dream vs. Super Dream categories), and debarment for unexcused interview absences—is prone to human error when tracked via spreadsheets.
3. **Logistical Scheduling Conflicts:** Coordinating multi-round interviews across multiple parallel companies leads to scheduling overlaps, duplicate candidate allocations, and communication breakdowns.
4. **Reporting & Regulatory Overhead:** Generating institutional reports, branch-wise placement percentages, average/median CTC metrics, and historical placement audits requires laborious manual data compilation.

---

### 1.3 How This Platform Addresses These Problems
The **Campus Placement & Referral Portal with Auto-Resume Matcher** centralizes recruitment operations into a single, policy-governed system:

1. **Single Source of Truth with Verification Locks:** Student academic records (CGPA, department, backlogs) are maintained in verified student profiles that cannot be altered unilaterally by students during active drives without TPC approval.
2. **Automated Gatekeeping & Eligibility Verification:** The platform evaluates job eligibility rules (minimum CGPA, allowed branches, backlog thresholds, and placement tier rules) automatically at the point of application, preventing ineligible submissions.
3. **ATS-Style Resume Parsing & Keyword/Skill Matching:** The system ingests PDF resumes, extracts plain text and candidate skills, and compares them against the job description to calculate an objective match score. This provides recruiters with ranked candidate shortlists and provides students with clear alignment insights.
4. **Structured Application Lifecycle Tracking:** End-to-end pipeline management from initial application to screening, interview rounds, and final offer letters, giving all stakeholders synchronized status visibility.
5. **Integrated Policy Governance:** University placement rules (such as offer limits and dream company upgrades) are programmatically enforced at the application and offer-acceptance stages.
6. **Unified Analytics & Data Export:** Real-time dashboards compute institutional placement KPIs, while structured CSV/Excel export tools allow TPC to fulfill corporate and regulatory compliance reporting needs instantly.

---

### 1.4 What Distinguishes This Platform from a Simple Job Board or Generic CRUD App
This platform is an **institutional placement operating system**, not a public job board or a standard CRUD application:

| Dimension | Generic Job Board (e.g., Indeed, LinkedIn) | Generic CRUD Application | Campus Placement & Referral Portal |
| :--- | :--- | :--- | :--- |
| **Trust Model** | Unverified, self-reported user data | Basic database input/output forms without institutional validation | **Two-Tier Verification:** TPC must verify student academic credentials and approve company drives before applications proceed. |
| **Eligibility Control** | Anyone can click "Apply"; filtering occurs post-submission | Static checks on isolated form fields | **Rule-Based Institutional Engine:** Dynamic evaluation of CGPA cutoffs, backlog allowances, department exclusions, and university tier restrictions (e.g., Dream vs. Regular offer limits). |
| **Resume Processing** | Passive document attachment or basic keyword search | Simple file upload storing a URL | **Domain-Specific ATS Engine:** Text extraction, canonical skill normalization, and deterministic Jaccard similarity scoring against target JDs (advanced weighted scoring deferred to V2). |
| **Lifecycle State Machine** | Binary states (Applied, Viewed, Rejected) | Arbitrary database row updates | **Strict Sequential Workflow:** Managed stage transitions (Eligibility Gate $\rightarrow$ `Applied` $\rightarrow$ `ATS Shortlisted` $\rightarrow$ `Interview Scheduled` $\rightarrow$ `Offer Extended` $\rightarrow$ `Accepted`), with cascading effects (e.g., accepting an offer automatically locks out lower-tier drives). |
| **Stakeholder Triad** | Recruiter $\leftrightarrow$ Candidate | Single user or simple admin $\leftrightarrow$ user | **Tri-Party Governance:** Coordinated interactions between Student, Recruiter/Alumni, and TPC Admin with distinct operational permissions. |
| **Institutional Governance** | None; pure marketplace dynamics | None | **Compliance & Reporting:** Enforces university placement mandates, tracks drive audit logs, and produces regulatory placement analytics. |

---

## 2. User Roles & Permission-Oriented Access Control (RBAC)

The platform enforces a strict, multi-tenant institutional trust model where data visibility and state mutations are governed by three primary roles: **Student**, **Recruiter / Alumni**, and **TPC Admin**.

---

### 2.1 Role: Student

#### A. Purpose
Enables enrolled students to manage their placement profiles, discover eligible campus recruitment drives and alumni referral postings, parse/upload resumes, track their multi-stage application pipeline, view interview schedules, and respond to official job offers in compliance with institutional placement rules.

#### B. Responsibilities
- Maintain an accurate, up-to-date personal, academic, and skills profile.
- Upload valid, uncorrupted PDF resumes formatted for automated parsing.
- Submit applications only to drives for which institutional and company-specific eligibility criteria are met.
- Monitor application status, interview schedules, and provide timely decisions on extended offers.
- Adhere to institutional placement policies, code of conduct, and acceptance deadlines.

#### C. View Permissions (What They Can View)
- **Own Profile:** View all personal, academic, skill, and portfolio attributes, including TPC verification status and lock indicators.
- **Job & Referral Listings:** View all published and active campus placement drives and alumni referral postings, along with full job descriptions, compensation details (CTC breakdowns), eligibility criteria, deadlines, and drive schedules.
- **Eligibility Status:** View real-time, pre-computed eligibility badges (Eligible / Ineligible with exact discrepancy reasons, e.g., "CGPA below 7.5 cutoff" or "Branch not permitted") for every listed job.
- **ATS Match Insights:** View the automated resume match score, matched skill keywords, and missing recommended skills for their own resume relative to specific job descriptions.
- **Own Applications:** View real-time status and stage transitions of their own submitted applications (e.g., Applied, Shortlisted, Interview Round 1/2, Offered, Rejected).
- **Interview Details:** View interview dates, time slots, locations/meeting URLs, and special instructions assigned to them.
- **Extended Offers:** View official offer letters, compensation packages, and decision deadlines extended to their account.

#### D. Create Permissions (What They Can Create)
- **Profile Initialization:** Create initial student profile data (contact details, social/code links, past education history) during onboarding.
- **Resume Uploads:** Upload new PDF resumes and trigger the ATS extraction parser.
- **Job Applications:** Create job application submissions for drives where eligibility criteria are satisfied and placement policy allows.

#### E. Update Permissions (What They Can Update)
- **Personal & Professional Info:** Update contact numbers, portfolio/GitHub/LinkedIn URLs, and self-reported technical skills.
- **Academic Details (Conditional):** Update marks, CGPA, and backlog counts **only while profile status is `Unverified`**. Once marked `Verified` by TPC, academic fields are read-only.
- **Active Resume Selection:** Select which uploaded resume version serves as the default or drive-specific resume.
- **Offer Decisions:** Record an explicit `Accept` or `Decline` decision on extended job offers within the institutional decision window.

#### F. Delete Permissions (What They Can Delete)
- **Uploaded Resumes:** Soft-delete or remove previous resume versions, provided the resume is not attached to an active, locked application in an interview/offer stage.
- **Application Withdrawals (Conditional):** Withdraw an application **only before the application deadline passes** and before shortlisting commences. Cannot delete historical applications.

#### G. Allowed Actions
- Run ATS resume auto-matching against published job descriptions to preview skill gaps before applying.
- Apply to multiple eligible drives concurrently, subject to institutional tier rules (e.g., Dream vs. Regular).
- Accept an offer (which triggers automated profile status changes to `Placed` and auto-withdraws/locks conflicting applications as per university policy).
- Decline an offer with mandatory recorded justification.

#### H. Explicitly Forbidden Actions
- **Cannot** alter academic records (CGPA, branch, graduation year, active backlogs) after TPC verification.
- **Cannot** apply to drives where automated eligibility evaluation flags the candidate as ineligible.
- **Cannot** bypass placement tier restrictions (e.g., apply for a Regular tier job if already holding an accepted Regular tier offer, unless qualifying for an institutional Dream upgrade).
- **Cannot** view other students' profiles, resumes, application statuses, ATS match scores, or offer details.
- **Cannot** view recruiter internal notes, evaluation rubrics, or interview rating scores.
- **Cannot** modify application status or force stage transitions.
- **Cannot** view or export institutional placement analytics or administrative audit logs.

---

### 2.2 Role: Recruiter / Alumni

#### A. Purpose
Enables corporate recruitment partners and alumni referrers to publish hiring opportunities, review applicant pools ranked by automated ATS match scores, manage multi-round selection pipelines, schedule candidate interviews, and issue official employment offers through the placement cell.

#### B. Responsibilities
- Provide comprehensive, accurate job specifications, compensation breakdowns, and clear eligibility criteria.
- Adhere to agreed campus recruitment timelines and drive schedules.
- Process applications objectively using ATS insights and human evaluation.
- Update candidate progression through interview stages in a timely manner.
- Issue formal offer details and adhere to institutional placement communication standards.

#### C. View Permissions (What They Can View)
- **Own Job Postings:** View all job postings and referral drives created under their organization/account, including draft, pending approval, active, and closed drives.
- **Applicant Pools:** View all students who applied to their own job postings, including verified academic summaries, uploaded resume PDFs, extracted skill profiles, and computed ATS match scores.
- **Applicant ATS Breakdown:** View granular match diagnostics for candidates (matched skills, missing skills, and ATS match score/rank computed via deterministic Jaccard similarity).
- **Candidate Pipeline:** View stage-wise applicant distribution across selection rounds (Shortlisted, Technical Test, Interview Round 1, Round 2, HR, Offered, Rejected).
- **Candidate Interview Availability:** View assigned interview slots and candidate attendance/confirmation status.

#### D. Create Permissions (What They Can Create)
- **Job / Referral Postings:** Create new campus placement drive postings or alumni referral openings, specifying role details, eligibility criteria, CTC, and deadlines.
- **Interview Rounds:** Create selection stages (e.g., Aptitude Test, Coding Assessment, Technical Interview 1, HR Interview).
- **Interview Schedules:** Create interview slots with date, time, venue, or virtual meeting links for shortlisted candidates.
- **Candidate Evaluation Notes:** Create internal recruiter feedback, evaluation notes, and rating scores for candidates during interview rounds.
- **Job Offers:** Create and issue employment offers with structured CTC figures and offer letter attachments.

#### E. Update Permissions (What They Can Update)
- **Job Postings (Conditional):** Update job description, requirements, and deadlines **prior to drive commencement**. Post-commencement edits to core eligibility criteria require TPC re-approval.
- **Application Pipeline Status:** Transition applicants between allowed lifecycle stages (`Applied` $\rightarrow$ `Shortlisted` $\rightarrow$ `Interview Round X` $\rightarrow$ `Offered` / `Rejected`).
- **Interview Schedules:** Reschedule or update interview slots and venue/link details prior to scheduled times.
- **Recruiter Profile:** Update recruiter company profile, contact details, and organization description.

#### F. Delete Permissions (What They Can Delete)
- **Draft Postings:** Delete draft job postings that have not been submitted for TPC approval or published to students.
- **Interview Slots:** Cancel scheduled interview slots (with automated candidate notification).
- **Cannot** permanently delete applicant records, submitted resumes, or historical application audit trails.

#### G. Allowed Actions
- Filter and rank applicant lists using ATS match score thresholds, CGPA cutoffs, and specific skill tags.
- Batch-shortlist or batch-reject candidates at specific pipeline stages.
- Export candidate rosters and resume bundles for their own active drives.
- Log private internal evaluation ratings visible only to recruiter team members and TPC.

#### H. Explicitly Forbidden Actions
- **Cannot** publish a campus drive directly to students without TPC Admin review and approval (for on-campus corporate drives).
- **Cannot** view students who have not applied to their specific job postings (no unrestricted access to the institution's student directory).
- **Cannot** view student applications, resumes, or interview notes associated with other companies/recruiters.
- **Cannot** alter student profile details, academic records, or TPC verification statuses.
- **Cannot** bypass university placement rules (e.g., extend an under-the-table offer or pressure a student who is restricted by university policy).
- **Cannot** access system-wide institutional placement reports or administrative configuration settings.

---

### 2.3 Role: TPC Admin (Training & Placement Cell Administrator)

#### A. Purpose
Serves as the supreme institutional authority and operational steward of the placement ecosystem. Governs student academic verification, approves and regulates corporate/alumni drives, oversees multi-company interview logistics, enforces university placement policies, resolves disputes, and generates institutional placement audits and reports.

#### B. Responsibilities
- Verify and lock student academic records against official university registrar data.
- Review and approve corporate job descriptions, compensation claims, and eligibility criteria before release to students.
- Enforce institutional placement mandates (e.g., "One Student, One Job", tier upgrade thresholds, debarment policies).
- Monitor fair recruitment practices across all parallel drives.
- Produce accurate institutional placement analytics for university accreditation (e.g., NIRF, NAAC) and corporate relations.

#### C. View Permissions (What They Can View)
- **Universal Directory:** View all student profiles, verification statuses, academic records, backlogs, and uploaded resumes.
- **All Drives & Postings:** View all job postings (campus drives and alumni referrals) across all companies, including draft, pending approval, active, and archived states.
- **Global Application Pipelines:** View all applications across all students and companies, with complete stage-change audit histories.
- **ATS Engine Logs:** View ATS match scores, extraction logs, and algorithmic scoring configurations.
- **System Audits & Overrides:** View all administrative actions, profile lock changes, policy override logs, and user login activity.
- **Institutional Analytics:** View university-wide dashboards including branch-wise placement percentages, median/average/highest CTC, company-wise recruit counts, and gender diversity statistics.

#### D. Create Permissions (What They Can Create)
- **TPC User Accounts:** Create and provision institutional coordinator and admin accounts.
- **Direct Job Drives:** Create campus placement drives directly on behalf of invited corporate partners.
- **Placement Policy Rules:** Create and define institutional placement rules (e.g., CTC tier brackets: Core, Dream, Super Dream; minimum attendance thresholds).
- **Manual Overrides & Annotations:** Create administrative exception notes and disciplinary debarment records.
- **Institutional Data Exports:** Generate customized CSV/Excel exports for administration, accreditation bodies, and corporate partners.

#### E. Update Permissions (What They Can Update)
- **Student Verification State:** Transition student profiles between `Unverified`, `Verified`, and `Suspended/Debarred`, locking or unlocking academic records.
- **Academic Record Corrections:** Edit or correct student CGPA, backlogs, and branch records to match official registrar records.
- **Job Posting Status:** Approve, reject, modify, pause, or archive job postings submitted by recruiters or alumni.
- **Application Status Overrides:** Manually advance, roll back, or cancel application stages in exceptional circumstances (with mandatory audit reason logging).
- **Institutional Placement Settings:** Update drive deadlines, tier rules, and system-wide operational parameters.

#### F. Delete Permissions (What They Can Delete)
- **Spam / Invalid Postings:** Soft-delete fraudulent, duplicate, or unapproved job postings.
- **Revoked Documents:** Remove invalid or policy-violating resume uploads upon student/institutional request.
- **Cannot** hard-delete placed student records or completed drive audit histories (preserves regulatory audit integrity).

#### G. Allowed Actions
- Bulk-verify student academic data via CSV import or batch selection.
- Enforce automated or manual debarment of students who violate institutional placement conduct.
- Override eligibility blocks or policy locks with explicit, logged administrative justification.
- Download complete candidate datasets, resume archives, and drive summary packages.

#### H. Explicitly Forbidden Actions
- **Cannot** submit job applications or accept/decline offers on behalf of students without logged emergency delegation.
- **Cannot** alter recruiter-submitted evaluation notes or tamper with recruiter interview ratings.
- **Cannot** bypass mandatory audit logging when performing manual overrides or academic record corrections.

---

### 2.4 Comprehensive Role-Resource Permission Matrix

The following matrix provides an exhaustive, granular definition of permissions across all platform resources for each user role (**Student**, **Recruiter / Alumni**, and **TPC Admin**). For every resource-role combination, rights are strictly categorized into **CREATE**, **READ**, **UPDATE**, **DELETE**, and **SPECIAL ACTION**.

| Resource | Role | CREATE | READ | UPDATE | DELETE | SPECIAL ACTION |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Own Profile** | **Student** | Yes (at registration) | Yes (full profile) | Yes (personal, links, skills; academic fields only if `Unverified`) | No | Request TPC re-verification upon semester grade updates |
| | **Recruiter** | Yes (at registration) | Yes (own profile) | Yes (company description, contact, logo) | No | Invite / provision recruiter team members |
| | **TPC Admin** | Yes (admin provisioning) | Yes (own profile) | Yes (admin credentials, contact info) | No | Transfer institutional primary admin ownership |
| **Other User Profiles** | **Student** | No | No (strictly isolated) | No | No | None |
| | **Recruiter** | No | Conditional (view profile summary of applicants to own jobs only) | No | No | None |
| | **TPC Admin** | Yes (manual user provisioning) | Yes (all users across all roles) | Yes (account status, role assignment) | Yes (soft-delete inactive/fraudulent accounts) | Suspend account, force password reset, debar from placement |
| **Resume** | **Student** | Yes (upload PDF) | Yes (own uploaded documents) | Yes (select active resume, replace prior to application) | Yes (delete unlinked historical versions) | Trigger ATS text extraction and skill preview |
| | **Recruiter** | No | Yes (view/download resumes of applicants to own jobs) | No | No | Bulk-download candidate resumes as ZIP package |
| | **TPC Admin** | Yes (upload on behalf in exceptional cases) | Yes (all student resumes across the institution) | Yes (flag or update document verification tag) | Yes (purge policy-violating or corrupted files) | Bulk-export resume bundles for accreditation audits |
| **Jobs** | **Student** | No | Yes (view approved, `ACTIVE` campus drives and referral postings; `DRAFT`, `PENDING_APPROVAL`, rejected, archived, or closed drives are strictly hidden) | No | No | Filter by pre-computed eligibility status; search by skills |
| | **Recruiter** | Yes (create campus drives or referral postings with initial status `PENDING_APPROVAL`) | Yes (own job postings in all states) | Yes (edit details prior to drive launch; criteria edits post-launch require TPC re-approval) | Yes (delete unsubmitted drafts only) | Close application window early, archive completed drive |
| | **TPC Admin** | Yes (create drives directly for visiting companies) | Yes (universal view of all jobs across all companies and states) | Yes (modify any drive, adjust deadlines, edit criteria) | Yes (archive or soft-delete cancelled/fraudulent drives) | Approve / Reject recruiter submissions, feature drive on student board |
| **Applications** | **Student** | Yes (submit application if eligible) | Yes (view status of own submitted applications) | Conditional (withdraw prior to deadline only) | No | Real-time stage progression tracking |
| | **Recruiter** | No | Yes (view all applications submitted to own jobs) | Yes (transition candidate pipeline stage: Shortlist, Reject, etc.) | No | Batch-shortlist, batch-reject, add private interviewer evaluation notes |
| | **TPC Admin** | Yes (manual application entry for exceptional cases) | Yes (universal view of all applications across all drives) | Yes (override candidate stage, cancel invalid application) | No (soft-flag only; preserve audit history) | Override automated eligibility rejection with logged audit reason |
| **ATS Results** | **Student** | No (automated computation) | Conditional (view own match score, matched skills, missing skills for a job) | No | No | Run pre-application match diagnostic to preview alignment |
| | **Recruiter** | No (automated computation) | Yes (view ranked applicant list and score breakdown for own jobs) | No (algorithmic calculation) | No | Filter and sort candidates by ATS match score threshold (e.g., $\ge$ 75%) |
| | **TPC Admin** | No (automated computation) | Yes (view ATS match scores and breakdown across all drives) | No | No | Audit extraction logs and review scoring discrepancies |
| **Interviews** | **Student** | No | Yes (view own scheduled interview slots, venue, link, instructions) | No | No | Confirm attendance or submit reschedule request to TPC |
| | **Recruiter** | Yes (create interview rounds and time slots) | Yes (view own scheduled interviews and candidate rosters) | Yes (reschedule slots, update meeting links and venues) | Yes (cancel unconducted interview slots) | Record candidate round scorecards and recommend next round |
| | **TPC Admin** | Yes (schedule centralized / pooled interview rounds) | Yes (view all scheduled interviews across all companies) | Yes (reassign slots, update centralized venues) | Yes (cancel clashing interview slots) | Detect and resolve schedule collisions across competing company drives |
| **Offers** | **Student** | No | Yes (view official offers extended to self) | Yes (record formal Accept or Decline decision) | No | Accepting an offer triggers automated placed lock and application auto-withdrawals |
| | **Recruiter** | Yes (issue offer with CTC breakdown and offer letter) | Yes (view status of extended offers for own jobs) | Yes (modify offer terms prior to candidate decision) | Conditional (revoke unaccepted offer with written reason) | Set offer acceptance deadline |
| | **TPC Admin** | Yes (log off-campus or direct placement offers) | Yes (view all extended, accepted, and declined offers institution-wide) | Yes (verify offer terms, approve offer release) | No | Enforce "One Student, One Job" or Dream Tier policy locks; nullify invalid offers |
| **Students** (Directory & Records) | **Student** | No | No (cannot browse peers) | No | No | None |
| | **Recruiter** | No | Conditional (view academic summaries of own applicants only) | No | No | None |
| | **TPC Admin** | Yes (enroll students, bulk-import batches via CSV) | Yes (full institutional student directory and historical cohorts) | Yes (verify academic records, correct CGPA/backlogs, update batch info) | Yes (archive graduated cohorts) | Lock verified academic records; apply disciplinary debarment |
| **Analytics** | **Student** | No | No (no aggregate institutional access) | No | No | View personal placement summary statistics only (applied, shortlisted, offers) |
| | **Recruiter** | No | Yes (drive-level recruitment funnel analytics for own postings) | No | No | View candidate conversion and drop-off rates across interview rounds |
| | **TPC Admin** | No (automated computation) | Yes (full institutional dashboards: placement %, CTC distributions, branch-wise metrics) | No | No | Generate NIRF, NAAC, and corporate accreditation compliance statistics |
| **Exports** | **Student** | No | Conditional (download own profile / application summary PDF) | No | No | Download verified institutional placement pass |
| | **Recruiter** | Yes (generate candidate shortlist export) | Yes (download CSV of applicants/shortlists for own jobs) | No | No | Export bundled candidate resume archive (.zip) |
| | **TPC Admin** | Yes (generate universal reports) | Yes (download any generated export) | No | Yes (purge expired export download links) | Custom cross-table Excel/CSV generation (eligible rosters, placed lists, salary reports) |
| **System Settings** | **Student** | No | No | No | No | None |
| | **Recruiter** | No | No | No | No | None |
| | **TPC Admin** | Yes (define placement rules, branch mappings, academic tiers) | Yes (view all institutional configurations and policies) | Yes (modify policy parameters, tier brackets, application grace periods) | Yes (deprecate obsolete tags/categories) | Toggle institutional placement season active/closed; enable maintenance mode |

---

## 3. Student Functional Requirements

This section specifies the 14 core functional requirements for the **Student** persona. Every feature is specified with operational preconditions, primary workflows, alternative pathways, exception failure cases, expected results, and testable acceptance criteria.

---

### 3.1 Student Registration

#### A. Description
Enables an enrolled student to create a personal account on the placement portal using their official institutional email address, establishing their identity within the college placement ecosystem.

#### B. Preconditions
- The student is currently enrolled in an active degree program at the institution.
- The student possesses an active, valid institutional email address (e.g., `@college.edu`).
- The student does not already have an existing registered account associated with the provided institutional email or university roll number.

#### C. Main Flow
1. The student navigates to the registration page and selects the "Student Registration" option.
2. The system prompts the student to enter their full name, official institutional email, university roll number / registration ID, degree program, department/branch, graduation year, and a secure password.
3. The student enters all mandatory attributes and submits the registration form.
4. The system validates that the email domain matches the institutional domain whitelist and that the roll number has not been previously registered.
5. The system hashes the password, creates the student record in an `Unverified` state, and dispatches an email verification link containing a secure, time-limited token.
6. The student accesses their email, clicks the verification link, and confirms their account.
7. The system updates the account status to `Active (Unverified Profile)` and redirects the student to the onboarding profile setup wizard.

#### D. Alternative Flow
- **Pre-Provisioned Roll Number Match:** If TPC Admin has already pre-imported the student batch roster via CSV, the system cross-references the entered roll number, matches the student's branch and name automatically, links the account to the pre-seeded record, and confirms basic enrollment.

#### E. Failure Cases
- **Invalid Domain:** The student submits a non-institutional email (e.g., personal `@gmail.com`). System rejects submission with: *"Registration requires a valid university email address."*
- **Duplicate Registration:** The institutional email or university roll number already exists in the database. System halts registration with: *"An account with this email or roll number is already registered."*
- **Weak Password:** Password fails complexity rules (less than 8 characters, missing alphanumeric or symbol requirements). System flags validation errors inline.
- **Verification Token Expired:** The student clicks the email link after the token expiration window (e.g., 24 hours). System prompts: *"Verification link has expired. Request a new verification link."*

#### F. Expected Result
A new student account is persisted in the database with hashed credentials, email verified, and initial status set to `Active (Unverified Profile)`.

#### G. Acceptance Criteria
- System rejects any email domain not explicitly whitelisted for students.
- System strictly enforces uniqueness on both email and student roll number.
- Unverified accounts cannot apply to campus recruitment drives until email verification is completed.

---

### 3.2 Student Authentication (Login) & Session Management

#### A. Description
Enables registered students to authenticate securely, establish a protected user session, and access student-facing portal functionalities based on their verification status.

#### B. Preconditions
- The student has completed email registration.
- The student account is not in a `Suspended` or `Debarred` state by TPC Admin.

#### C. Main Flow
1. The student accesses the login page and selects the "Student" role portal.
2. The student inputs their registered institutional email and password.
3. The system validates the credentials against stored secure password hashes.
4. The system checks account standing (verifies the account is not suspended or debarred in the database).
5. The system issues a secure session token (HTTP-only cookie or JWT) containing user identity (`sub`), role (`STUDENT`), and standard token metadata. The database remains the authoritative source for mutable critical state (such as verification, placement, and debarment status).
6. The system redirects the student to the Student Dashboard.

#### D. Alternative Flow
- **Password Reset:** If the student forgets their password, they click "Forgot Password", enter their registered institutional email, receive a secure time-limited reset link, and reset their password.

#### E. Failure Cases
- **Incorrect Credentials:** The entered email/password combination does not match. System returns generic error: *"Invalid email or password."* (prevents user enumeration).
- **Suspended / Debarred Account:** Credentials are valid, but the account is marked `Suspended` or `Debarred` by TPC. System halts login and displays: *"Your placement account has been suspended by TPC Admin. Contact the placement office."*
- **Unverified Email:** Account credentials are correct, but the email verification link was never confirmed. System halts login and offers to resend the verification link.

#### F. Expected Result
The student is authenticated, a role-governed session is initialized, and the student is routed to their personal dashboard displaying their profile status and active drive alerts.

#### G. Acceptance Criteria
- Passwords are never returned in network payloads or session payloads.
- Rate-limiting prevents more than 5 consecutive failed attempts per IP/account within a 15-minute window.
- Debarred students are denied entry with explicit administrative contact guidance.

---

### 3.3 Student Profile Management (Personal & Contact Details)

#### A. Description
Allows students to view, complete, and update personal, biographical, and professional contact information that recruiters will access during campus hiring.

#### B. Preconditions
- The student is authenticated with an active session.

#### C. Main Flow
1. The student navigates to the "Profile" tab from the dashboard.
2. The system displays current profile fields: Full Name, Contact Phone Number, Alternate Email, Gender, Permanent Address, Current Address, LinkedIn Profile URL, GitHub Profile URL, and Portfolio / Personal Website URL.
3. The student edits or fills out their contact numbers and social/professional links.
4. The student clicks "Save Profile".
5. The system sanitizes input, validates phone number formatting (e.g., 10 digits) and URL schemes (`https://`), and updates the student record.
6. A success confirmation is displayed: *"Profile details updated successfully."*

#### D. Alternative Flow
- **Profile Completion Prompt:** If the student logs in for the first time, a persistent banner alerts them: *"Your profile is incomplete. Complete your contact and academic details to unlock job applications."*

#### E. Failure Cases
- **Malformed URL:** The student submits an invalid URL format in portfolio/social fields (e.g., `htp:/linkedin`). System displays: *"Please enter a valid URL beginning with https://"*
- **Invalid Phone Number:** Contact number contains non-numeric characters or incorrect digit length. System flags inline validation error.
- **Empty Required Fields:** Primary contact phone number is cleared. System blocks save action with: *"Primary contact number is mandatory."*

#### F. Expected Result
Personal and professional contact attributes are stored in the database and immediately reflected across the student's dashboard and profile view.

#### G. Acceptance Criteria
- URLs are strictly validated and normalized with HTTPS protocol.
- Changes to personal contact information do not invalidate existing academic verifications.

---

### 3.4 Academic Information & Verification Management

#### A. Description
Enables students to record their formal academic metrics—including 10th standard percentage, 12th / Diploma percentage, current degree CGPA, active backlog count, and total backlog history—and tracks the institutional verification status set by TPC Admin.

#### B. Preconditions
- The student is authenticated.
- Profile status is either `Unverified` or explicit re-verification has been requested.

#### C. Main Flow
1. The student navigates to the "Academic Details" section of their profile.
2. The student inputs:
   - 10th Grade Percentage / Board / Year of Passing
   - 12th Grade / Diploma Percentage / Board / Year of Passing
   - Current Undergraduate/Postgraduate Degree & Branch
   - Current Cumulative CGPA (scale of 10.00)
   - Number of Active (Current Standing) Backlogs
   - Number of Historical (Cleared) Backlogs
3. The student clicks "Save & Request Verification".
4. The system validates numeric boundaries (CGPA between 0.00 and 10.00; percentages between 0.00% and 100.00%; backlog counts $\ge 0$).
5. The system persists the academic details, marks profile verification status as `Pending TPC Verification`, and locks the fields from further self-service editing.
6. An institutional notification is queued for TPC Admin to review and verify the academic claim.

#### D. Alternative Flow
- **Locked State (Verified by TPC):** When the student views this section after TPC Admin has verified their records against official university transcripts, all academic fields are rendered in a **Read-Only / Locked** state with a green `Verified` badge.
- **Academic Re-verification Request:** If a semester result is declared and the student's CGPA changes, the student clicks "Request Academic Update", uploads official mark sheet proof, and enters updated CGPA. The previous verified record remains in effect until TPC Admin reviews and approves the update.

#### E. Failure Cases
- **Out-of-Range Metrics:** Student enters a CGPA of 11.2 or -1.5. System rejects with: *"CGPA must be a valid number between 0.00 and 10.00."*
- **Attempting Unauthorized Edits on Verified Record:** Student attempts to modify academic values when status is `Verified`. The system blocks the request and returns: *"Academic records are locked following TPC verification. Submit a re-verification request to update grades."*
- **Negative Backlogs:** Student inputs negative numbers for backlogs. System rejects input.

#### F. Expected Result
Academic metrics are accurately captured. Once verified by TPC Admin, academic records are immutable to the student, guaranteeing high data integrity for corporate eligibility evaluations.

#### G. Acceptance Criteria
- Verified academic fields cannot be altered via any client-side request or direct form submission.
- Eligibility engine must strictly utilize verified academic metrics when computing job eligibility.

---

### 3.5 Technical & Professional Skills Management

#### A. Description
Enables students to maintain a structured inventory of technical competencies, programming languages, frameworks, developer tools, and domain specializations used by the ATS matching engine.

#### B. Preconditions
- The student is authenticated.

#### C. Main Flow
1. The student navigates to the "Skills" section of their profile.
2. The system displays a searchable input field supported by a standard technical skill taxonomy (e.g., Python, Java, React, Node.js, PostgreSQL, Docker, AWS, Machine Learning).
3. The student selects skills from taxonomy recommendations or adds specific validated tags.
4. For each added skill, the student optionally specifies an experience/proficiency level (Beginner, Intermediate, Advanced).
5. The student saves the skill list.
6. The system persists the skills array and indexes them for candidate search and ATS alignment.

#### D. Alternative Flow
- **Auto-Populate from Resume:** When the student uploads their PDF resume (Section 3.6), the ATS extraction engine detects skills within the document and prompts the student: *"We extracted 8 skills from your resume. Would you like to add them to your profile skills?"* The student reviews and accepts with one click.

#### E. Failure Cases
- **Duplicate Skills:** The student attempts to add "Python" when it is already in their skill list. System prevents duplicates.
- **Excessive Skill Injection:** Student attempts to input spam tags exceeding platform limits (e.g., more than 50 skills). System limits skill tags to a maximum of 50.
- **Empty / White-Space Tags:** Submission of blank or special-character-only tags is rejected.

#### F. Expected Result
The student profile maintains a clean, normalized list of skills that directly contributes to the resume matching and ranking algorithms.

#### G. Acceptance Criteria
- Skills are normalized (e.g., "react.js", "ReactJS", and "React" map to the canonical skill "React").
- Students can add or remove profile skills at any time without requiring TPC administrative lock overrides.

---

### 3.6 Resume PDF Upload & Text Parsing Pipeline

#### A. Description
Enables students to upload their resume in PDF format, stores the document securely in cloud storage, extracts the raw text content from the file, and runs initial skill detection.

#### B. Preconditions
- The student is authenticated.
- The student has an active account.

#### C. Main Flow
1. The student navigates to the "Resume Management" section.
2. The student selects a resume file from their local device and clicks "Upload Resume".
3. The client validates file extension (`.pdf`) and file size ($\le 5\text{ MB}$).
4. The system uploads the PDF to secure cloud storage (Cloudinary) and receives a persistent document reference URL.
5. The system initiates the server-side text extraction pipeline, reading all text blocks, headings, and character streams from the PDF.
6. The system extracts the raw text, identifies detected skills against the skills dictionary, and stores both the document URL and extracted text in the database.
7. The student receives a confirmation: *"Resume uploaded and parsed successfully. 12 skills identified."*

#### D. Alternative Flow
- **Multi-Version Resume Management:** The student uploads an updated resume. The system displays previously uploaded versions with upload timestamps and allows the student to designate which resume is the "Default Active Resume" for applications.

#### E. Failure Cases
- **Invalid File Type:** The student uploads a `.docx`, `.png`, or `.txt` file. System halts upload with: *"Only PDF documents (.pdf) are permitted."*
- **File Size Exceeded:** The student uploads a file larger than 5 MB. System halts upload with: *"File size exceeds maximum allowable limit of 5 MB."*
- **Corrupted / Unreadable PDF:** The PDF is corrupted, password-protected, or DRM-encrypted. System extraction fails and alerts: *"Unable to parse text from the uploaded PDF. Please ensure the document is not password-protected or scanned as a flat image."*
- **Scanned Image-Only PDF (Zero Text):** The document contains only rasterized images without an embedded text layer. System issues warning: *"No readable text detected. Upload a text-based PDF to enable ATS resume matching."*

#### F. Expected Result
The resume PDF is safely stored in cloud storage, and its extracted text is indexed in the database, ready to be compared against job descriptions.

#### G. Acceptance Criteria
- Only valid PDF MIME types (`application/pdf`) are accepted.
- Uploaded resumes cannot be accessed publicly without authenticated user authorization.
- Extracted text must preserve alphanumeric skill keywords, education titles, and work experience descriptions.

---

### 3.7 Available Job & Referral Drive Discovery

#### A. Description
Provides students with a centralized, filterable feed of all approved and published campus placement drives and alumni referral postings.

#### B. Preconditions
- The student is authenticated.
- The student account is in an active, non-debarred standing.

#### C. Main Flow
1. The student clicks the "Jobs & Drives" tab.
2. The system queries all job postings that satisfy:
   - Status is `Active` and approved by TPC Admin.
   - Application deadline has not elapsed (`deadline > current_time`).
3. The system renders the job feed as structured cards displaying: Company Name, Company Logo, Job Title, Drive Type (`On-Campus Drive` vs. `Alumni Referral`), Placement Tier (`Regular`, `Dream`, `Super Dream`), Offered CTC / Stipend, Job Location, Application Deadline, and an immediate **Eligibility Status Badge**.
4. The student uses search and filter controls to refine opportunities by: Minimum CTC, Drive Type, Placement Category, or Required Skills.
5. The student clicks a job card to view the complete Job Description, including role expectations, hiring process rounds, bonding agreements, and detailed eligibility criteria.

#### D. Alternative Flow
- **Referral Posts vs. Campus Drives:** If the listing is an Alumni Referral, the card displays an "Alumni Referral" tag along with the alumni referrer's batch/branch and organization, distinguishing it from formal institutional recruitment drives.

#### E. Failure Cases
- **Expired Postings:** A drive passes its deadline while the student is browsing. If the student attempts to view details, the system flags: *"This drive is no longer accepting applications."*
- **Unapproved / Draft Postings:** Drives submitted by recruiters but not yet approved by TPC Admin are strictly omitted from the student feed.

#### F. Expected Result
Students gain complete, real-time visibility into all legitimate hiring opportunities authorized by the institution.

#### G. Acceptance Criteria
- Closed, archived, or pending-approval drives must never appear in the active student job feed.
- Every displayed job card must calculate and render the student's eligibility status in real time.

---

### 3.8 Placement Eligibility Check & Validation Engine

#### A. Description
Automatically evaluates the student's verified academic and institutional profile against the strict gatekeeping criteria established for a specific job drive, providing transparent eligibility feedback.

#### B. Preconditions
- The student is authenticated.
- A published job posting exists.

#### C. Main Flow
1. The student views a specific job drive details page.
2. The eligibility engine evaluates the student's verified profile across five core gatekeeping parameters:
   - **CGPA Cutoff:** Student's verified CGPA $\ge$ Job Minimum CGPA.
   - **Active Backlogs:** Student's active backlogs $\le$ Job Maximum Allowed Backlogs.
   - **Allowed Branches / Departments:** Student's branch $\in$ Job Eligible Branch List.
   - **Graduation Cohort:** Student's graduation year matches target batch.
   - **Placement Policy Constraints:** Student's current placement status allows applying to this tier (e.g., student not already placed in a higher/equal tier under the "One Student, One Job" policy).
3. If all conditions evaluate to `true`, the system displays a prominent green badge: `Eligible to Apply`, and enables the "Apply Now" button.

#### D. Alternative Flow (Ineligible Candidate)
- If one or more criteria fail, the system renders an amber/red badge: `Not Eligible to Apply`, disables the "Apply Now" action button, and outputs an explicit bulleted explanation:
  - *e.g., "Reason: Verified CGPA (7.20) is below the minimum required cutoff (7.50)."*
  - *e.g., "Reason: Mechanical Engineering is not included in the eligible branches for this role."*
  - *e.g., "Reason: You already hold an accepted offer in the Dream tier."*

#### E. Failure Cases
- **Unverified Academic Profile:** Student attempts to check eligibility before TPC Admin has verified their academic profile. System displays: *"Eligibility cannot be determined. Your academic profile is currently pending TPC verification."* The application button remains locked.
- **Disciplinary Debarment:** Student is marked as `Debarred` by TPC. System displays: *"You are debarred from campus placement drives for the current academic session."*

#### F. Expected Result
Students receive instant, objective, and mathematically deterministic eligibility feedback, eliminating ineligible applications at the source.

#### G. Acceptance Criteria
- The eligibility check must execute server-side before allowing any application persistence (cannot be bypassed by modifying client state).
- The system must display the exact reason(s) for ineligibility.

---

### 3.9 Job Application Submission

#### A. Description
Enables an eligible student to submit a formal application to an active job drive or referral posting, linking their verified academic profile and selected resume to the recruiter's applicant pool.

#### B. Preconditions
- The student is authenticated.
- The student's academic profile is `Verified` by TPC Admin.
- The eligibility engine has evaluated the student as `Eligible`.
- The drive application deadline has not passed.
- The student has at least one active, parsed resume on file.
- The student has not already applied to this job posting.

#### C. Main Flow
1. The student clicks "Apply Now" on an eligible job page.
2. A modal dialog opens displaying:
   - Summary of verified academic credentials being submitted (CGPA, Branch, Backlogs).
   - Dropdown selector to choose the active resume version.
   - Mandatory acknowledgment checkbox confirming adherence to the university placement code of conduct.
3. The student selects their desired resume and checks the acknowledgment box.
4. The student clicks "Confirm & Submit Application".
5. The system performs a final server-side atomic validation (re-verifying eligibility, quota, and deadline).
6. The system creates an application record with initial status `Applied`, associates the selected resume snapshot, and executes the background ATS matching pipeline.
7. The student receives a confirmation message: *"Your application has been successfully submitted."*
8. The job card dynamically updates its action button to `Applied (View Status)`.

#### D. Alternative Flow (Withdrawal Prior to Deadline)
- If the recruiter allows application withdrawals and the drive deadline has not closed, the student can click "Withdraw Application". The system prompts for confirmation, updates application state to `Withdrawn`, and frees up the student's concurrent application limit if applicable.

#### E. Failure Cases
- **Deadline Passed During Submission:** The student opens the modal at 11:59 PM and clicks submit at 12:01 AM after the deadline has closed. System rejects submission with: *"Application deadline has passed. Submissions are closed."*
- **Duplicate Submission Attempt:** The student submits concurrent requests. The unique database constraint on `(student_id, job_id)` prevents duplicates and returns: *"You have already applied for this position."*
- **Policy Violation Post-Verification:** Student accepted an offer from another company in the intervening period. System halts application with: *"Placement policy restriction: You have already accepted an offer."*

#### F. Expected Result
A persistent application record is logged, linking the student, company drive, selected resume, and timestamp, with status initialized to `Applied`.

#### G. Acceptance Criteria
- Double submissions to the same job posting are strictly blocked.
- Applications submitted after the timestamp deadline are unconditionally rejected.
- A snapshot of the candidate's verified academic metrics at the moment of application is permanently recorded.

---

### 3.10 ATS Match Score & Diagnostics Preview

#### A. Description
Calculates and presents an ATS-style compatibility score between the student's uploaded resume text and the job description, highlighting keyword alignment, matched technical skills, and identified skill gaps.

#### B. Preconditions
- The student has uploaded a readable, parsed PDF resume.
- The target job posting has a published job description and extracted target skill keywords.

#### C. Main Flow
1. The student navigates to a job details page and clicks "Check Resume Compatibility / ATS Match".
2. The system retrieves the student's active resume text and the job description's required/preferred skills.
3. The ATS matching engine executes:
   - Plain-text normalization and skill keyword extraction from the active resume.
   - Set-based comparison between candidate resume skills and job required skills.
   - Computation of deterministic Jaccard similarity percentage ($0\%$ to $100\%$) based on normalized skill sets (fuzzy matching, frequency weighting, and semantic scoring are classified as V2 / Future Scope).
4. The system presents an interactive ATS diagnostic card showing:
   - **Overall Match Score:** (e.g., $82\%$ Match - High Alignment).
   - **Matched Skills Badge List:** (e.g., `Python`, `PostgreSQL`, `REST APIs`, `Git`).
   - **Missing / Recommended Skills List:** (e.g., `Docker`, `Redis`, `Tailwind CSS`).
5. The student reviews the insights to understand profile strengths or areas for self-development before interviews.

#### D. Alternative Flow (Post-Application Recruiter Ranking View)
- When the student submits an application, this computed score is stored on the application record, enabling the recruiter to sort and filter applicant shortlists by ATS match rank.

#### E. Failure Cases
- **Unparsed / Image Resume:** Student uploaded a scanned image PDF with no extracted text layer. System returns: *"ATS matching unavailable: No text content could be extracted from your resume. Upload a standard text-based PDF."*
- **Job Description Lacks Defined Keywords:** The job posting contains an empty or unparsed description. System defaults to a baseline notification: *"ATS score unavailable for this drive."*

#### F. Expected Result
The student receives a transparent, quantifiable diagnostic breakdown explaining how well their resume matches the technical requirements of the role.

#### G. Acceptance Criteria
- Matching algorithm is deterministic (the same resume against the same JD produces identical scores).
- Missing skill recommendations are directly derived from the job's stated requirements.

---

### 3.11 Application Lifecycle & Pipeline Tracking

#### A. Description
Provides students with a dedicated tracking dashboard to monitor the status and stage-by-stage progression of all submitted campus placement and referral applications in real time.

#### B. Preconditions
- The student is authenticated.
- The student has submitted at least one job application.

#### C. Main Flow
1. The student navigates to the "My Applications" dashboard.
2. The system retrieves all applications tied to the student's account.
3. Each application is displayed with: Company Name, Job Title, Applied Date, Current Stage Badge, and a linear visual progress tracker.
4. The lifecycle states displayed adhere strictly to the institutional pipeline sequence (preceded by the pre-application Eligibility Gate `ELIGIBLE`):
   - `APPLIED` $\rightarrow$ `ATS_SHORTLISTED` $\rightarrow$ `INTERVIEW_SCHEDULED` (or Assessment/Interview Rounds) $\rightarrow$ `OFFER_EXTENDED` $\rightarrow$ `ACCEPTED` (with terminal states `REJECTED`, `WITHDRAWN`, `DECLINED`, `AUTO_WITHDRAWN`).
5. The student clicks on an individual application card to view stage history, status timestamp logs, and any official notes published by TPC or the recruiter.

#### D. Alternative Flow (Application Inactivity)
- If an application remains under review, the card indicates: `In Review - Awaiting Recruiter Shortlisting`.

#### E. Failure Cases
- **Unauthorized Application Access:** A student attempts to query or view the application tracking details of another student by manipulating the URL application ID. System returns HTTP 403 Forbidden with: *"Access Denied: You do not have permission to view this application."*

#### F. Expected Result
Students have end-to-end, transparent visibility into their recruitment pipeline across multiple companies without needing to check fragmented spreadsheets or messaging groups.

#### G. Acceptance Criteria
- State changes executed by Recruiters or TPC Admins must be reflected on the student tracking view immediately upon page refresh.
- Historical stage logs must show dates and times of progression.

---

### 3.12 Interview Scheduling & Information Management

#### A. Description
Enables shortlisted candidates to view scheduled interview rounds, assigned time slots, interview locations or virtual meeting URLs, and special preparation instructions provided by recruiters.

#### B. Preconditions
- The student has an application in the `Shortlisted` or `Interview` stage.
- The recruiter has scheduled an interview round and assigned a slot to the student.

#### C. Main Flow
1. The student receives an in-app alert that an interview has been scheduled.
2. The student navigates to the "Interviews" section of their dashboard.
3. The system displays upcoming interview cards containing:
   - Company & Job Title
   - Round Name (e.g., "Round 1: Technical Interview", "HR Round")
   - Date, Start Time, and Duration
   - Mode & Location: Physical Room / Campus Lab OR Virtual Video Conference URL
   - Instructions / Required Materials (e.g., "Bring 2 hard copies of resume and college ID card")
4. The student clicks "Confirm Attendance" to acknowledge the slot.
5. The system records candidate confirmation for the recruiter.

#### D. Alternative Flow (Emergency Reschedule Request)
- If the student has an unavoidable academic conflict (e.g., university semester exam clash), they click "Request Reschedule", input mandatory written justification, and submit the request. The request is routed to TPC Admin for review and recruiter coordination.

#### E. Failure Cases
- **Unconfirmed Slots / No-Show Risk:** If a student does not confirm attendance prior to the cutoff threshold, the system displays persistent high-priority warnings on the dashboard.
- **Accessing Cancelled Interview:** If a recruiter cancels an interview round, the student's card updates to `Cancelled` with the cancellation reason displayed.

#### F. Expected Result
The student is fully informed of when and where their interview takes place and can confirm participation, eliminating scheduling ambiguity.

#### G. Acceptance Criteria
- Virtual meeting links and room locations are only visible to the specific candidate assigned to the slot.
- Schedule conflicts between two parallel company drives trigger an automated alert to both the student and TPC Admin.

---

### 3.13 Offer Evaluation, Decision & Placement Policy Locking

#### A. Description
Enables a selected student to review an extended formal job offer, examine compensation details and offer letters, and execute a binding `Accept` or `Decline` decision governed by university placement policies.

#### B. Preconditions
- A recruiter has issued an offer to the student, and the offer has been validated by TPC Admin.
- The offer decision window has not expired.

#### C. Main Flow
1. The student navigates to the "Offers" tab upon receiving an offer notification.
2. The system displays the offer details:
   - Company Name & Designating Role
   - Compensation Breakdown: Gross Annual CTC, Fixed Salary, Variable Component, Joining Bonus, and Stipend (if internship-linked)
   - Joining Date and Location
   - Downloadable Official Offer Letter (PDF)
   - Decision Expiration Countdown Timer
3. The student evaluates the package and selects either "Accept Offer" or "Decline Offer".
4. If the student selects **Accept Offer**:
   - A critical confirmation modal displays: *"Accepting this offer will formally mark you as PLACED. Under institutional policy, your remaining active applications for regular tier drives will be automatically withdrawn. Do you wish to proceed?"*
   - The student types "ACCEPT" and confirms.
   - The system transitions the offer status to `Accepted`, updates student profile placement status to `Placed`, and executes automated policy cascades (locks profile, auto-withdraws from lower/equal tier drives).
   - TPC Admin and the recruiter are notified immediately.

#### D. Alternative Flow (Decline Offer)
- If the student selects **Decline Offer**:
  - The system prompts for a mandatory recorded reason (e.g., "Pursuing Higher Studies", "Competing offer accepted", "Location constraints").
  - The system transitions the offer status to `Declined` and alerts TPC Admin and the recruiter.
  - Institutional placement policy records the decline against the student's quota.

#### E. Failure Cases
- **Expired Offer Window:** The student attempts to accept an offer after the decision deadline has passed. System blocks the action with: *"This offer decision window has expired. Contact TPC Admin."*
- **Attempting Conflicting Acceptance:** Student somehow attempts to accept two offers simultaneously. Database transaction locks ensure only the first valid acceptance succeeds; the second is rejected.
- **Decline Without Justification:** The student attempts to decline without entering a written explanation. Form validation blocks submission.

#### F. Expected Result
The student's employment status is definitively resolved, and institutional placement rules are programmatically enforced across all other active drives.

#### G. Acceptance Criteria
- Acceptance triggers immediate state change of the student to `Placed`.
- The system must prevent placed students from applying to non-qualifying drives as per institutional tier rules.
- Offer letters must be downloadable only by the recipient student and authorized administrators.

---

### 3.14 Placement Alerts & In-App Notifications

#### A. Description
Delivers critical, event-driven notifications to students regarding application updates, eligibility announcements, upcoming interview slots, and offer expirations.

#### B. Preconditions
- The student has an active account.

#### C. Main Flow
1. A triggering lifecycle event occurs in the system (e.g., TPC verifies profile, new drive published, recruiter shortlists application, interview scheduled, offer extended).
2. The system generates an in-app notification record linked to the student ID containing: Event Category, Title, Message Body, Action URL, and Timestamp.
3. An unread notification badge indicator appears in the student's navigation bar.
4. The student clicks the notification bell icon to view a chronological feed of recent alerts.
5. The student clicks a notification item (e.g., "You have been shortlisted for Cloud Software Engineer!").
6. The system marks the alert as `Read` and routes the student directly to the relevant application/interview page.

#### D. Alternative Flow
- **Mark All as Read:** The student clicks "Mark all as read" to clear all unread notification badges in a single action.

#### E. Failure Cases
- **Stale Notification Navigation:** A student clicks an alert for a drive that was subsequently cancelled or archived. The system loads a safe fall-back page displaying: *"The referenced drive is no longer active."*

#### F. Expected Result
Students receive immediate, actionable communication on critical placement milestones, minimizing missed deadlines and interview no-shows.

#### G. Acceptance Criteria
- High-priority events (Interview Scheduled, Offer Extended, Verification Rejected) must trigger persistent in-app alerts.
- Read/Unread state is preserved across login sessions.

---

## 4. Recruiter / Alumni Functional Requirements

This section details the 14 functional requirements for the **Recruiter / Alumni** persona. Each feature defines operational preconditions, primary workflows, alternative pathways, failure handling, expected outcomes, and verifiable acceptance criteria.

---

### 4.1 Recruiter / Alumni Registration & Authentication (Login)

#### A. Description
Enables corporate recruitment partners and verified alumni to register for an organizational hiring account, verify their identity, and authenticate securely to access recruitment management tools.

#### B. Preconditions
- The recruiter represents a legitimate hiring organization or is a registered alumnus of the institution.
- The user possesses a valid corporate email address (e.g., `@company.com`) or verified alumni credentials.

#### C. Main Flow
1. The user navigates to the portal registration page and selects "Recruiter / Alumni Registration".
2. The user submits: Full Name, Work Email, Phone Number, Organization Name, Designation, User Sub-type (`Corporate Recruiter` vs. `Alumnus Referrer`), Official Website URL, and Password.
3. If `Alumnus Referrer` is selected, the user additionally provides: Graduation Year, Degree, and Branch of study.
4. The system validates corporate email domain authenticity (blocking generic domains like `@gmail.com` for corporate accounts) and checks against existing accounts.
5. The system persists the account in an `Inactive (Pending TPC Approval)` state and sends an email verification link.
6. The user confirms their email via the verification token.
7. TPC Admin receives an administrative prompt to review and approve the corporate/alumni account.
8. Upon TPC approval, the account transitions to `Active`, and the recruiter can authenticate using their email and password to receive a secure session token (JWT) scoped to the `RECRUITER` role. The database remains the authoritative source for critical, mutable state (such as verification, organization standing, and approval status).

#### D. Alternative Flow
- **Invited Recruiter Onboarding:** TPC Admin directly creates and invites an authorized recruiter via an invitation link. The recruiter clicks the link, sets their password, and immediately accesses the active dashboard without secondary approval.

#### E. Failure Cases
- **Generic Email Submission:** A corporate recruiter attempts to register with a free public email provider (e.g., `@yahoo.com`, `@gmail.com`). System halts registration with: *"Corporate accounts must register using an official company email address."*
- **Unapproved Login Attempt:** The user verifies email but attempts to log in while account is still `Pending TPC Approval`. System prevents dashboard access with: *"Your account is awaiting verification by the Training & Placement Cell."*
- **Incorrect Credentials:** Invalid password returns standard authentication failure message.

#### F. Expected Result
The recruiter/alumni account is authenticated with appropriate institutional vetting, granting access to job creation and applicant management tools.

#### G. Acceptance Criteria
- Corporate accounts must be backed by non-public email domains unless explicitly exempted by TPC Admin.
- Unapproved recruiter accounts cannot create or submit drives for approval, nor view student information.

---

### 4.2 Recruiter & Organization Profile Management

#### A. Description
Allows recruiters to maintain their corporate identity, provide organizational background, upload corporate branding (logos), and manage primary contact details visible to students and TPC.

#### B. Preconditions
- The recruiter is authenticated with an active, approved account.

#### C. Main Flow
1. The recruiter navigates to the "Company Profile" section.
2. The recruiter updates: Organization Overview, Headquarters Location, Industry Domain (e.g., Software, Finance, Core Engineering), Careers Portal URL, Company LinkedIn URL, and uploads an official high-resolution Company Logo (`.png`, `.jpg`, $\le 2\text{ MB}$).
3. The recruiter saves changes.
4. The system validates image dimensions/file size, sanitizes URLs, and updates the organization entity.
5. A confirmation message appears: *"Company profile updated successfully."*

#### D. Alternative Flow
- **Alumni Profile Context:** For alumni referrers, the profile also highlights their graduation cohort and alma mater department alongside their current employer information.

#### E. Failure Cases
- **Oversized Logo:** The recruiter uploads an image larger than 2 MB. System rejects with: *"Logo file size must not exceed 2 MB."*
- **Invalid Web Address:** An invalid URL syntax is rejected with an inline validation warning.

#### F. Expected Result
The company brand profile is updated and uniformly rendered across job cards, drive headers, and student application views.

#### G. Acceptance Criteria
- Company logos are served via secure CDN/cloud storage.
- Updates to company profiles automatically propagate across all active job postings tied to that organization.

---

### 4.3 Job Opportunity & Referral Drive Creation

#### A. Description
Enables recruiters and alumni to initiate a new employment opportunity, designating whether it is a formal On-Campus Recruitment Drive or an Alumni Referral Opening.

#### B. Preconditions
- The recruiter is authenticated and approved by TPC Admin.

#### C. Main Flow
1. The recruiter navigates to the "Drives & Postings" tab and clicks "Create New Opportunity".
2. The recruiter selects Opportunity Category:
   - `On-Campus Placement Drive` (full institutional recruitment process with multi-round interviews and offer issuance).
   - `Alumni Referral Posting` (referral opportunity with candidate resume shortlisting and direct referral submission).
3. The recruiter inputs: Job Title, Target Role Type (`Full-Time`, `Internship + PPO`, `Internship Only`), Placement Tier (`Regular`, `Dream`, `Super Dream`), Work Location (e.g., Bengaluru, Hybrid, Remote), Number of Open Vacancies (optional/indicative), Application Opening Date, and Application Closing Deadline.
4. The recruiter inputs compensation details: Annual Gross CTC (INR), Fixed Base Pay, Variable Performance Bonus, and Monthly Stipend (if internship).
5. The recruiter clicks "Submit for TPC Approval" (or "Save as Draft").
6. The system persists the posting record with initial status `PENDING_APPROVAL` (or `DRAFT`). Recruiter-created jobs MUST NOT become `ACTIVE` immediately upon creation.

#### D. Alternative Flow
- **Clone Existing Posting:** The recruiter selects a previous drive posting, clicks "Clone Drive", and modifies dates and compensation figures to submit a new drive for TPC approval.

#### E. Failure Cases
- **Invalid Deadline Configuration:** Application closing deadline is set in the past or earlier than the opening date. System returns: *"Application deadline must be a future date and time."*
- **Zero / Negative Compensation:** Base CTC is submitted as negative or non-numeric. System rejects input.
- **Unverified Recruiter Action:** If an unapproved account attempts to create a posting, the API returns HTTP 403 Forbidden.

#### F. Expected Result
A structured job posting entity is initialized in the database with status `PENDING_APPROVAL`, awaiting review and approval by TPC Admin.

#### G. Acceptance Criteria
- Recruiter-created jobs MUST NOT become `ACTIVE` immediately after creation; they must initially have status `PENDING_APPROVAL`.
- Only an authorized TPC Admin can review and approve a pending job, transitioning its status to `ACTIVE`.
- Students must not be able to view or apply to jobs that are still `PENDING_APPROVAL`.
- Placement Tier assignment must conform to institutional CTC thresholds defined by TPC.

---

### 4.4 Job Description & Role Specification Definition

#### A. Description
Allows recruiters to document comprehensive role specifications, candidate responsibilities, qualification expectations, service agreements (bonds), and recruitment milestones.

#### B. Preconditions
- A job posting record has been created and is in `DRAFT` or `PENDING_APPROVAL` state.

#### C. Main Flow
1. The recruiter opens the "Job Description" tab within the posting editor.
2. The recruiter inputs:
   - **Role Summary:** Overview of the engineering/functional team and core objectives.
   - **Key Responsibilities:** Structured bullet points detailing day-to-day duties.
   - **Candidate Requirements:** Academic background expectations and soft skill competencies.
   - **Bond / Service Agreement:** Specifies whether a service agreement applies (e.g., "None" or "18 Months, ₹1,00,000 indemnity bond").
   - **Recruitment Process Rounds:** Outlines selection stages (e.g., "1. Online Coding Assessment, 2. Technical Round 1, 3. Technical Round 2, 4. HR Interview").
3. The recruiter saves the details.
4. The system validates required text lengths and saves the formatted markdown/HTML text.

#### D. Alternative Flow
- **Rich-Text Formatting:** The recruiter utilizes a markdown editor to incorporate formatted headers, bulleted lists, and emphasized text.

#### E. Failure Cases
- **Incomplete Description:** The recruiter submits a description shorter than 50 characters. System flags: *"Job description must be at least 50 characters to enable accurate ATS matching."*
- **Post-Commencement Unilateral Modifications:** Recruiter attempts to drastically rewrite job responsibilities after students have already applied. System flags that changes require TPC administrative notification.

#### F. Expected Result
The job posting contains clear, structured role expectations accessible to eligible students and usable by the ATS keyword indexing engine.

#### G. Acceptance Criteria
- Job description text is properly sanitized against XSS attacks.
- Text content is parsed and indexed for ATS resume matching.

---

### 4.5 Required Skills & Keyword Taxonomy Definition

#### A. Description
Enables recruiters to define mandatory core skills, secondary preferred skills, and domain tools that the ATS resume matching engine will use to score and rank student applicants.

#### B. Preconditions
- A job posting is open for editing.

#### C. Main Flow
1. The recruiter accesses the "Required Skills & ATS Tuning" tab.
2. The recruiter searches and tags:
   - **Mandatory Skills (Primary Keywords):** (e.g., `Java`, `Spring Boot`, `PostgreSQL`, `Data Structures`).
   - **Preferred / Bonus Skills (Secondary Keywords):** (e.g., `Docker`, `Kubernetes`, `AWS`, `Kafka`).
   - **Domain / Conceptual Tags:** (e.g., `Microservices`, `RESTful Design`, `CI/CD`).
3. For each skill, the system normalizes the tag against the platform's standardized skill taxonomy.
4. The recruiter assigns relative importance weightings (e.g., Mandatory Skills: $70\%$ score weight; Preferred Skills: $30\%$ score weight).
5. The recruiter clicks "Save Skills Configuration".
6. The system indexes the target skill vector for automated candidate scoring.

#### D. Alternative Flow
- **Auto-Extract from Description:** The recruiter clicks "Extract Skills from JD Text". The system automatically scans the entered job description text, detects technical terms matching the platform taxonomy, and pre-populates the skill lists for recruiter review.

#### E. Failure Cases
- **Zero Mandatory Skills Defined:** Recruiter attempts to finalize the drive without selecting any required skills. System rejects with: *"At least one mandatory skill is required to enable ATS matching."*
- **Duplicate Tags:** Recruiter selects the same technology under both mandatory and preferred lists. System alerts: *"Skill cannot be both mandatory and preferred."*

#### F. Expected Result
A clean, weighted skill profile is attached to the job posting, serving as the benchmark for algorithmic resume evaluation.

#### G. Acceptance Criteria
- Skills are canonicalized into standard identifiers (e.g., `nodejs` $\rightarrow$ `Node.js`).
- Weightings must sum to $100\%$ across evaluation categories.

---

### 4.6 Eligibility Criteria & Gatekeeping Rules Configuration

#### A. Description
Allows recruiters to establish strict, deterministic academic cutoffs and institutional eligibility filters that automatically gate which students are permitted to apply.

#### B. Preconditions
- A job posting is in `DRAFT` or `PENDING_APPROVAL` state.

#### C. Main Flow
1. The recruiter navigates to the "Eligibility Configuration" tab.
2. The recruiter configures parameters:
   - **Minimum CGPA Cutoff:** (e.g., $\ge 7.50 / 10.00$).
   - **Minimum 10th Standard Percentage:** (e.g., $\ge 70.0\%$).
   - **Minimum 12th / Diploma Percentage:** (e.g., $\ge 70.0\%$).
   - **Active Backlog Limit:** Maximum allowable active standing backlogs (e.g., $0$ or $\le 1$).
   - **Dead / History Backlog Allowance:** Boolean indicating whether students with cleared historical backlogs are permitted.
   - **Eligible Degree Programs & Branches:** Multi-select checklist of permitted branches (e.g., Computer Science, Information Technology, Electronics & Communication).
   - **Target Graduation Year / Batch:** (e.g., 2026 Batch).
   - **Gender Inclusivity Settings:** (All / Female-only diversity drive, if authorized by institutional policy).
3. The recruiter saves the criteria.
4. The system validates parameter boundaries and stores the gatekeeping rule definition.

#### D. Alternative Flow
- **TPC Mandatory Overrides:** If the recruiter sets criteria violating university guidelines (e.g., setting a CGPA cutoff lower than the university baseline for campus drives), the system notifies the recruiter that TPC approval will evaluate the exception.

#### E. Failure Cases
- **Invalid Threshold Values:** Recruiter enters a CGPA cutoff $> 10.0$ or a percentage $> 100\%$. System rejects with inline errors.
- **No Branches Selected:** Recruiter leaves the branch list empty. System halts saving with: *"You must select at least one eligible academic department."*

#### F. Expected Result
The job posting is bounded by programmatic gatekeeping rules that the student eligibility engine executes in real time.

#### G. Acceptance Criteria
- Gatekeeping rules cannot be softened or modified after applications open without explicit TPC Admin approval and audit logging.
- Only verified student academic data will be compared against these rules.

---

### 4.7 Application Pool & Pipeline Management (View Applications)

#### A. Description
Provides recruiters with a centralized, real-time dashboard of all students who have applied to their job posting, organized by pipeline stages with search, filter, and batch-action capabilities.

#### B. Preconditions
- The recruiter is authenticated.
- The job posting is `Active` or `Closed` and has received student applications.

#### C. Main Flow
1. The recruiter navigates to "Drives", selects a specific job posting, and clicks "View Applicants".
2. The system renders the applicant pool table displaying: Candidate Name, Roll Number, Department/Branch, Verified CGPA, Active Backlogs, Application Submission Date, **ATS Match Score (%)**, Current Stage Badge, and Action Menus.
3. The recruiter uses dynamic filters to narrow down candidates:
   - Filter by ATS Match Score Range (e.g., $\ge 80\%$).
   - Filter by Branch / Department.
   - Filter by CGPA bracket (e.g., $\ge 8.5$).
   - Filter by Current Stage (e.g., `Applied`, `Shortlisted`, `Technical Round 1`).
4. The recruiter clicks on column headers to sort the candidate table (e.g., sort descending by ATS score).

#### D. Alternative Flow
- **Kanban Pipeline View:** The recruiter toggles from table view to a drag-and-drop Kanban Board displaying candidate cards categorized across columns: `Applied` $\rightarrow$ `Shortlisted` $\rightarrow$ `Round 1` $\rightarrow$ `Round 2` $\rightarrow$ `Offered` $\rightarrow$ `Rejected`.

#### E. Failure Cases
- **Unauthorized Job Access:** Recruiter attempts to access applicant lists of another company's job posting via URL parameter manipulation. System returns HTTP 403 Forbidden with: *"Access Denied: You do not own this recruitment drive."*

#### F. Expected Result
Recruiters have a comprehensive, structured interface to monitor, search, and manage candidate progression through their selection pipeline.

#### G. Acceptance Criteria
- Recruiters can only access applications submitted directly to their own job postings.
- Filtering and sorting operations must execute reliably across large applicant pools ($\ge 1000$ candidates).

---

### 4.8 Candidate Profile & Verified Academic Inspection (View Candidate Info)

#### A. Description
Enables recruiters to inspect a candidate's complete verified institutional profile, verified academic metrics, educational history, portfolio links, and self-reported skills.

#### B. Preconditions
- The recruiter is viewing an applicant tied to their own active drive.

#### C. Main Flow
1. The recruiter clicks on a candidate's name or row in the applicant table.
2. A detailed Candidate Profile drawer/page opens displaying:
   - Full Name, Student Roll Number, Branch, and Degree Cohort.
   - Verified Academic Summary: 10th %, 12th %, Current CGPA, Active Backlogs, Total Historical Backlogs, and Green `TPC Verified` status badge.
   - Professional Links: Clickable URLs for GitHub, LinkedIn, and personal portfolio websites.
   - Candidate Skills Inventory: Tagged competencies categorized by proficiency level.
   - Application History for this drive: Date applied, resume version selected, and current stage.
3. The recruiter reviews the verified profile to confirm credentials before moving the candidate to interview rounds.

#### C. Alternative Flow
- **Internal Recruiter Notes:** The recruiter writes private notes (e.g., "Strong GitHub projects in distributed systems; recommend for Backend team") accessible only to recruiter team members and TPC.

#### E. Failure Cases
- **Unverified Profile Warning:** If an administrative override permitted an unverified student to apply, the profile prominently displays an amber alert: *"Warning: Academic records pending official TPC verification."*

#### F. Expected Result
Recruiters inspect trusted, university-verified student credentials without relying on unvalidated claims.

#### G. Acceptance Criteria
- Verified academic fields are clearly distinguished from self-reported profile links.
- Recruiter notes are strictly hidden from the candidate's view.

---

### 4.9 Candidate Resume Inspection & Document Access (View Resumes)

#### A. Description
Allows recruiters to view, preview in-browser, and download the exact PDF resume submitted by the candidate for this specific job application.

#### B. Preconditions
- The candidate has applied with an uploaded PDF resume.
- The recruiter is viewing an application within their own drive.

#### C. Main Flow
1. Within the applicant list or candidate profile drawer, the recruiter clicks "View Resume".
2. The system generates a time-limited, secure signed URL to retrieve the document from cloud storage.
3. The system renders an embedded PDF preview directly within a modal viewer in the browser.
4. The recruiter reviews the formatted resume (projects, internships, publications, extracurriculars).
5. The recruiter optionally clicks "Download PDF" to save the file locally.

#### D. Alternative Flow
- **Bulk Resume Download:** The recruiter selects multiple candidates via checkboxes (or filters by "Shortlisted") and clicks "Download Resumes (ZIP)". The system compiles the selected PDF files into a single structured archive file (`Drive_Shortlisted_Resumes.zip`) for offline review by the interview panel.

#### E. Failure Cases
- **Storage Retrieval Error:** If the cloud asset is temporarily inaccessible, the system logs the incident and displays: *"Document temporarily unavailable. Please retry or notify TPC Admin."*
- **Unauthorized Resume Fetch:** Direct hot-linking to cloud storage URLs without authentication is blocked by storage bucket security policies.

#### F. Expected Result
Recruiters seamlessly review and download authentic candidate resumes with high visual fidelity and document security.

#### G. Acceptance Criteria
- Resumes are served strictly via authenticated, temporary signed URLs.
- Bulk download archives must name PDF files cleanly using the format: `RollNumber_StudentName_Resume.pdf`.

---

### 4.10 ATS Match Score & Keyword Breakdown Analytics (View ATS Info)

#### A. Description
Provides recruiters with an explainable, data-driven breakdown of how the candidate's resume text scored against the job's required and preferred skills taxonomy.

#### B. Preconditions
- The candidate's resume has undergone text extraction.
- The job posting has configured required skills.

#### C. Main Flow
1. In the applicant table, the recruiter clicks on the candidate's **ATS Match Score** badge (e.g., `88%`).
2. An ATS Breakdown modal opens detailing:
   - **ATS Match Score:** Total percentage ($0-100\%$) computed strictly via deterministic Jaccard similarity based on normalized skill sets: $\frac{|\text{ResumeSkills} \cap \text{RequiredSkills}|}{|\text{ResumeSkills} \cup \text{RequiredSkills}|} \times 100$.
   - **Matched Skills:** Green badges indicating required technologies identified in the resume (e.g., `Python [Found]`, `PostgreSQL [Found]`, `Docker [Found]`).
   - **Missing Skills:** Red badges highlighting required job skills not identified in the resume (e.g., `Kubernetes [Not Found]`).
   - **Skills Count:** Explicit tally of matching skills count versus total required skills count.
   - *(V2 Scope: Mandatory/Preferred badge partitioning and keyword occurrence context snippets are deferred to V2).*
3. The recruiter uses this objective data to make informed shortlisting decisions.

#### D. Alternative Flow
- **Threshold Auto-Filter:** The recruiter toggles "Show only candidates with ATS Score $\ge 75\%$", instantly filtering the candidate pool to high-alignment applicants.

#### E. Failure Cases
- **Unparsed Resume Text:** Candidate resume was an unreadable image scan. ATS score displays as `N/A` with indicator: *"Text extraction failed: Scanned image document."*

#### F. Expected Result
Recruiters gain objective, explainable shortlisting assistance rather than reviewing hundreds of documents manually.

#### G. Acceptance Criteria
- ATS match computation must be fully transparent, showing matched and missing skills explicitly.
- Filtering by ATS score must execute in sub-second time for lists up to 1,000 candidates.

---

### 4.11 Candidate Pipeline Progression (Shortlist & Reject Candidates)

#### A. Description
Enables recruiters to transition applicants between lifecycle stages, advancing promising candidates to shortlists or interview rounds and marking unsuccessful candidates as rejected.

#### B. Preconditions
- The recruiter is authenticated and owns the job posting.
- Target applications are in a mutable lifecycle state (e.g., `Applied` or active `Interview Round`).

#### C. Main Flow
1. The recruiter selects one or more candidates using table checkboxes.
2. The recruiter clicks the "Actions" dropdown and selects "Shortlist Candidates" or "Advance to Round [X]".
3. A confirmation modal appears summarizing the action: *"Advance 15 selected candidates to Technical Round 1?"*
4. The recruiter clicks "Confirm Progression".
5. The system atomically updates the application stage for all selected candidates to `Shortlisted` (or `Round 1`).
6. The system dispatches in-app notifications to the updated candidates.
7. The recruiter table refreshes, reflecting the updated pipeline statuses.

#### D. Alternative Flow (Candidate Rejection)
- The recruiter selects candidates to disqualify and clicks "Mark as Rejected".
- The system prompts for an optional rejection reason (e.g., "Did not meet technical assessment threshold").
- The system transitions the status to `Rejected`. Unsuccessful candidates receive a formal, professional notification, and their application lifecycle for this drive terminates.

#### E. Failure Cases
- **Attempting to Alter Locked / Placed Students:** If a candidate has already accepted another company's offer and is locked as `Placed`, the system halts stage advancement with: *"Cannot advance candidate: Student is locked under university placement policy (Placed elsewhere)."*
- **Empty Batch Selection:** Recruiter clicks batch action without selecting rows. System alerts: *"Please select at least one candidate."*

#### F. Expected Result
Candidate application stages advance sequentially through the selection funnel, keeping students, recruiters, and TPC aligned.

#### G. Acceptance Criteria
- Stage transitions must be logged with timestamp and acting user ID for institutional audit trails.
- Batch operations must be atomic (either all valid candidates transition, or failed items are explicitly reported).

---

### 4.12 Multi-Round Interview Scheduling & Management

#### A. Description
Allows recruiters to define multi-stage interview workflows, configure specific interview slots, assign shortlisted candidates, provide venue/meeting credentials, and log round evaluation ratings.

#### B. Preconditions
- Candidates have been advanced to an interview-eligible stage (`Shortlisted` or subsequent round).

#### C. Main Flow
1. The recruiter navigates to the "Interview Scheduler" for the active drive.
2. The recruiter clicks "Add Interview Round" and defines:
   - Round Name: (e.g., "Round 1: System Design & Algorithms").
   - Round Type: `In-Person (Campus)` or `Virtual Video Conference`.
   - Date, Start Time, and Duration per slot (e.g., 45 minutes).
3. The recruiter generates time slots and assigns shortlisted candidates to specific slots.
4. For in-person rounds, the recruiter specifies Campus Building / Room Number; for virtual rounds, the recruiter inputs Meeting Platform URL and Access Code.
5. The recruiter clicks "Publish Schedule & Notify Candidates".
6. The system persists the interview records, assigns candidates, and sends calendar alerts to students.
7. During/after the interview, the recruiter opens the slot scorecard to enter evaluation ratings (Scale 1–5 for Technical Competency, Problem Solving, Communication) and marks result: `Passed to Next Round` or `Failed Round`.

#### D. Alternative Flow (Slot Reschedule)
- If a scheduling conflict arises, the recruiter updates the time slot and inputs a reschedule reason. The system alerts the candidate and updates their calendar view.

#### E. Failure Cases
- **Slot Conflict (Double Booking):** Recruiter inadvertently schedules two candidates to the same interviewer/slot simultaneously. System warns: *"Interviewer slot collision detected. Confirm if parallel panels exist."*
- **University Examination Clash:** TPC Admin flags the scheduled date as an institutional black-out period (e.g., semester final exams). System notifies recruiter to choose an alternate date.

#### F. Expected Result
Interviews are organized smoothly with zero communication ambiguity, transparent venue coordinates, and structured candidate evaluation logging.

#### G. Acceptance Criteria
- Meeting links and room allocations are revealed only to assigned candidates.
- Interview scorecards and evaluator ratings are permanently archived for drive auditing.

---

### 4.13 Employment Offer Issuance & Lifecycle Management

#### A. Description
Enables recruiters to extend formal employment offers to successful candidates, specifying finalized compensation breakdowns, joining timelines, and uploading official offer letters.

#### B. Preconditions
- The candidate has completed all prerequisite interview rounds.
- The candidate does not hold a conflicting accepted offer under institutional placement policies.

#### C. Main Flow
1. In the applicant management dashboard, the recruiter selects a candidate who cleared the final round and clicks "Extend Offer".
2. The recruiter inputs offer terms:
   - Final Annual Gross CTC (INR).
   - Fixed Base Salary & Variable Incentive.
   - Joining Date and Employment Location.
   - Offer Acceptance Deadline (e.g., 72 hours from issuance).
3. The recruiter uploads the formal Offer Letter document (`.pdf`, $\le 5\text{ MB}$).
4. The recruiter clicks "Submit Offer for TPC Verification".
5. The system performs an institutional policy check (ensures student is not already placed in an equal or higher tier).
6. TPC Admin validates the offer terms. Upon TPC clearance, the offer is officially released to the student.
7. The application stage transitions to `Offered`, and the student's decision countdown begins.
8. The recruiter tracks offer state in real time: `Pending Candidate Decision`, `Accepted`, or `Declined`.

#### D. Alternative Flow (Offer Revocation Prior to Acceptance)
- In the rare event of organizational hiring freezes or administrative errors, the recruiter submits an "Offer Revocation Request" with written justification to TPC Admin for joint approval before the candidate accepts.

#### E. Failure Cases
- **Policy Violation (Student Already Placed):** Recruiter attempts to extend a Regular tier offer to a student who already holds a Regular tier offer. The system blocks the action: *"Policy Restriction: Student has already accepted an offer in this tier under the university 'One Job' policy."*
- **Missing Offer Letter:** Recruiter attempts to issue an offer without attaching the formal offer document. System requires a valid PDF attachment.

#### F. Expected Result
Legitimate, policy-compliant employment offers are extended with verifiable compensation terms and monitored decision windows.

#### G. Acceptance Criteria
- Offers cannot bypass university placement tier rules.
- When a candidate accepts an offer, the recruiter's dashboard reflects the `Accepted` state immediately.

---

### 4.14 Job-Level Recruitment Funnel & Pipeline Analytics

#### A. Description
Provides recruiters with real-time visual analytics and metrics on their recruitment drive, tracking applicant volumes, qualification conversion rates, and stage-by-stage candidate drop-offs.

#### B. Preconditions
- The recruiter is authenticated.
- The drive has received applications.

#### C. Main Flow
1. The recruiter opens the "Drive Analytics" tab for their job posting.
2. The system computes and renders key performance indicators:
   - **Total Applications Received:** Raw applicant count (all submissions are pre-screened as `ELIGIBLE` by the Eligibility Gate).
   - **ATS Score Distribution:** Histogram showing candidate count across match score tiers ($<50\%$, $50-70\%$, $70-85\%$, $\ge 85\%$).
   - **Recruitment Funnel Conversion:** Step-by-step funnel visualization (`APPLIED` $\rightarrow$ `ATS_SHORTLISTED` $\rightarrow$ `INTERVIEW_SCHEDULED` $\rightarrow$ `OFFER_EXTENDED` $\rightarrow$ `ACCEPTED`).
   - **Branch / Gender Diversity Breakdown:** Demographic distribution of shortlisted and offered candidates.
3. The recruiter reviews metrics to evaluate drive yield and panel throughput.

#### D. Alternative Flow (Shortlist Export)
- From the analytics dashboard, the recruiter clicks "Export Funnel Report (CSV)" to download tabular summary data for internal corporate HR reporting.

#### E. Failure Cases
- **Zero Applications Edge Case:** A freshly published drive with no applicants displays clean empty-state visual placeholders rather than calculation errors or blank screens.

#### F. Expected Result
Recruiters gain actionable, real-time insights into their campus hiring efficiency, pipeline velocity, and candidate drop-off patterns.

#### G. Acceptance Criteria
- All metric aggregations are strictly scoped to the recruiter's own drive.
- Funnel stage conversion calculations must update dynamically as candidate stages mutate.

---

## 5. TPC Admin Functional Requirements

This section specifies the 13 functional requirements for the **TPC Admin** persona. As the supreme operational and compliance authority for the campus placement ecosystem, TPC Admin exercises universal governance over student records, corporate engagements, eligibility enforcement, pipeline oversight, dispute resolution, and regulatory reporting.

---

### 5.1 Student Account & Academic Cohort Management (Manage Students)

#### A. Description
Enables TPC Admins to manage the university-wide student placement directory, onboard student cohorts via bulk CSV import or individual provisioning, verify academic credentials against official registrar records, and manage student account states (`Unverified`, `Verified`, `Suspended`, `Debarred`).

#### B. Preconditions
- The TPC Admin is authenticated with root institutional administrative privileges.

#### C. Main Flow
1. The admin navigates to the "Students Directory" in the TPC control panel.
2. The system renders the complete institutional student table with columns: Roll Number, Full Name, Department/Branch, Degree Program, Batch Year, Verified CGPA, Active Backlogs, Profile Verification Status, and Placement Status (`Unplaced`, `Placed`, `Opted Out`).
3. To onboard a new batch, the admin clicks "Bulk Import Students (CSV)".
4. The admin uploads a structured CSV file containing registrar records (`roll_no`, `full_name`, `email`, `department`, `cgpa`, `active_backlogs`, `history_backlogs`, `batch_year`).
5. The system performs dry-run validation (checking schema, duplicate roll numbers, valid branch identifiers, and numeric ranges).
6. The admin clicks "Execute Import".
7. The system creates or updates student records, sets academic fields to `Verified` (since sourced from official registrar data), and issues system welcome/activation emails to students.
8. The student directory updates immediately, displaying the imported batch.

#### D. Alternative Flow (Individual Student Record Modification)
- If an individual student submits official grade re-evaluation proof, the admin searches for the student by roll number, clicks "Edit Academic Record", updates the CGPA/backlog values, and logs the official registrar memo number as justification.

#### E. Failure Cases
- **CSV Schema Mismatch:** Uploaded CSV is missing required columns or uses incorrect header names. System rejects import and returns an error report: *"Schema validation failed: Missing column 'active_backlogs' at row 1."*
- **Duplicate Identifier Collision:** The CSV contains multiple identical roll numbers or emails. System flags conflicting row numbers for administrative resolution.
- **Out-of-Bounds Grade Values:** CSV contains CGPA $> 10.0$ or negative backlogs. System rejects invalid rows and outputs a downloadable error log.

#### F. Expected Result
Student cohorts are successfully provisioned, verified, and locked, providing an indisputable baseline for corporate recruitment eligibility.

#### G. Acceptance Criteria
- Bulk imports must handle up to 5,000 student records within an acceptable transaction window without timing out.
- Any manual modification to verified student grades must require a recorded administrative reason.

---

### 5.2 Recruiter & Organization Governance (Manage Recruiters)

#### A. Description
Allows TPC Admins to vet, verify, approve, suspend, or revoke corporate recruiter and alumni referrer accounts to protect students from fraudulent employers or unvetted recruitment campaigns.

#### B. Preconditions
- The TPC Admin is authenticated.

#### C. Main Flow
1. The admin navigates to the "Recruiter Approvals & Directory" section.
2. The system displays a dedicated tab: `Pending Verification`, listing newly registered corporate recruiters and alumni referrers.
3. The admin inspects the applicant's details: Organization Name, Corporate Domain Email, Official Website, HR Contact Phone, and User Subtype (`Corporate Recruiter` vs. `Alumnus`).
4. For alumni, the admin cross-checks the alumnus graduation year and department against historical alumni databases.
5. The admin clicks "Approve Recruiter".
6. The system transitions the recruiter account status from `Inactive (Pending TPC Approval)` to `Active` and dispatches an automated approval email containing portal access instructions.
7. The recruiter is moved to the "Approved Employers" directory.

#### D. Alternative Flow (Rejection or Suspension)
- If an organization cannot be verified or violates institutional hiring ethics, the admin clicks "Reject Registration" or "Suspend Account", enters a mandatory institutional reason, and revokes access. All active job postings associated with the account are immediately unpublished.

#### E. Failure Cases
- **Approving Blacklisted Entities:** If an admin attempts to approve an employer previously flagged on the institutional blacklist, the system displays a critical warning dialog: *"Caution: This organization is currently listed on the institutional disciplinary blacklist."*

#### F. Expected Result
Only legitimate, vetted corporate hiring partners and verified alumni can interact with students or post job opportunities.

#### G. Acceptance Criteria
- Unapproved recruiters cannot create or submit drives for approval, nor access candidate profiles.
- Suspending an organization immediately invalidates active recruiter sessions and unpublishes associated active drives.

---

### 5.3 Centralized Campus Drive & Referral Governance (Manage Jobs)

#### A. Description
Enables TPC Admins to review, approve, edit, schedule, pause, or archive campus recruitment drives and alumni referral postings submitted by recruiters or created directly by the placement office.

#### B. Preconditions
- The TPC Admin is authenticated.

#### C. Main Flow
1. The admin navigates to the "Drives Management" dashboard.
2. Postings awaiting institutional authorization appear under `Pending Drive Approvals` with status `PENDING_APPROVAL`.
3. The admin opens the drive details to inspect: Role Title, CTC Breakdown (Base Pay vs. Variable components), Bond/Service Agreement terms, Configured Eligibility Criteria, and Proposed Recruitment Dates.
4. The admin verifies that the compensation structure meets university minimum CTC guidelines for campus placement drives and that the drive schedule does not collide with existing academic exams or peer company drives.
5. The admin assigns the official institutional tier badge (`Regular`, `Dream`, `Super Dream`).
6. The admin makes an approval decision:
   - **Approve:** The admin clicks "Approve & Publish Drive". The system transitions the drive state from `PENDING_APPROVAL` to `ACTIVE` and broadcasts an in-app notification to all eligible students.
   - **Reject:** The admin clicks "Reject Drive" and enters a mandatory rejection justification note. The system transitions drive status to `REJECTED_BY_TPC` and notifies the recruiter.

#### D. Alternative Flow (Direct Institutional Drive Creation)
- For visiting legacy campus recruiters who coordinate directly via offline placement office channels, the admin clicks "Create Campus Drive", inputs all company, role, eligibility, and scheduling parameters, and publishes the drive directly on behalf of the partner organization.

#### E. Failure Cases
- **Unrealistic Compensation / Ambiguous Terms:** Recruiter submits an inflated CTC that includes non-guaranteed multi-year stock options without fixed base pay. Admin clicks "Request Revisions" and inputs required clarifications; drive status returns to `DRAFT (Revisions Requested)` or `REJECTED_BY_TPC`.

#### F. Expected Result
Every recruitment drive published on the student portal is certified for compensation legitimacy, policy compliance, and scheduling alignment.

#### G. Acceptance Criteria
- Recruiter-created jobs MUST NOT become `ACTIVE` immediately upon creation; they must initially have status `PENDING_APPROVAL`.
- Only an authorized TPC Admin can approve a pending job, transitioning it to `ACTIVE`.
- TPC Admin may reject a pending job with a recorded justification (`REJECTED_BY_TPC`).
- Students must not be able to view or apply to jobs that are still `PENDING_APPROVAL` or `REJECTED_BY_TPC`.
- Modifying a published drive's criteria triggers an automated audit entry and recalculation of student eligibility badges.

---

### 5.4 Institutional Eligibility Configuration & Academic Verification (Configure/Verify Eligibility)

#### A. Description
Empowers TPC Admins to configure institutional placement eligibility rules (e.g., minimum university baseline cutoffs, maximum tier application limits, backlog policy definitions) and individually or batch-verify student academic standing.

#### B. Preconditions
- The TPC Admin is authenticated.

#### C. Main Flow
1. The admin accesses "Placement Policy & Eligibility Settings".
2. The admin defines university-wide placement thresholds:
   - **Tier CTC Brackets:** e.g., Regular ($\le 7\text{ LPA}$), Dream ($7-15\text{ LPA}$), Super Dream ($> 15\text{ LPA}$).
   - **"One Student, One Job" Policy Constraints:** e.g., A student placed in Regular tier can only apply for Dream or Super Dream tiers; a student placed in Dream tier can only apply for Super Dream tier; a student placed in Super Dream is permanently locked.
   - **Backlog Strictness:** University policy on whether active backlog re-evaluations are recognized before official mark sheets.
3. The admin saves the configuration.
4. The admin navigates to "Verification Queue" where students with `Pending TPC Verification` status are queued.
5. The admin compares student self-reported CGPA and backlogs against registrar records, clicks "Verify & Lock Record", and confirms.
6. The system locks the student's academic fields, updates status to `Verified`, and enables full job eligibility evaluations for that student.

#### D. Alternative Flow (Discrepancy Resolution)
- If a student's self-reported CGPA differs from the official transcript, the admin clicks "Reject Verification & Overwrite", inputs the official registrar CGPA, enters an administrative note: *"Corrected CGPA from 8.5 to 7.8 per Semester 6 official gazette"*, and saves.

#### E. Failure Cases
- **Overlapping Tier CTC Brackets:** Admin configures Regular as $\le 8\text{ LPA}$ and Dream as $6-12\text{ LPA}$. System halts saving with: *"Tier compensation brackets must be strictly non-overlapping and contiguous."*

#### F. Expected Result
Institutional placement policies are codified into deterministic system rules, and all student academic records are verified against an indisputable source of truth.

#### G. Acceptance Criteria
- Changes to institutional tier brackets take effect immediately across all newly evaluated job applications.
- Verified academic locks prevent any student-side modification.

---

### 5.5 Universal Application Pipeline Oversight (View Applications)

#### A. Description
Provides TPC Admins with an unrestricted, cross-company view of all job applications submitted across the entire institution, enabling monitoring of recruitment velocity, company shortlists, and student application loads.

#### B. Preconditions
- The TPC Admin is authenticated.

#### C. Main Flow
1. The admin clicks the "Universal Applications" tab.
2. The system renders a master application table displaying: Application ID, Student Name, Roll Number, Department, Company Name, Job Title, Drive Tier, Application Date, Current Stage (`Applied`, `Shortlisted`, `Interview Round 1`, `Offered`, etc.), and Action Logs.
3. The admin utilizes high-level filters to view applications:
   - By Company Drive.
   - By Department / Branch.
   - By Current Application Stage.
   - By Date Range.
4. The admin selects an individual application to inspect its full chronological audit history, including timestamped stage changes, recruiter notes, and interview feedback.

#### D. Alternative Flow
- **Student-Centric Application View:** Admin searches a specific student roll number to view every application submitted by that student across all companies during the academic year.

#### E. Failure Cases
- **High-Volume Query Performance:** In an institution with 10,000+ applications, unfiltered queries must default to paginated views (50 items per page) to prevent browser memory exhaustion.

#### F. Expected Result
TPC administrators maintain absolute transparency over every student-company interaction across the recruitment season.

#### G. Acceptance Criteria
- TPC Admins have read access to all applications across all corporate drives without ownership restrictions.
- All historical stage changes must display the actor (Recruiter, Student, or Admin) and timestamp.

---

### 5.6 Universal Candidate Search & Multi-Parametric Filtering (Filter Candidates)

#### A. Description
Enables TPC Admins to query the global student population using granular, multi-attribute filtering criteria to generate candidate rosters, identify unplaced students, or satisfy urgent corporate recruitment criteria.

#### B. Preconditions
- The TPC Admin is authenticated.

#### C. Main Flow
1. The admin navigates to "Candidate Search & Filter Engine".
2. The admin configures multiple filter parameters:
   - **Academic Filters:** CGPA range (e.g., $7.50$ to $10.00$), Active Backlogs ($= 0$), Cleared Backlogs ($\le 2$), 10th/12th percentages.
   - **Demographic & Institutional Filters:** Department(s) (e.g., Computer Science, Mechanical), Degree (B.Tech, M.Tech), Graduation Year (2026), Gender.
   - **Placement Status Filters:** `Unplaced Only`, `Placed in Regular Tier`, `Placed in Dream Tier`, `Debarred`.
   - **Skill Filters:** Must possess specific skill tags (e.g., `Python` AND `AWS`).
3. The admin clicks "Execute Filter".
4. The system executes the composite database query and displays the matched candidate pool with aggregate counts (e.g., *"142 students match the selected criteria"*).
5. The admin can save the filtered view, export the roster, or broadcast targeted placement notifications to the filtered subset.

#### D. Alternative Flow
- **Unplaced Students Rapid Identification:** The admin applies the one-click preset: "Unplaced Eligible Candidates (CGPA $\ge 6.0$, No Active Backlogs)", instantly generating the list of students needing targeted placement support.

#### E. Failure Cases
- **Mutually Exclusive Filters:** Admin selects conflicting conditions (e.g., CGPA $< 6.0$ AND CGPA $> 8.0$). System returns 0 records with a helpful prompt: *"No candidates match these contradictory filter parameters."*

#### F. Expected Result
Admins can dynamically slice and segment the student body according to any combination of academic, placement, and skill criteria in real time.

#### G. Acceptance Criteria
- Complex composite filters must return query results in under 2 seconds across datasets of up to 10,000 student records.
- Filtering must strictly operate on verified academic data.

---

### 5.7 System-Wide ATS Score & Algorithm Audit (View ATS Results)

#### A. Description
Allows TPC Admins to inspect ATS resume matching scores, examine keyword extraction logs, evaluate algorithmic alignment across drives, and investigate student complaints regarding resume matching fairness.

#### B. Preconditions
- The TPC Admin is authenticated.
- ATS scoring has executed on candidate applications.

#### C. Main Flow
1. The admin opens the "ATS Matching Engine Audit" console.
2. The admin selects a specific recruitment drive or searches for a specific student's application.
3. The system presents the complete ATS diagnostic panel:
   - **Extracted Candidate Resume Text:** Plain-text stream parsed from the student's uploaded PDF.
   - **Indexed Job Description Keywords:** Required skills and normalized tokens (semantic entity extraction deferred to Future Scope).
   - **Calculated Match Score:** Deterministic Jaccard similarity percentage ($0-100\%$).
   - **Algorithmic Weightings Applied:** Raw Jaccard formula for MVP: $\frac{|\text{ResumeSkills} \cap \text{RequiredSkills}|}{|\text{ResumeSkills} \cup \text{RequiredSkills}|}$ (advanced mandatory/preferred weighted scoring deferred to V2).
   - **Matched vs. Missing Keywords:** Explicit lists of identified tokens and missed requirements.
4. The admin reviews the parsing fidelity to confirm whether the score accurately reflects the candidate's resume content.

#### D. Alternative Flow
- **Global Extraction Diagnostics:** The admin views a system-wide log of unparsable PDFs (e.g., scanned image files or corrupted uploads) to instruct affected students to upload compliant, machine-readable documents.

#### E. Failure Cases
- **Missing Extraction Text:** If a PDF parsing failed during application ingestion, the audit view flags: *"Extraction Status: Failed - Empty text layer detected."*

#### F. Expected Result
The placement administration maintains transparency into the automated scoring engine, guaranteeing algorithmic fairness and explainability.

#### G. Acceptance Criteria
- ATS audit logs must show exact keyword match occurrences within the extracted document text.
- TPC Admins can view ATS metrics across any drive without restriction.

---

### 5.8 Administrative Application Stage Management & Overrides (Manage Application Stages)

#### A. Description
Empowers TPC Admins to manually transition, roll back, or cancel candidate application stages in exceptional circumstances (e.g., recruiter technical errors, corporate disputes, or emergency student exemptions) with mandatory administrative audit logging.

#### B. Preconditions
- The TPC Admin is authenticated.
- An application record exists in the system.

#### C. Main Flow
1. The admin searches for the target application in the Universal Applications view.
2. The admin clicks "Administrative Override".
3. The system displays a stage transition selector showing allowed pipeline states: `Applied`, `ATS_Shortlisted`, `Interview_Scheduled` (or specific round), `Offer_Extended`, `Accepted`, `Rejected`, `Withdrawn`, `Administrative Cancel`. (Eligibility status `ELIGIBLE`/`INELIGIBLE` is an independent pre-application evaluation gate and is never an application pipeline stage).
4. The admin selects the target state (e.g., advancing an erroneously omitted candidate to `Shortlisted`).
5. The system prompts for a **Mandatory Audit Reason** (text, minimum 20 characters).
6. The admin enters the justification: *"Candidate inadvertently omitted by recruiter offline; approved by Lead Campus Recruiter via official email confirmation."*
7. The admin clicks "Commit Override".
8. The system updates the application stage, records an immutable entry in the administrative audit log (`admin_id`, `previous_stage`, `new_stage`, `reason`, `timestamp`), and updates the student's application tracking view.

#### D. Alternative Flow (Batch Stage Correction)
- If a recruiter submits an offline Excel shortlist after technical interview rounds, the admin selects multiple candidates via checkboxes, chooses "Batch Stage Override to Round 2", enters the audit justification, and commits the batch update.

#### E. Failure Cases
- **Attempting Override Without Justification:** The admin attempts to commit a stage transition while leaving the audit reason field blank. System blocks the action: *"An explicit administrative audit reason is required for manual stage overrides."*
- **Overriding a Placed Student into Lower Tier:** Admin attempts to force a placed student into an ineligible drive. System displays a policy violation confirmation dialog requiring secondary admin confirmation.

#### F. Expected Result
Administrative interventions can resolve operational anomalies swiftly while maintaining complete regulatory accountability.

#### G. Acceptance Criteria
- Every manual stage override must be permanently recorded in the system audit log.
- Audit records cannot be deleted or modified by any user, including admins.

---

### 5.9 Centralized Interview Coordination & Collision Monitoring (Monitor Interviews)

#### A. Description
Enables TPC Admins to oversee all multi-company interview schedules, detect and resolve candidate interview timing collisions across concurrent company drives, and manage institutional interview infrastructure (campus labs, interview rooms).

#### B. Preconditions
- The TPC Admin is authenticated.
- One or more recruiters have published interview schedules.

#### C. Main Flow
1. The admin navigates to the "Centralized Interview Master Calendar".
2. The system renders an interactive calendar displaying all scheduled interview slots across all active companies.
3. The system's **Automated Collision Detector** highlights conflicting slots in red:
   - *e.g., "Collision Detected: Candidate Rahul Sharma (Roll #1024) is scheduled with Company A at 10:00 AM - 10:45 AM and with Company B at 10:30 AM - 11:15 AM."*
4. The admin clicks on the collision alert.
5. The admin contacts the respective company coordinators through portal messaging and adjusts Rahul's slot in Company B to 11:30 AM.
6. The system updates the interview record, clears the collision warning, and dispatches updated calendar notifications to the candidate and recruiter.

#### D. Alternative Flow (Campus Facility Allocation)
- For on-campus physical drives, the admin assigns designated physical rooms (e.g., "Placement Cell Interview Room 3") to visiting corporate panels, ensuring no physical room is double-booked.

#### E. Failure Cases
- **Overlapping Room Bookings:** Admin attempts to assign two visiting companies to the same physical campus room for the same time window. System flags: *"Facility collision: Room 102 is already booked for Company X."*

#### F. Expected Result
Interview logistics operate without schedule conflicts, eliminating candidate no-shows caused by corporate timing overlaps.

#### G. Acceptance Criteria
- Collision detection algorithm must run automatically upon the creation or rescheduling of any interview slot.
- Real-time alerts must highlight candidate-level and room-level schedule conflicts.

---

### 5.10 Institutional Offer Verification & Policy Lock Enforcement (Monitor Offers)

#### A. Description
Allows TPC Admins to audit, verify, approve, and track all formal employment offers issued by recruiters, ensuring compliance with institutional compensation minimums and programmatically enforcing university placement locking rules.

#### B. Preconditions
- The TPC Admin is authenticated.
- A recruiter has submitted an offer for a selected student.

#### C. Main Flow
1. The admin navigates to the "Offers Management & Policy Lock" dashboard.
2. The system displays offers categorized into: `Pending TPC Verification`, `Active / Pending Student Decision`, `Accepted`, `Declined`, and `Revoked`.
3. The admin reviews an offer in the `Pending TPC Verification` queue:
   - Verifies the attached formal Offer Letter (PDF).
   - Confirms that the Gross CTC matches the drive posting promises.
   - Verifies that the candidate is eligible to receive this offer under the university's "One Job" and Tier Upgrade rules.
4. The admin clicks "Verify & Release Offer to Student".
5. The offer is released to the student's portal with an active decision countdown.
6. When the student clicks "Accept", the system notifies TPC Admin and executes the **Automated Placement Policy Cascade**:
   - Updates student status to `Placed`.
   - Records company name, job title, and CTC against the student's profile.
   - Automatically withdraws the student's active applications from all equal or lower tier drives.
   - Locks the student's profile from applying to lower-tier drives.

#### D. Alternative Flow (Off-Campus Placement Offer Logging)
- If a student secures an off-campus employment offer independently, they submit proof to TPC. The admin reviews the offer letter, clicks "Log Off-Campus Offer", enters company details and CTC, marks the student as `Placed (Off-Campus)`, and applies institutional policy locks.

#### E. Failure Cases
- **Policy Violation Attempt:** A recruiter attempts to issue an offer to a student who has already accepted an equal-tier offer. The system blocks the offer submission with: *"Institutional Policy Violation: Candidate has already accepted an offer in the [Dream] tier."*

#### F. Expected Result
All extended offers are thoroughly vetted for authenticity, and placement rules are enforced deterministically across the institution.

#### G. Acceptance Criteria
- Acceptance of an offer must immediately trigger automated withdrawal from non-qualifying drives.
- Placed student records must be permanently marked with the securing drive's tier and compensation.

---

### 5.11 University-Wide Placement Analytics & Accreditation Reporting (View Placement Analytics)

#### A. Description
Generates comprehensive, real-time institutional analytics, statistical dashboards, and regulatory compliance metrics required for university leadership, public disclosure, and accreditation bodies (e.g., NIRF, NAAC, NBA).

#### B. Preconditions
- The TPC Admin is authenticated.

#### C. Main Flow
1. The admin opens the "Institutional Placement Analytics" suite.
2. The system dynamically computes and renders institutional KPIs:
   - **Overall Placement Percentage:** Total eligible students vs. unique placed students.
   - **Compensation Metrics:** Highest CTC, Average CTC, Median CTC, and Lowest CTC across the graduating batch.
   - **Branch-Wise Placement Breakdown:** Comparative bar charts showing placement percentages and average packages across each academic department (CSE, IT, ECE, ME, CE, etc.).
   - **Tier Distribution:** Proportion of offers categorized by Regular, Dream, and Super Dream tiers.
   - **Corporate Partner Metrics:** Top recruiting organizations, offer counts per company, and year-over-year recruitment volume trends.
   - **Gender & Diversity Statistics:** Placement conversion rates and average packages segmented by gender.
3. The admin filters data by Graduation Year / Batch to compare historical trends across consecutive placement seasons.

#### D. Alternative Flow (Audit Metric Drill-Down)
- The admin clicks on a specific branch bar (e.g., "Mechanical Engineering: 74% Placed") to open a filtered drill-down roster showing which students are placed and which remain actively unplaced.

#### E. Failure Cases
- **Skewed Averages from Outliers:** Extreme outliers (e.g., international offers in foreign currencies) are clearly marked, and median CTC is emphasized alongside mean CTC to prevent distorted reporting.

#### F. Expected Result
University leadership and placement officers gain instantaneous, mathematically accurate analytics for strategic decision-making and statutory compliance.

#### G. Acceptance Criteria
- Calculations must account for unique placed students (preventing duplicate counting of students holding tier upgrade offers).
- Analytics dashboards must load and render aggregated metrics in under 3 seconds.

---

### 5.12 High-Fidelity CSV/Excel Data Export Engine (Export Filtered Candidate/Application Data)

#### A. Description
Enables TPC Admins to generate and export customized, high-fidelity CSV and Excel spreadsheets containing filtered candidate datasets, application rosters, company drive summaries, and accreditation audit tables.

#### B. Preconditions
- The TPC Admin is authenticated.

#### C. Main Flow
1. The admin navigates to the "Reports & Data Export Center".
2. The admin selects an Export Template:
   - `Eligible Candidate Roster for Company Drive` (Roll No, Name, Verified CGPA, 10th %, 12th %, Branch, Phone, Email, Resume Link).
   - `Final Placed Students Gazette` (Roll No, Name, Department, Company Name, Designated Role, CTC Breakdown, Offer Date).
   - `Unplaced Student Intervention Roster` (Roll No, Name, Branch, CGPA, Active Backlogs, Total Applications Submitted).
   - `Statutory Accreditation Audit Sheet (NIRF/NAAC compliant format)`.
3. The admin applies optional filters (e.g., Batch 2026, Department: Computer Science, Minimum CGPA: 7.5).
4. The admin selects file format: `Excel (.xlsx)` or `Comma Separated Values (.csv)`.
5. The admin clicks "Generate & Download Export".
6. The system queries the database, compiles the structured spreadsheet, applies column headers, and streams the file to the admin's device.
7. An entry is recorded in the export audit log (`admin_id`, `template_type`, `record_count`, `timestamp`).

#### D. Alternative Flow (Custom Column Builder)
- The admin selects "Custom Export Builder", selects specific fields from a checkbox tree (e.g., Name, Roll No, Personal Email, ATS Match Score, Placed Company), arranges column sequence, and downloads the customized file.

#### E. Failure Cases
- **Large Dataset Timeout:** Exporting 10,000+ candidate records with extensive historical links causes memory spikes. The system streams records in batches rather than buffering the entire dataset in RAM, ensuring reliable downloads.

#### F. Expected Result
Clean, properly formatted, and audit-ready spreadsheets are exported on-demand, eliminating manual copy-pasting from web portals.

#### G. Acceptance Criteria
- Exported files must contain clean UTF-8 encoding and sanitized cells (preventing CSV formula injection attacks like `=cmd|' /C...`).
- Generated spreadsheets must exactly match official column formatting standards.

---

### 5.13 Content Moderation, Disciplinary Debarment & Record Invalidation (Manage Inappropriate or Invalid Records)

#### A. Description
Provides TPC Admins with the authority to moderate user-generated content, investigate placement misconduct, enforce disciplinary student debarment, and invalidate fraudulent or corrupted records.

#### B. Preconditions
- The TPC Admin is authenticated.

#### C. Main Flow
1. An incident of placement misconduct occurs (e.g., student accepts an on-campus offer and fails to attend joining, or candidate submits a fraudulent forged resume, or an unapproved recruiter posts spam job openings).
2. The admin opens the "Disciplinary & Moderation Console".
3. For student misconduct:
   - The admin searches for the student and selects "Apply Disciplinary Debarment".
   - The admin selects Debarment Scope: `Full Season Debarment` or `Temporary Suspension (e.g., 30 Days)`.
   - The admin inputs mandatory official disciplinary memo details and justification.
   - The admin clicks "Enforce Debarment".
   - The system transitions the student's account state to `Debarred`, revokes their active applications, disables their "Apply" button across all drives, and displays an official debarment notice upon login.
4. For spam or fraudulent job postings:
   - The admin selects the posting and clicks "Invalidate & Purge".
   - The system transitions the drive to `Invalidated / Removed`, alerts affected applicants, and flags the employer account.

#### D. Alternative Flow (Debarment Revocation)
- If the university disciplinary committee resolves the student's appeal and pardons the offense, the admin accesses the debarred student record, clicks "Reinstate Account", inputs the clearance memo number, and restores active placement status.

#### E. Failure Cases
- **Action Without Written Justification:** Admin attempts to debar a student or purge a drive without entering an official justification. System blocks the action: *"Disciplinary actions require formal documented justification."*

#### F. Expected Result
The placement cell maintains strict behavioral standards, ethical integrity, and clean institutional records.

#### G. Acceptance Criteria
- Debarred students cannot submit new applications or attend scheduled interviews.
- Invalidation actions must preserve historical logs for legal and administrative review.

---

## 6. Job & Opportunity Management Functional Requirements

This section defines the operational rules, data schemas, lifecycle state transitions, mutation permissions, and exception policies governing **Job Opportunities and Recruitment Drives**.

---

### 6.1 Field Classification & Data Integrity Rules

Every job opportunity represents either an On-Campus Placement Drive or an Alumni Referral Opportunity. The table below classifies all core and institutional attributes into **Mandatory** vs. **Optional**, including validation rules and consumer dependencies.

| Field Name | Type / Format | Status | Validation Constraints | Business Purpose & System Role |
| :--- | :--- | :--- | :--- | :--- |
| **Job Title** | String ($3-100$ chars) | **Mandatory** | Alphanumeric; sanitized against script tags | Primary title displayed on student job cards and search indexes. |
| **Company / Organization** | Foreign Key (Org ID) | **Mandatory** | Must reference an approved active organization entity | Links posting to corporate branding, recruiter permissions, and past drive histories. |
| **Description** | Rich Text / Markdown ($\ge 50$ chars) | **Mandatory** | Sanitized HTML; minimum 50 characters | Detailed role expectations, team overview, and raw source for ATS keyword parsing. |
| **Required Skills** | Array of Canonical Skill IDs | **Mandatory** | Minimum 1 mandatory skill; max 30 skills | Serves as the benchmark vector for automated ATS candidate matching. |
| **Minimum CGPA** | Decimal ($0.00$ to $10.00$) | **Mandatory** | Numeric, scale 2; defaults to $0.00$ if open to all | Hard gatekeeper; compared against student's verified CGPA. |
| **Eligible Branches** | Array of Branch Codes | **Mandatory** | Non-empty array; must match institutional department catalog | Hard gatekeeper; restricts applications to designated academic majors. |
| **Graduation Year / Batch** | Integer (YYYY) | **Mandatory** | Valid future or active graduating cohort year | Hard gatekeeper; restricts applications to the target graduating class. |
| **Application Deadline** | ISO-8601 Timestamp | **Mandatory** | Must be in the future at the moment of publishing | Programmatic application cut-off; triggers automated drive closure. |
| **Job Status** | Enumeration | **Mandatory** | `DRAFT`, `PENDING_APPROVAL`, `ACTIVE`, `REJECTED_BY_TPC`, `CLOSED`, `ARCHIVED`, `INVALIDATED` | Governs visibility, mutability, and student application permissions. |
| **Recruiter / Creator** | Foreign Key (User ID) | **Mandatory** | Must reference authenticated Recruiter or TPC Admin | Establishes record ownership, audit trail, and management rights. |
| **Drive Category** | Enumeration | **Mandatory** | `On-Campus Drive` or `Alumni Referral` | Controls recruitment workflow (referral vs. full campus interview pipeline). |
| **Gross Annual CTC (INR)** | Decimal / Number | **Mandatory** | Numeric value $> 0$ (e.g., ₹12,00,000) | Determines institutional tier classification and student compensation transparency. |
| **Placement Tier** | Enumeration | **Mandatory** | `Regular`, `Dream`, `Super Dream` | Governs "One Student, One Job" rules and tier upgrade eligibility. |
| **Work Location** | String ($2-100$ chars) | **Mandatory** | Text (e.g., "Bengaluru", "Remote", "Hybrid") | Critical logistical information for student career decisions. |
| **Active Backlog Limit** | Integer ($\ge 0$) | **Optional** | Defaults to $0$ (zero standing backlogs allowed) | Gatekeeper for backlog tolerance; allows recruiters to accommodate backlog students. |
| **Cleared Backlogs Allowed** | Boolean | **Optional** | Defaults to `true` | Dictates whether historical backlogs that were subsequently cleared disqualify candidates. |
| **10th / 12th Cutoff %** | Decimal ($0.0-100.0$) | **Optional** | Defaults to $0.0\%$ (no school-level cutoff) | Secondary academic filter enforced by legacy or engineering consulting firms. |
| **Bond / Service Agreement** | String ($0-250$ chars) | **Optional** | Defaults to "None" | Discloses legal bonding commitments (e.g., "18 Months, ₹1.5 Lakh"). |
| **Tentative Vacancies** | Integer ($> 0$) | **Optional** | Numeric; nullable | Indicative intake number; does not constrain application counts. |
| **Preferred / Bonus Skills** | Array of Skill IDs | **Optional** | Max 20 skills | Secondary keyword vector evaluated for bonus ATS match weighting ($30\%$). |

---

### 6.2 Job Creation & Approval Lifecycle (PENDING_APPROVAL → ACTIVE)

Every recruitment drive or referral posting initiated by a Recruiter or Alumni strictly adheres to an unambiguous two-phase approval lifecycle before it becomes visible or open to student applications:

```
DRAFT
  ↓
PENDING_APPROVAL
  ↓
TPC ADMIN APPROVES (or REJECTS)
  ↓
ACTIVE (or REJECTED_BY_TPC)
  ↓
CLOSED / ARCHIVED
```

**Lifecycle Rules & Constraints:**
1. **Initial Status & Submission:** Recruiter-created jobs submitted for approval MUST have initial status: `PENDING_APPROVAL` (or `DRAFT` if saved in progress prior to submission).
2. **No Immediate Activation:** Recruiter-created jobs MUST NOT become `ACTIVE` immediately after creation or submission.
3. **Approval Authority:** Only an authorized TPC Admin can approve a pending recruiter-created job.
4. **Transition to Active:** Formal TPC Admin approval transitions the status: `PENDING_APPROVAL → ACTIVE`.
5. **Rejection Handling:** TPC Admin may reject a pending job where compensation terms, eligibility criteria, or documentation fail university standards, setting status to `REJECTED_BY_TPC` with mandatory explanatory feedback.
6. **Student Visibility & Application Gating:** Students must not see or apply to jobs that are in `DRAFT`, `PENDING_APPROVAL`, `REJECTED_BY_TPC`, `ARCHIVED`, or `CLOSED` states. Only `ACTIVE` jobs within their deadline window are visible and open for applications.
7. **TPC Direct Publishing:** TPC-created jobs (established by TPC Admin directly on behalf of legacy campus corporate partners) may be published directly to `ACTIVE` according to existing administrative requirements.

---

### 6.3 What Makes a Job Active?

A job opportunity is in an **`ACTIVE`** state if and only if **all** of the following conditions are simultaneously met:

1. **Approved State:** The posting's `status` attribute is explicitly set to `ACTIVE` (following formal review and approval by TPC Admin for recruiter/alumni-created drives, or created directly by TPC Admin).
2. **Current Timestamp Valid:** The current system timestamp is strictly between `opening_date` and `application_deadline`:
   $$\text{opening\_date} \le \text{current\_time} < \text{application\_deadline}$$
3. **Verified Employer Standing:** The sponsoring organization and creator account are in an `Active` standing (not suspended, archived, or blacklisted by TPC).
4. **Institutional Season Active:** The university placement season is currently toggled `Open` by TPC Admin.

**Consequences of `ACTIVE` State:**
- The job card is visible on the student public dashboard feed.
- Eligible students can view real-time ATS match diagnostics.
- The "Apply Now" action button is enabled for verified eligible candidates.

---

### 6.4 Post-Deadline Operational Rules & Lifecycle Behavior

When the system clock crosses the configured `application_deadline` timestamp:

1. **Immediate Programmatic Cut-Off:**
   - The system transitions the job's operational state to **`Closed`**.
   - The application submission API endpoint immediately rejects any pending incoming payload with HTTP 400: *"Application window closed at [timestamp]. Submissions are no longer accepted."*
2. **Student Interface Mutation:**
   - The job card badge updates from green `Active` to amber `Applications Closed`.
   - The "Apply Now" button is permanently disabled and replaced with `Application Window Closed` (or `Applied - Track Status` for candidates who submitted before the deadline).
3. **Recruiter Shortlisting Window Unlocks:**
   - The applicant pool snapshot is finalized.
   - Recruiter tools for batch shortlisting, ATS rank sorting, and interview round creation are fully activated.
4. **TPC Drive Monitoring:**
   - The drive is logged into the TPC Admin closed drives monitor, allowing administrators to audit total applicant counts and schedule interview rounds.

---

### 6.5 Can Students Apply After the Deadline?

**Strict Answer: Absolute NO.**

- **Hard Server-Side Gatekeeping:** The backend API enforces an immutable server-side timestamp check. Even if a student kept their browser window open prior to the deadline, any network submission received after the deadline is atomically rejected.
- **Client-Side Grace Period:** There is zero client-side grace period. Countdown timers synchronize with server NTP time to avoid local device clock manipulation.
- **Single Exception Protocol (Administrative Override Only):** A student cannot apply self-service after the deadline under any circumstance. Only a **TPC Admin** can manually insert an application post-deadline using an explicit Administrative Override, requiring written authorization from the corporate hiring lead and a mandatory recorded audit reason.

---

### 6.6 Mutability Rules: Can a Recruiter Edit a Job After Applications Exist?

Once one or more students have submitted applications to a job posting, recruiter edit capabilities are restricted into two distinct tiers to preserve recruitment integrity:

#### A. Permitted Edits (Informational / Non-Criteria Fields)
The recruiter **is allowed** to edit non-qualifying attributes without requiring re-approval:
- Role Summary & Responsibilities text (minor clarifications).
- Work Location details (e.g., specifying office building/campus).
- Tentative joining dates and interview stage descriptions.
- Extension of Application Deadline: The recruiter may **extend** the deadline to a later future date (shortening an active deadline is prohibited if students have planned around it).

#### B. Restricted / Prohibited Edits (Core Eligibility & Compensation Fields)
The recruiter **cannot unilaterally modify** the following fields once applications exist:
- Minimum CGPA Cutoff.
- Eligible Branches list.
- Graduation Batch year.
- Active Backlog tolerance.
- Offered Gross CTC or Tier Category.

*Rationale:* Permitting recruiters to alter criteria post-submission would retroactively disqualify students who applied in good faith or invalidate institutional tier agreements. Any requested modification to these fields triggers a **"Request Criteria Amendment"** workflow routed to TPC Admin for formal review.

---

### 6.7 Administrative Authority: Can an Admin Edit a Recruiter-Created Job?

**Strict Answer: YES.**

- **Institutional Governance Supremacy:** TPC Admins possess unrestricted administrative authority to edit, modify, pause, or reschedule any job posting, regardless of who created it.
- **Use Cases for Admin Edits:**
  - Correcting erroneous CTC figures or tier classifications.
  - Rectifying branch mapping errors (e.g., adding a newly created departmental specialization).
  - Adjusting deadlines to avoid conflicts with university examination schedules.
  - Adding mandatory institutional disclaimers or bonding regulations.
- **Mandatory Audit Logging:** Whenever a TPC Admin modifies a recruiter-created posting, the system records an immutable audit log entry documenting:
  - `admin_id`
  - Fields modified (with before/after diffs)
  - Mandatory administrative justification
  - Timestamp
- **Stakeholder Notification:** The system automatically dispatches an email alert to the recruiter notifying them of the administrative amendment and the recorded reason.

---

### 6.8 Policy & System Behavior When Eligibility Criteria Change After Applications Exist

In exceptional scenarios where TPC Admin approves an amendment to a job's eligibility criteria (e.g., Company raises CGPA cutoff from $7.0$ to $7.5$, or adds Mechanical Engineering to eligible branches) after applications have been submitted, the platform executes a deterministic **Eligibility Recalculation & Impact Workflow**:

#### A. Automated Re-Evaluation Pipeline
1. The system queues an asynchronous re-evaluation task for all existing applications tied to the drive.
2. The eligibility engine re-runs each applicant's verified academic record against the amended criteria.

#### B. Impact on Already-Applied Candidates
- **Scenario 1: Candidate Becomes Ineligible (Criteria Tightened, e.g., CGPA raised from 7.0 to 7.5):**
  - The candidate's application is not silently deleted.
  - The application status transitions to `REJECTED` (with rejection reason metadata: `REJECTED_CRITERIA_AMENDMENT`), while the student's dynamic eligibility evaluation returns `INELIGIBLE`. (Eligibility is a pre-application gate; it is never represented as an application lifecycle status).
  - The student receives an immediate high-priority notification explaining the revision: *"Company X updated their minimum CGPA requirement from 7.00 to 7.50. Your application has been moved to Rejected (Criteria Amendment)."*
  - Any institutional tier hold or application quota consumed by this application is immediately freed.
- **Scenario 2: Candidate Remains Eligible:**
  - The application remains unaffected in its current lifecycle stage (`APPLIED` or `ATS_SHORTLISTED`).

#### C. Impact on Newly Eligible Candidates (Criteria Relaxed, e.g., Branch Added)
- If criteria are relaxed (e.g., Electrical Engineering added), students in the newly eligible branch receive an automated placement broadcast: *"Company X has expanded eligibility to Electrical Engineering. You are now eligible to apply."*
- If the application deadline had already passed, TPC Admin must configure a mandatory **24-hour Application Window Extension** to afford newly eligible candidates an equitable opportunity to submit resumes.

#### D. Institutional Audit Trail
Every criteria amendment creates a permanent snapshot of:
- Total applicants prior to amendment.
- Count of candidates disqualified by the amendment.
- Count of newly eligible students.
- Recorded TPC Admin authorization memo.

---

## 7. Placement Eligibility Engine Requirements

The **Placement Eligibility Engine** is the deterministic gatekeeping core of the platform. It automatically cross-references a candidate's official, TPC-verified academic records against configured job parameters to guarantee that only qualified, policy-compliant students are permitted to submit applications.

---

### 7.1 Conceptual Distinction: Eligibility Gate vs. Application Lifecycle

Eligibility and Application Lifecycle are two fundamentally distinct domain concepts:

1. **Eligibility (`ELIGIBLE` / `INELIGIBLE`):**
   - Determines whether a student is qualified and permitted to apply for a job drive.
   - Evaluated dynamically prior to application submission.
   - Depends on academic and institutional rules: CGPA cutoff, allowed branches, target batch year, backlog limits, placement tier upgrade rules, and disciplinary standing.
   - Acts as a **pre-application gatekeeper**.
   - `ELIGIBLE` enables the application submission flow; `INELIGIBLE` strictly blocks submission.

2. **Application Status (`APPLIED` → `ATS_SHORTLISTED` → `INTERVIEW_SCHEDULED` → `OFFER_EXTENDED` → `ACCEPTED`):**
   - Represents what happens to a candidate's submission *after* an eligible student applies.
   - Includes terminal states: `REJECTED`, `WITHDRAWN`, `AUTO_WITHDRAWN`, `DECLINED`.
   - Never contains `ELIGIBLE` or `INELIGIBLE`.

**Core Architectural Rules:**
1. A student can only apply if the eligibility evaluation returns `ELIGIBLE`.
2. `INELIGIBLE` students are strictly prevented from submitting applications.
3. Eligibility status must NOT be represented as an application status.
4. `ELIGIBLE` / `INELIGIBLE` must not be mixed with `APPLIED`, `ATS_SHORTLISTED`, `INTERVIEW_SCHEDULED`, `OFFER_EXTENDED`, or `ACCEPTED`.
5. Once an eligible student applies, the application record is created with initial status: `APPLIED`.
6. ATS processing changes the application's progression in the application lifecycle; it does not redefine student eligibility.

---

### 7.2 Codified Eligibility Rules

The engine evaluates a composite boolean predicate composed of eight distinct evaluation rules. A student is declared **`ELIGIBLE`** if and only if **all eight rules evaluate to `TRUE`**.

| Rule Identifier | Parameter Name | Logic / Formula | Rule Type | Business Context & Policy Intent |
| :--- | :--- | :--- | :--- | :--- |
| **RULE-01** | **CGPA Cutoff** | $\text{Student.VerifiedCGPA} \ge \text{Job.MinCGPA}$ | Mandatory Hard Gate | Ensures academic merit meets corporate cutoff; uses verified transcript CGPA only. |
| **RULE-02** | **Branch / Department** | $\text{Student.Branch} \in \text{Job.EligibleBranches}$ | Mandatory Hard Gate | Restricts applications to authorized degree specializations (e.g., CSE, IT, ECE). |
| **RULE-03** | **Graduation Cohort** | $\text{Student.BatchYear} == \text{Job.TargetBatch}$ | Mandatory Hard Gate | Restricts drives to target graduating classes (e.g., 2026 graduating batch). |
| **RULE-04** | **Active Backlog Cap** | $\text{Student.ActiveBacklogs} \le \text{Job.MaxActiveBacklogs}$ | Mandatory Hard Gate | Enforces corporate zero-backlog or capped standing backlog tolerances. |
| **RULE-05** | **Historical Backlogs** | If $\text{Job.AllowClearedBacklogs} == \text{False}$, then $\text{Student.HistoryBacklogs} == 0$ | Optional Hard Gate | Filters out candidates who ever had a backlog, even if subsequently cleared. |
| **RULE-06** | **Secondary Schooling** | $\text{Student.10thPct} \ge \text{Job.Min10thPct} \land \text{Student.12thPct} \ge \text{Job.Min12thPct}$ | Optional Hard Gate | Enforces classic legacy recruiter "60% or 70% throughout academic career" criteria. |
| **RULE-07** | **Institutional Policy & Tier Cap** | CanApply(Student.CurrentPlacementTier, Job.Tier) | Institutional Hard Gate | Enforces university "One Student, One Job" and CTC tier upgrade rules (e.g., Regular $\rightarrow$ Dream $\rightarrow$ Super Dream). |
| **RULE-08** | **Disciplinary Standing** | $\text{Student.AccountStatus} \neq \text{Debarred}$ | Governance Hard Gate | Blocks students under temporary or seasonal placement disciplinary debarment. |

---

### 7.3 End-to-End Eligibility Evaluation Flow

The eligibility evaluation executes server-side across five sequential phases:

```
[Student Requests Job Details / Dashboard]
                  │
                  ▼
       [1. Verification Check]
   Is Student Academic Profile Verified?
          ├── NO  ──► Status: UNVERIFIED (Evaluation Halted)
          │           (Banner: "Pending TPC Verification")
          └── YES
                  │
                  ▼
       [2. Disciplinary Check]
       Is Student Active & Non-Debarred?
          ├── NO  ──► Status: DEBARRED (Evaluation Halted)
          │           (Banner: "Debarred by TPC")
          └── YES
                  │
                  ▼
       [3. Placement Policy Check]
   Does Student's Placement Tier Permit Applying?
          ├── NO  ──► Status: POLICY BLOCKED
          │           (Banner: "Placed in Higher/Equal Tier")
          └── YES
                  │
                  ▼
       [4. Academic Matrix Evaluation]
   Evaluate CGPA, Branch, Batch, Backlogs, Schooling
          ├── ANY FAIL ──► Status: NOT ELIGIBLE
          │                (Granular Failure Reasons Logged)
          └── ALL PASS
                  │
                  ▼
       [5. Final Classification: ELIGIBLE]
       - Badge: "Eligible to Apply" (Green)
       - "Apply Now" Button Unlocked
       - ATS Diagnostic Preview Enabled
```

---

### 7.4 System Behavior When a Student is ELIGIBLE

When all criteria evaluate to `TRUE`:

1. **Visual State:** The job listing card and job detail view render a prominent green badge: `Eligible to Apply`.
2. **Action Enablement:** The primary call-to-action button, "Apply Now", is rendered in an enabled, interactive state.
3. **ATS Alignment Access:** The student can click "Preview ATS Match" to analyze resume keyword alignment and skill gaps before applying.
4. **Application Payload Caching:** The system pre-compiles the verified academic snapshot to be bundled with the application payload upon submission.

---

### 7.5 System Behavior When a Student is NOT ELIGIBLE

When one or more criteria evaluate to `FALSE`:

1. **Visual State:** The job card renders an amber/red badge: `Not Eligible to Apply`.
2. **Action Disablement:** The "Apply Now" button is rendered in a **permanently disabled state** (grayed out with a lock icon).
3. **Transparent Reason Breakdown:** The system displays an expandable "Eligibility Diagnostics" drawer explicitly detailing every criterion that failed (see Section 7.10).
4. **Backend Gating (Security):** The backend application submission route (`POST /api/student/applications`) independently re-evaluates all eligibility rules upon receiving any payload. If an ineligible student bypasses client-side controls (e.g., via direct API invocation), the server immediately rejects the request with HTTP 403 Forbidden: *"Application rejected: You do not meet the institutional eligibility criteria for this drive."*

---

### 7.6 Can Ineligible Students See the Job?

**Strict Answer: YES.**

- **Full Transparency Policy:** Ineligible students **can view** all active, approved job postings and full role specifications.
- **Pedagogical & Career Value:** Allowing ineligible students to view postings provides essential visibility into industry hiring standards, compensation benchmarks, and required technical skills, encouraging students to upskill for future drives.
- **Feed Customization:** To prevent student fatigue during high-volume placement weeks, the job feed provides a persistent toggle switch:
  `[ ] Show Only Drives I Am Eligible For`
  When toggled, ineligible drives are filtered out of the active feed view.

---

### 7.7 Can Ineligible Students Apply?

**Strict Answer: Absolute NO.**

- Under no circumstance can an ineligible student submit an application self-service.
- Client-side buttons are disabled, and server-side route handlers enforce transactional eligibility verification before persisting any application record.
- **Sole Exception:** If a student believes their academic records were miscalculated, they must submit a formal re-verification request to TPC Admin. Only TPC Admin can correct the record or issue an administrative exception override.

---

### 7.8 What Happens When Student Information Changes?

When a student's academic profile is updated (e.g., semester examination results declared, backlog cleared, or TPC Admin updates CGPA):

1. **Verification Requirement:** The student cannot self-update their verified metrics during active drives. Grade revisions require TPC Admin verification against official transcripts.
2. **Cache Invalidation & Real-Time Re-evaluation:** Once TPC Admin approves and verifies the updated grade, the system automatically invalidates cached eligibility flags for that student.
3. **Dynamic State Shift:**
   - If a student's improved CGPA (e.g., from 7.2 to 7.8) now satisfies a previously closed drive (minimum CGPA 7.5), the job card status immediately flips from `Not Eligible` to `Eligible to Apply`.
   - The "Apply Now" button dynamically unlocks.
4. **Automated Opportunity Alert:** The system dispatches an instant in-app placement notification: *"Good news! Following your recent academic grade verification, you are now ELIGIBLE to apply for [Company Name] - [Role Title]. Application deadline closes in [X] days."*

---

### 7.9 What Happens When Job Eligibility Changes?

If a recruiter or TPC Admin updates the eligibility criteria of an active job drive (as governed by Section 6.8):

1. **Instant Candidate Re-Calculation:** The eligibility engine executes an asynchronous sweep across all registered students and existing applicants for that drive.
2. **Existing Applicants Re-Checked:**
   - Any applicant who fails the tightened criteria has their application transitioned to `REJECTED` (with rejection reason: `REJECTED_CRITERIA_AMENDMENT`) while their dynamic eligibility status for the drive evaluates to `INELIGIBLE`, with placement quotas restored. (Eligibility status is never represented as an application status).
3. **Unapplied Student Feed Updates:**
   - All student dashboard cards instantly reflect the amended criteria without requiring cache flushes.
   - Newly eligible students receive immediate broadcast alerts, and application deadlines are extended if required.

---

### 7.10 Explainability Architecture & Concrete Result Presentation

The platform treats eligibility as a transparent diagnostic, never a black-box rejection. For every job drive, the student can view an explicit comparative breakdown table comparing **Required Criteria** vs. **Your Verified Credentials**.

#### Example 1: Fully Eligible Student (Clean Pass)

```
Candidate Profile:
- Name: Priya Patel (Roll #2022-CSE-084)
- Verified CGPA: 8.20 / 10.00
- Branch: Computer Science & Engineering (CSE)
- Graduation Batch: 2026
- Active Backlogs: 0
- Placement Standing: Unplaced

Job Criteria: Google Cloud - Software Engineer (Early Career)
- Minimum CGPA: 7.50
- Allowed Branches: [CSE, IT, ECE]
- Graduation Batch: 2026
- Maximum Active Backlogs: 0
- Tier Category: Super Dream (> ₹15 LPA)

========================= ELIGIBILITY DIAGNOSTIC =========================
| Criterion             | Required Threshold | Your Verified Record | Status  |
| :-------------------- | :----------------- | :------------------- | :------ |
| Minimum CGPA          | >= 7.50            | 8.20                 | [PASS]  |
| Department / Branch   | CSE, IT, ECE       | CSE                  | [PASS]  |
| Graduation Cohort     | 2026 Batch         | 2026 Batch           | [PASS]  |
| Active Backlogs       | 0 Allowed          | 0 Active             | [PASS]  |
| Placement Tier Policy | Super Dream Open   | Unplaced (Eligible)  | [PASS]  |
==========================================================================
FINAL EVALUATION RESULT: [ELIGIBLE TO APPLY]
Action: "Apply Now" button ENABLED.
```

---

#### Example 2: Ineligible Student with Multiple Discrepancies (Detailed Explainability)

```
Candidate Profile:
- Name: Rohan Verma (Roll #2022-ME-112)
- Verified CGPA: 7.15 / 10.00
- Branch: Mechanical Engineering (ME)
- Graduation Batch: 2026
- Active Backlogs: 1 (Kinematics of Machines)
- Placement Standing: Placed (Regular Tier - ₹4.5 LPA)

Job Criteria: Microsoft - Cloud Support Associate
- Minimum CGPA: 7.50
- Allowed Branches: [CSE, IT, ECE, EE]
- Graduation Batch: 2026
- Maximum Active Backlogs: 0
- Tier Category: Regular Tier (₹6.5 LPA)

========================= ELIGIBILITY DIAGNOSTIC =========================
| Criterion             | Required Threshold | Your Verified Record | Status  |
| :-------------------- | :----------------- | :------------------- | :------ |
| Minimum CGPA          | >= 7.50            | 7.15                 | [FAIL]  |
| Department / Branch   | CSE, IT, ECE, EE   | ME                   | [FAIL]  |
| Graduation Cohort     | 2026 Batch         | 2026 Batch           | [PASS]  |
| Active Backlogs       | 0 Allowed          | 1 Active Backlog     | [FAIL]  |
| Placement Tier Policy | Tier Upgrade Only  | Placed in Regular    | [FAIL]  |
==========================================================================
FINAL EVALUATION RESULT: [NOT ELIGIBLE TO APPLY]
Action: "Apply Now" button DISABLED (Locked).

Detailed Failure Explanations:
1. Academic Cutoff: Verified CGPA (7.15) is below the minimum required cutoff of 7.50.
2. Department Restriction: Mechanical Engineering is not included in the eligible branches for this drive.
3. Backlog Cap: Role requires 0 active standing backlogs; you currently have 1 active backlog.
4. Policy Constraint: You have already accepted an offer in the Regular tier (₹4.5 LPA). Under university placement guidelines, you may only apply for Dream (>= ₹7 LPA) or Super Dream (>= ₹15 LPA) drives.
```

---

## 8. Application Lifecycle & State Transition Engine

The **Application Lifecycle** models the progression of a student's job candidacy from initial submission through automated screening, interview rounds, offer negotiations, and final institutional placement locking.

The lifecycle consists of 5 primary progression states and 4 terminal/contingency states:
- **Primary Progression:** `APPLIED` $\rightarrow$ `ATS_SHORTLISTED` $\rightarrow$ `INTERVIEW_SCHEDULED` $\rightarrow$ `OFFER_EXTENDED` $\rightarrow$ `ACCEPTED`
- **Terminal & Contingency States:** `REJECTED`, `WITHDRAWN`, `DECLINED`, `AUTO_WITHDRAWN`

> [!IMPORTANT]
> **Separation of Concerns: Application Status vs. Eligibility Gate**
> Eligibility determines whether a student is permitted to apply for a job (`ELIGIBLE` vs. `INELIGIBLE`). Application status represents what happens *after* an eligible student applies. `ELIGIBLE` and `INELIGIBLE` must never be represented as application statuses or mixed into the application lifecycle. Once an eligible student applies, the application status is initialized strictly to `APPLIED`.

```
                          ┌───────────────┐
                          │    APPLIED    ├──────────────────────────┐
                          └──┬─────────┬──┘                          │
                             │         │ (Withdraw)                  │ (Reject)
           (Recruiter / ATS) │         ▼                             │
                             │  ┌─────────────┐                      │
                             │  │  WITHDRAWN  │                      │
                             │  └─────────────┘                      │
                             ▼                                       │
                   ┌───────────────────┐                             │
                   │  ATS_SHORTLISTED  ├─────────────────────────────┤
                   └─────────┬─────────┘                             │
                             │                                       │
          (Schedule Round 1) │                                       │
                             ▼                                       │
                ┌─────────────────────────┐                          │
       ┌────────┤   INTERVIEW_SCHEDULED   ├──────────────────────────┤
       │        └────────────┬────────────┘                          │
       │ (Next Round)        │                                       │
       └─────────────────────┘ (Final Round Cleared)                 │
                             │                                       │
                             ▼                                       ▼
                   ┌───────────────────┐                      ┌──────────────┐
                   │  OFFER_EXTENDED   ├─────────────────────►│   REJECTED   │
                   └───┬───────────┬───┘       (Reject)       └──────────────┘
                       │           │
     (Student Accepts) │           │ (Student Declines)
                       ▼           ▼
                 ┌──────────┐ ┌──────────┐
                 │ ACCEPTED │ │ DECLINED │
                 └────┬─────┘ └──────────┘
                      │
                      ▼
         [Trigger Institutional Cascade]
    All lower/equal tier active applications
             transition to:
           ┌────────────────┐
           │ AUTO_WITHDRAWN │
           └────────────────┘
```

---

### 8.1 Granular State Specifications

---

#### State 1: `APPLIED`

* **Meaning:** The student has successfully submitted their verified academic profile and selected resume to an active, eligible job drive. The application is officially queued in the recruiter's applicant pool.
* **Who Can Transition Into It:** **Student** (self-service upon meeting all eligibility criteria); or **TPC Admin** (manual override).
* **Who Can Transition Out of It:** **Recruiter** (shortlist or reject); **Student** (withdraw prior to deadline); **TPC Admin** (override/reject).
* **Allowed Next States:** `ATS_SHORTLISTED`, `REJECTED`, `WITHDRAWN`, `AUTO_WITHDRAWN`.
* **Notifications Triggered:**
  * To Student: *"Application submitted successfully for [Role] at [Company]."*
  * To Recruiter: Real-time applicant counter increments on drive dashboard.
* **Information Recorded:**
  * `application_id`, `student_id`, `job_id`, `resume_id` (snapshot reference).
  * Candidate academic snapshot at submission (CGPA, active backlogs, department).
  * `submission_timestamp`, `ip_address`, `initial_ats_score`.
* **Student Modification Rights:** Can withdraw application **only** if the drive deadline has not passed. Cannot edit submitted resume or academic snapshot.
* **Recruiter / Admin Modification Rights:** Recruiter can add private evaluation notes, view verified candidate details, and trigger ATS ranking.

---

#### State 2: `ATS_SHORTLISTED`

* **Meaning:** The candidate has passed automated ATS score threshold filters or has been manually flagged by the corporate recruiter as a qualified applicant to advance to selection rounds.
* **Who Can Transition Into It:** **Recruiter** (manual/batch shortlist action); **System** (if automated threshold auto-shortlisting is configured); **TPC Admin** (administrative override).
* **Who Can Transition Out of It:** **Recruiter** (schedule interview or reject); **TPC Admin** (override/cancel).
* **Allowed Next States:** `INTERVIEW_SCHEDULED`, `REJECTED`, `AUTO_WITHDRAWN`.
* **Notifications Triggered:**
  * To Student: *"Congratulations! You have been shortlisted for [Role] at [Company]. Interview schedule details will follow."*
  * To TPC Admin: Drive shortlist count logged into administrative monitor.
* **Information Recorded:**
  * `shortlisted_timestamp`, `shortlisting_actor_id` (recruiter user ID or `SYSTEM_ATS`).
  * ATS match score at shortlisting time, matched keywords array.
* **Student Modification Rights:** Strictly Read-Only. Cannot withdraw self-service without formal TPC intervention.
* **Recruiter / Admin Modification Rights:** Recruiter can assign candidate to specific interview panels, change internal priority rankings, and add round notes.

---

#### State 3: `INTERVIEW_SCHEDULED`

* **Meaning:** The candidate has been assigned to an active selection stage (e.g., Coding Assessment, Technical Round 1, System Design, HR Interview) with confirmed date, time slot, and venue/meeting URL.
* **Who Can Transition Into It:** **Recruiter** (upon publishing interview schedule); **TPC Admin** (pooled campus drive scheduling).
* **Who Can Transition Out of It:** **Recruiter** (advance to subsequent round, extend offer, or reject); **TPC Admin** (reschedule/cancel).
* **Allowed Next States:** `INTERVIEW_SCHEDULED` (re-entrant for multi-round progression, e.g., Round 1 $\rightarrow$ Round 2), `OFFER_EXTENDED`, `REJECTED`, `AUTO_WITHDRAWN`.
* **Notifications Triggered:**
  * To Student: *"Interview Scheduled: [Round Name] with [Company] on [Date] at [Time]. Confirm your attendance."*
  * To Recruiter: Candidate slot assignment confirmation.
* **Information Recorded:**
  * `interview_round_id`, `round_name`, `round_type` (`Campus Physical` vs. `Virtual`).
  * `slot_start_time`, `slot_end_time`, `room_number` or `meeting_url`.
  * Candidate attendance confirmation status (`Pending`, `Confirmed`, `Reschedule Requested`).
  * Recruiter scorecard ratings (1–5 scale across technical rubrics) and written round remarks.
* **Student Modification Rights:** Can click "Confirm Attendance" or submit a formal "Reschedule Request" (with mandatory written academic justification routed to TPC).
* **Recruiter / Admin Modification Rights:** Recruiter can reschedule slot, update meeting URL, change room allocation, and log scorecard feedback.

---

#### State 4: `OFFER_EXTENDED`

* **Meaning:** The candidate has successfully cleared all interview stages. The recruiter has issued a formal employment offer with detailed CTC breakdowns, validated by TPC Admin, and awaiting student decision.
* **Who Can Transition Into It:** **Recruiter** (issues offer terms and uploads offer letter); **TPC Admin** (validates and releases offer).
* **Who Can Transition Out of It:** **Student** (Accepts or Declines); **Recruiter / TPC Admin** (Offer revocation in exceptional disputes).
* **Allowed Next States:** `ACCEPTED`, `DECLINED`, `REJECTED` (Administrative Revocation).
* **Notifications Triggered:**
  * To Student: High-priority banner + email: *"Official Job Offer Received from [Company]! CTC: ₹[Amount]. Decision required within [Hours] hours."*
  * To Recruiter & TPC Admin: Offer delivery confirmed; countdown timer initiated.
* **Information Recorded:**
  * `offer_id`, `offer_letter_url` (secure signed cloud PDF).
  * Itemized compensation: Gross Annual CTC, Fixed Base, Performance Bonus, Joining Bonus, Stipend.
  * `offer_issued_timestamp`, `decision_deadline_timestamp` (e.g., 72 hours from release).
* **Student Modification Rights:** Student can perform exactly one binding action: click `Accept Offer` (with mandatory confirmation) or `Decline Offer` (with mandatory written reason).
* **Recruiter / Admin Modification Rights:** Can modify decision expiration deadline or revoke offer with formal documented justification prior to student acceptance.

---

#### State 5: `ACCEPTED` (Terminal Success)

* **Meaning:** The student has formally accepted the employment offer within the designated decision window. The candidate is officially marked as **`PLACED`**, triggering institutional policy locks across the entire portal.
* **Who Can Transition Into It:** **Student** (via explicit confirmation modal typing "ACCEPT").
* **Who Can Transition Out of It:** **None** (Terminal State; irreversible except via formal University Disciplinary Committee action).
* **Allowed Next States:** None (Terminal State).
* **Notifications Triggered:**
  * To Student: *"Offer Acceptance Confirmed! Congratulations on your placement at [Company]."*
  * To Recruiter: *"Candidate [Name] has accepted your job offer."*
  * To TPC Admin: Placement statistics updated immediately; student gazette entry created.
* **Information Recorded:**
  * `acceptance_timestamp`, `acceptance_ip_address`, `digital_signature_acknowledgment`.
  * Cascading policy log: List of auto-withdrawn competing application IDs.
* **Student Modification Rights:** Strictly Read-Only. Cannot apply to lower or equal tier drives. Cannot accept secondary offers unless qualifying for an institutional Dream Tier upgrade.
* **Recruiter / Admin Modification Rights:** TPC Admin verifies and locks student placed profile in university records. Recruiter gains access to post-offer candidate onboarding package.

---

#### State 6: `DECLINED` (Terminal Student Rejection)

* **Meaning:** The student was extended an official offer but formally declined to accept it within the decision window.
* **Who Can Transition Into It:** **Student** (clicks Decline Offer); or **System** (auto-declined upon decision window expiration).
* **Who Can Transition Out of It:** **None** (Terminal State).
* **Allowed Next States:** None (Terminal State).
* **Notifications Triggered:**
  * To Recruiter: *"Candidate [Name] has declined the employment offer. Reason: [Recorded Reason]."*
  * To TPC Admin: Decline alert logged; candidate tier quota decremented per institutional policy.
* **Information Recorded:**
  * `decline_timestamp`, `decline_reason_category` (e.g., "Higher Studies", "Location Preference", "Competing Offer"), `written_justification`.
* **Student Modification Rights:** Strictly Read-Only.
* **Recruiter / Admin Modification Rights:** Recruiter can immediately re-allocate the offer to a waitlisted candidate.

---

#### State 7: `REJECTED` (Terminal Disqualification)

* **Meaning:** The candidate was evaluated at screening or interview stages and was not selected to advance further in this recruitment drive.
* **Who Can Transition Into It:** **Recruiter** (marks candidate as rejected at any stage); **TPC Admin** (disqualification override).
* **Who Can Transition Out of It:** **TPC Admin** (administrative appeal reinstatement only).
* **Allowed Next States:** None (Terminal State, except via administrative dispute override to `APPLIED` / `SHORTLISTED`).
* **Notifications Triggered:**
  * To Student: Formal professional notification: *"Update regarding your application for [Role] at [Company]: You have not been selected to advance. We encourage you to apply for upcoming drives."*
* **Information Recorded:**
  * `rejection_timestamp`, `rejection_stage` (`Screening`, `Interview Round 1`, `Final HR`).
  * `rejection_actor_id`, optional internal recruiter feedback notes.
* **Student Modification Rights:** Strictly Read-Only.
* **Recruiter / Admin Modification Rights:** Read-only historical record.

---

#### State 8: `WITHDRAWN` (Terminal Student Voluntary Exit)

* **Meaning:** The student voluntarily withdrew their application before the application deadline closed and before shortlisting began.
* **Who Can Transition Into It:** **Student** (clicks "Withdraw Application" during active window).
* **Who Can Transition Out of It:** **Student** (can re-apply if drive remains active and deadline has not passed).
* **Allowed Next States:** `APPLIED` (Re-application prior to deadline only).
* **Notifications Triggered:**
  * To Student: *"Your application for [Role] at [Company] has been withdrawn."*
  * To Recruiter: Candidate removed from active screening pool.
* **Information Recorded:**
  * `withdrawn_timestamp`, `withdrawal_reason`.
* **Student Modification Rights:** Can re-submit application prior to deadline.
* **Recruiter / Admin Modification Rights:** Read-only historical record.

---

#### State 9: `AUTO_WITHDRAWN` (Terminal Policy Cascade)

* **Meaning:** The application was automatically closed and withdrawn by the platform because the student accepted a job offer with another company, invoking institutional placement rules (e.g., "One Student, One Job" or Tier Cap locks).
* **Who Can Transition Into It:** **System** (automated policy engine cascade triggered upon student transitioning any offer to `ACCEPTED`).
* **Who Can Transition Out of It:** **None** (Terminal State).
* **Allowed Next States:** None (Terminal State).
* **Notifications Triggered:**
  * To Student: *"Under university placement policy, your application for [Company B] has been automatically withdrawn following your accepted offer with [Company A]."*
  * To Recruiter: *"Candidate [Name] has been placed with another organization and withdrawn by placement policy."*
* **Information Recorded:**
  * `auto_withdrawn_timestamp`, `triggering_offer_id`, `triggering_company_name`.
  * Policy rule reference (e.g., `"POLICY_ONE_STUDENT_ONE_JOB"`).
* **Student Modification Rights:** Strictly Read-Only.
* **Recruiter / Admin Modification Rights:** Recruiter can immediately backfill the interview slot or shortlist from waitlisted candidates.

---

### 8.2 Comprehensive State Transition Table

The table below defines every valid transition, enforcing strict state invariants across the platform:

| Current State | Triggering Event / Action | Acting Role | Next State | Guard Conditions / Preconditions | Post-Conditions & System Cascades |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **NONE** | Submit Application | Student | `APPLIED` | Student verified; eligible; drive active; deadline valid; not placed. | Application record created; resume snapshot saved; ATS score computed. |
| **APPLIED** | Recruiter Shortlists | Recruiter | `ATS_SHORTLISTED` | Drive applications closed or rolling; candidate eligible. | Student notified; candidate added to shortlist pool. |
| **APPLIED** | Recruiter Rejects | Recruiter | `REJECTED` | None. | Application terminated; candidate notified. |
| **APPLIED** | Student Withdraws | Student | `WITHDRAWN` | Drive deadline has not passed; shortlisting not finalized. | Application marked withdrawn; quota released. |
| **APPLIED** | Candidate Placed Elsewhere | System | `AUTO_WITHDRAWN` | Student accepts competing offer of equal/higher tier. | Application closed; recruiter notified of candidate placement. |
| **ATS_SHORTLISTED** | Schedule Interview Round | Recruiter / TPC | `INTERVIEW_SCHEDULED` | Round details configured; slot assigned; no interview collision. | Interview record generated; candidate calendar alert dispatched. |
| **ATS_SHORTLISTED** | Recruiter Rejects | Recruiter | `REJECTED` | None. | Application terminated; candidate notified. |
| **ATS_SHORTLISTED** | Candidate Placed Elsewhere | System | `AUTO_WITHDRAWN` | Student accepts competing offer of equal/higher tier. | Candidate removed from shortlist pool; recruiter notified. |
| **INTERVIEW_SCHEDULED** | Advance to Next Round | Recruiter | `INTERVIEW_SCHEDULED` | Candidate cleared previous round; subsequent round configured. | Prior round scorecard archived; new slot credentials issued. |
| **INTERVIEW_SCHEDULED** | Final Round Cleared $\rightarrow$ Issue Offer | Recruiter | `OFFER_EXTENDED` | All rounds cleared; offer letter attached; TPC verified. | Offer timer started (72h); student receives decision modal. |
| **INTERVIEW_SCHEDULED** | Fail Round $\rightarrow$ Reject | Recruiter | `REJECTED` | Round scorecard evaluated as `Failed`. | Application terminated; candidate notified. |
| **INTERVIEW_SCHEDULED** | Candidate Placed Elsewhere | System | `AUTO_WITHDRAWN` | Student accepts competing offer of equal/higher tier. | Scheduled slot cancelled; candidate removed from interview roster. |
| **OFFER_EXTENDED** | Student Accepts Offer | Student | `ACCEPTED` | Decision window active; student confirms binding acceptance. | **CASCADE:** Student status $\rightarrow$ `Placed`; all conflicting apps $\rightarrow$ `AUTO_WITHDRAWN`. |
| **OFFER_EXTENDED** | Student Declines Offer | Student | `DECLINED` | Decision window active; mandatory justification provided. | Offer closed; recruiter alerted; tier quota updated. |
| **OFFER_EXTENDED** | Decision Window Expires | System | `DECLINED` | Decision timestamp crossed with no response. | Offer auto-expires; candidate and recruiter alerted. |
| **OFFER_EXTENDED** | Administrative Revocation | TPC Admin / Recruiter | `REJECTED` | Documented dispute or fraud before student acceptance. | Offer rescinded; audit memo recorded. |
| **WITHDRAWN** | Re-apply to Drive | Student | `APPLIED` | Application deadline still active; student remains eligible. | New application record logged; status reset to `APPLIED`. |
| **REJECTED** | Administrative Appeal | TPC Admin | `ATS_SHORTLISTED` | Documented recruiter error; written TPC override memo. | Candidate reinstated; audit log entry recorded. |
| **ACCEPTED** | Disciplinary Revocation | TPC Admin | `REJECTED` | University disciplinary committee ruling (e.g., student fraud). | Placed status revoked; disciplinary debarment enforced. |
| **AUTO_WITHDRAWN** | None | N/A | *Terminal* | Immutable state. | Read-only historical archive. |

---

## 9. ATS Resume Matcher Engine Requirements (Deterministic Scoring Pipeline)

The **Auto-Resume Matcher** is a deterministic, explainable Applicant Tracking System (ATS) engine designed to extract plain text and technical skills from student PDF resumes, evaluate them against structured job descriptions, compute an objective match score, and present transparent alignment diagnostics to both students and recruiters.

In MVP, the engine operates strictly on **deterministic text normalization, canonical skill extraction with documented aliases (preprocessing), and pure Jaccard similarity scoring**. Preprocessing steps (lowercase, whitespace, punctuation normalization, canonical skill mapping, and deterministic alias resolution) normalize tokens prior to set comparison and MUST NOT be described or treated as separate scoring algorithms. **Jaccard is the ONLY MVP scoring algorithm.** Advanced techniques (weighted scoring, frequency weighting, fuzzy string matching, semantic embeddings, and LLM scoring) are deferred to V2 / Future Scope to guarantee sub-second execution speeds, mathematical transparency, and complete repeatability.

---

### 9.1 Engine Inputs

The ATS matching engine accepts two primary inputs per evaluation transaction:

#### A. Input 1: Student Resume Document
- **Format:** Portable Document Format (`.pdf`).
- **Maximum File Size:** 5 MB.
- **Content:** Text-based resume document containing candidate contact info, education, technical skills, projects, internships, and certifications.
- **Origin:** Uploaded by authenticated student; verified for MIME type (`application/pdf`).

#### B. Input 2: Job Description & Skill Specification
- **Format:** Structured posting record.
- **Components:**
  - Raw Job Description (Plain-text/Markdown narrative).
  - Explicit Required Skills Set ($J$): Normalized canonical skill IDs tagged as required (e.g., `['python', 'postgresql', 'docker']`) used directly by the MVP Jaccard scoring formula.
  - Configured Skill Weights & Partitioning (V2 Scope): Optional Mandatory Skills Vector ($M$, default weight $70\%$) and Preferred Skills Vector ($P$, default weight $30\%$) used only in V2 weighted scoring extensions.

---

### 9.2 End-to-End Processing Pipeline

The matching pipeline executes across eight sequential processing phases:

```
┌─────────────────────────┐       ┌─────────────────────────┐
│   Student Resume PDF    │       │   Job Description +     │
│   (Cloud Storage URL)   │       │   Target Skill Vectors  │
└────────────┬────────────┘       └────────────┬────────────┘
             │                                 │
             ▼                                 │
┌─────────────────────────┐                    │
│ Phase 1: PDF Validation │                    │
│ & Security Scanning     │                    │
└────────────┬────────────┘                    │
             │                                 │
             ▼                                 │
┌─────────────────────────┐                    │
│ Phase 2: Plain-Text     │                    │
│ Extraction (pdf-parse)  │                    │
└────────────┬────────────┘                    │
             │                                 │
             ▼                                 │
┌─────────────────────────┐                    │
│ Phase 3: Text Clean-up  │                    │
│ & Normalization         │                    │
└────────────┬────────────┘                    │
             │                                 │
             ▼                                 │
┌─────────────────────────┐                    ▼
│ Phase 4: Skill Keyword  │       ┌─────────────────────────┐
│ Taxonomy Tokenization   ├──────►│ Phase 5: Target Skill   │
│ (Extracted Vector R)    │       │ Vector Compilation (J)  │
└────────────┬────────────┘       └────────────┬────────────┘
             │                                 │
             └────────────────┬────────────────┘
                              │
                              ▼
                 ┌─────────────────────────┐
                 │ Phase 6: Jaccard        │
                 │ Similarity Calculation  │
                 │ & Scoring Engine        │
                 └────────────┬────────────┘
                              │
                              ▼
                 ┌─────────────────────────┐
                 │ Phase 7: Matched vs.    │
                 │ Missing Skill Partition │
                 └────────────┬────────────┘
                              │
                              ▼
                 ┌─────────────────────────┐
                 │ Phase 8: Persistence &  │
                 │ Role-Scoped Dispatch    │
                 └─────────────────────────┘
```

#### Phase 1: Document Validation & Security Scanning
- Verifies that file extension is `.pdf` and magic bytes correspond to `%PDF-`.
- Validates file size ($\le 5\text{ MB}$).
- Verifies document is unencrypted, readable, and free of malicious macro payloads.

#### Phase 2: Plain-Text Extraction
- Executes server-side PDF text stream parsing (e.g., using `pdf-parse` / PDF extraction streams).
- Extracts all readable alphanumeric characters, preserving line breaks and structural section dividers.

#### Phase 3: Text Normalization & Tokenization
- Lowercases all extracted character streams (case folding).
- Strips non-standard control characters, excess whitespace, and decorative unicode symbols.
- Normalizes punctuation delimiters (hyphens, slashes, periods in version numbers).

#### Phase 4: Taxonomy-Backed Skill Extraction (Deterministic Preprocessing)
- The normalized text is scanned against the platform's **Canonical Technical Skills Taxonomy** (a curated dictionary of ~1,500 programming languages, frameworks, libraries, developer tools, and database systems).
- **Alias Resolution Engine (Deterministic Preprocessing):** Resolves common spelling variations and synonyms into standard canonical identifiers:
  - *e.g.,* `"react.js"`, `"reactjs"`, `"React"` $\rightarrow$ `canonical_id: "react"`
  - *e.g.,* `"node.js"`, `"nodejs"`, `"Node"` $\rightarrow$ `canonical_id: "node.js"`
  - *e.g.,* `"postgres"`, `"postgresql"`, `"psql"` $\rightarrow$ `canonical_id: "postgresql"`
  - *e.g.,* `"golang"`, `"go lang"`, `"Go"` $\rightarrow$ `canonical_id: "go"`
- *Rule:* Canonical skill normalization and alias resolution are purely deterministic preprocessing steps that map string variants into unified set elements prior to set operations; they MUST NOT be described or treated as separate scoring algorithms.
- Compiles the unique set of candidate skills identified in the resume: $\text{Set } R$.

#### Phase 5: Target Job Skill Set Compilation
- In MVP, compiles the target required skill set from the job posting:
  - Total Target Required Skills Set: $J = \text{RequiredSkills}$
  - *(Optional V2 Partitioning: Mandatory $M$ and Preferred $P$ sets are utilized strictly in V2 weighted scoring extensions).*

#### Phase 6: Similarity Calculation & Scoring Engine

##### A. MVP Deterministic Jaccard Similarity (Core V1 Baseline)
For the MVP, the matching engine computes pure **Deterministic Jaccard Similarity** based directly on normalized skill/keyword sets:

$$\text{Jaccard Similarity} = \frac{|\text{ResumeSkills} \cap \text{RequiredSkills}|}{|\text{ResumeSkills} \cup \text{RequiredSkills}|}$$

$$\text{Match Percentage} = \text{Round}\left(\frac{|\text{ResumeSkills} \cap \text{RequiredSkills}|}{|\text{ResumeSkills} \cup \text{RequiredSkills}|} \times 100\right)$$

Where:
- $\text{ResumeSkills} = R$: The set of normalized skills extracted from the candidate's parsed PDF resume.
- $\text{RequiredSkills} = J$: The set of required skills configured for the job drive.
- $|\text{ResumeSkills} \cap \text{RequiredSkills}|$: The count of overlapping/matching skills.
- $|\text{ResumeSkills} \cup \text{RequiredSkills}|$: The count of unique skills across both sets.

**MVP ATS Algorithmic Guarantees:**
- **Deterministic & Reproducible:** Identical resume text and job requirements always yield the exact same numerical score.
- **Explainable & Rule-Based:** Clear mathematical set ratio without opaque weights or hidden multipliers.
- **Normalized Set Operations:** Compares discrete normalized skill tokens (no fuzzy matching as primary algorithm, no frequency weighting, no semantic relevance scoring).
- **Independent of External AI APIs:** Runs entirely in server memory without external LLM, OpenAI, or embedding API dependencies.
- **Decision-Support Only:** The ATS engine is an objective diagnostic tool; it never makes autonomous hiring decisions.

##### B. Advanced Weighted Scoring & Algorithmic Extensions (Classified as V2 / Future Scope)
The following capabilities are formally documented as post-MVP architectural extensions:
1. **Mandatory vs. Preferred Weighted Partitioning (V2 Scope):**
   - Partitioning target skills into Mandatory ($M$) and Preferred ($P$) sets with weighted composite scoring:
     $$S_M = \frac{|R \cap M|}{|M|}, \quad S_P = \frac{|R \cap P|}{|P|}$$
     $$S_{ATS} = \text{Round}\left( (S_M \times 0.70 + S_P \times 0.30) \times 100 \right)$$
   - *Hard Mandatory Penalty Clamp:* Capping composite scores at $49\%$ if candidate possesses $<50\%$ of mandatory skills ($S_M < 0.50$).
2. **Fuzzy String Matching & Edit Distance (V2 Scope):**
   - Levenshtein distance string matching to catch OCR typos and unmapped acronyms.
3. **Term Frequency & Salience Weighting (V2 Scope):**
   - TF-IDF or contextual section weighting based on technology prominence within work experience or project narratives.
4. **Hybrid AI / LLM Semantic Embeddings (Future Scope / V3):**
   - Vector cosine similarity on dense embeddings for deep semantic concept matching.

#### Phase 7: Matched vs. Missing Skill Partitioning
The engine partitions the target job skills into explicit diagnostic subsets:
- **Matched Skills:** $K_{\text{matched}} = R \cap J = \{s \in J \mid s \in R\}$
- **Missing Mandatory Skills:** $K_{\text{missing\_mandatory}} = M \setminus R = \{m \in M \mid m \notin R\}$
- **Missing Preferred Skills:** $K_{\text{missing\_preferred}} = P \setminus R = \{p \in P \mid p \notin R\}$

#### Phase 8: Result Persistence & Role-Scoped Dispatch
- The output payload is saved into the database, linked to the `application_id`.
- The score and breakdown are immediately accessible via role-governed endpoints.

---

### 9.3 Output Data Structure Specification

The ATS processing engine outputs a structured diagnostic record persisted with the application entity:

```json
{
  "application_id": "app_98234",
  "student_id": "stu_4012",
  "job_id": "job_0842",
  "evaluated_at": "2026-09-24T00:15:30Z",
  "scoring_version": "v1.0-deterministic-jaccard",
  "match_percentage": 68,
  "metrics": {
    "raw_jaccard_similarity": 0.684,
    "matching_skills_count": 6,
    "total_required_skills_count": 9,
    "v2_weighted_composite_score": null
  },
  "skill_breakdown": {
    "total_required_skills_count": 9,
    "total_resume_skills_count": 14,
    "matching_skills_count": 6,
    "matched_skills": [
      { "canonical_id": "python", "display_name": "Python", "category": "Mandatory" },
      { "canonical_id": "postgresql", "display_name": "PostgreSQL", "category": "Mandatory" },
      { "canonical_id": "docker", "display_name": "Docker", "category": "Mandatory" },
      { "canonical_id": "rest_api", "display_name": "REST APIs", "category": "Mandatory" },
      { "canonical_id": "git", "display_name": "Git", "category": "Mandatory" },
      { "canonical_id": "redis", "display_name": "Redis", "category": "Preferred" }
    ],
    "missing_mandatory_skills": [
      { "canonical_id": "kubernetes", "display_name": "Kubernetes" }
    ],
    "missing_preferred_skills": [
      { "canonical_id": "aws", "display_name": "AWS Cloud" },
      { "canonical_id": "graphql", "display_name": "GraphQL" }
    ]
  },
  "extraction_summary": {
    "extracted_character_count": 3482,
    "extracted_word_count": 512,
    "is_text_truncated": false
  }
}
```

---

### 9.4 Edge Cases, Fault Tolerance & Exception Handling

| Edge Case / Scenario | Root Cause | System Detection & Handling Protocol | User Feedback & Fallback State |
| :--- | :--- | :--- | :--- |
| **Invalid / Non-PDF Document** | Student renames a `.docx` or `.png` to `.pdf`. | MIME validation and magic number verification (`%PDF-`) fail upon upload. | Upload blocked with: *"Invalid file format. Upload an authentic PDF document."* |
| **Password-Protected / Encrypted PDF** | PDF has security encryption or master password. | Parser encounters encryption flag and throws decryption error. | Upload blocked with: *"Unable to parse password-protected PDF. Remove encryption and retry."* |
| **Scanned Image-Only PDF (Empty Resume)** | PDF contains only raster images (scanned paper) with 0 plain-text characters. | Extracted character count $< 50$ characters after parsing. | System alerts student: *"Warning: No readable text detected in your PDF. Your ATS score will be 0%. Please upload a text-based PDF generated from Word/LaTeX."* Score recorded as `0%`. |
| **Corrupted PDF Document** | Incomplete download or malformed binary stream. | Parser throws stream parsing exception. | System catches exception gracefully; halts upload with: *"File is corrupted. Please re-export your resume."* |
| **Missing / Empty Job Description** | Job posting created without explicit text or required skills. | Engine checks if $M = \emptyset$. | Evaluation is bypassed; ATS score set to `null` with badge: `ATS Matching Unavailable (No Skills Configured)`. |
| **Zero-Skill Match ($0\%$)** | Candidate possesses none of the required technical keywords. | $R \cap J = \emptyset$. | Score recorded as `0%`. UI indicates: *"Low Alignment: No matching required skills identified."* Application remains valid (candidate is not automatically rejected unless recruiter configured a threshold). |
| **Keyword Stuffing / Spam Injection** | Student repeats "Python" 200 times in hidden white text. | Taxonomy extraction deduplicates tokens into a set ($\text{Set } R$). Frequency has zero additive benefit; a skill is either present ($1$) or absent ($0$). | System neutralizes spamming attempts. |

---

### 9.5 Score Interpretation & Compatibility Bands

ATS scores are classified into four transparent compatibility bands used for visual badging and recruiter candidate filtering:

| Score Bracket | Compatibility Classification | UI Badge Style | Practical Recruiter Interpretation | Student Recommendation |
| :--- | :--- | :--- | :--- | :--- |
| **$80\% - 100\%$** | **High Alignment** | Green Badge | Candidate resume explicitly reflects virtually all mandatory and preferred technologies. Ideal candidate for direct shortlisting. | Strong profile alignment. Focus on deep technical prep for interviews. |
| **$60\% - 79\%$** | **Moderate Alignment** | Blue Badge | Candidate possesses the core mandatory skills but lacks certain secondary preferred tools. Strong candidate for screening. | Review missing preferred skills and be prepared to discuss adaptability. |
| **$40\% - 59\%$** | **Partial Alignment** | Amber Badge | Candidate satisfies foundational requirements but misses significant mandatory competencies. Requires human review. | Identify missing mandatory keywords; highlight adjacent project experience. |
| **$0\% - 39\%$** | **Low Alignment** | Gray / Red Badge | Substantial mismatch between resume keywords and role requirements, or scanned image PDF. | High risk of recruiter screening rejection. Refine profile or acquire prerequisite skills. |

---

### 9.6 Security, Data Privacy & Confidentiality Requirements

1. **Private Document Storage & Temporary Access:**
   - Resumes stored in Cloudinary / cloud storage are maintained in private folders with non-public access policies.
   - Resumes are served to authenticated recruiters and students **exclusively via short-lived, cryptographically signed URLs** (expiry window $\le 15\text{ minutes}$). Hot-linking or public URL exposure is strictly prohibited.
2. **Ephemeral In-Memory Processing:**
   - Text extraction and Jaccard scoring execute entirely in volatile server memory.
   - Raw temporary PDF files downloaded for parsing are wiped from disk buffers immediately after text extraction completes.
3. **PII Data Scoping & Protection:**
   - Raw resume text stored in the database is accessible only to:
     - The student who owns the document.
     - Recruiters who own active jobs the student applied to.
     - Authorized TPC Admins.
   - Other students have zero access to peer resumes or ATS scores.
4. **Deterministic Auditability:**
   - The scoring algorithm is mathematically deterministic: evaluating the identical resume against the identical JD will yield the exact same score ($100\%$ reproducible), guaranteeing fairness during administrative dispute audits.

---

### 9.7 Conceptual ATS Result Specification & UI Display Models

The conceptual ATS result represents the standardized diagnostic artifact generated for every evaluated resume against a specific job description. It consists of six mandatory display attributes:

1. **Match Percentage:** Pure deterministic Jaccard similarity percentage ($0\%$ to $100\%$) rendered with color-coded compatibility badging: $\frac{|\text{ResumeSkills} \cap \text{RequiredSkills}|}{|\text{ResumeSkills} \cup \text{RequiredSkills}|} \times 100$.
2. **Matched Skills:** Explicit list of normalized skill tokens identified in both the student's resume and the job requirements ($R \cap J$). *(Mandatory vs. Preferred badge partitioning is deferred to V2).*
3. **Missing Skills:** Explicit list of required technologies present in the job description but absent from the student's resume ($J \setminus R$).
4. **Total Required Skills Count:** Cardinality of the target job required skill set ($|J|$).
5. **Matching Skills Count:** Cardinality of identified matching skills ($|R \cap J|$), formatted as *"X of Y skills matched"*.
6. **Analysis Timestamp:** Precise date and time (ISO-8601 / localized string) when the text parsing and scoring computation executed.

---

#### Conceptual Display Wireframe: ATS Diagnostic Card

```
┌────────────────────────────────────────────────────────────────────────┐
│  AUTOMATED RESUME COMPATIBILITY REPORT (DETERMINISTIC JACCARD)         │
├────────────────────────────────────────────────────────────────────────┤
│  Jaccard Match Score:  [ 70% ]  (MODERATE ALIGNMENT)                   │
│  Skill Alignment:      7 of 9 Required Skills Found                    │
│  Analysis Timestamp:   24 Sep 2026, 00:15 UTC                          │
├────────────────────────────────────────────────────────────────────────┤
│                                                                        │
│  [✓] MATCHED SKILLS (7)                                                │
│  --------------------------------------------------------------------  │
│    [ Python ]   [ PostgreSQL ]   [ Docker ]   [ REST APIs ]   [ Git ]  │
│    [ Redis ]    [ Linux ]                                              │
│                                                                        │
│  [✗] MISSING REQUIRED SKILLS (2)                                       │
│  --------------------------------------------------------------------  │
│    [ Kubernetes ]   [ AWS Cloud ]                                      │
│                                                                        │
│  *(Note: Mandatory vs. Preferred badge partitioning is V2 Scope)*      │
└────────────────────────────────────────────────────────────────────────┘
```

---

### 9.8 Persona Visibility & Data Disclosure Matrix

To protect candidate privacy, prevent strategic gaming, and deliver actionable insights, the system enforces a strict data disclosure matrix governing which attributes of the ATS result are exposed to each user role:

| Data Attribute / UI Component | Student | Recruiter | TPC Admin | Operational & Privacy Rationale |
| :--- | :--- | :--- | :--- | :--- |
| **Own Match Percentage** | **VISIBLE** | **VISIBLE** | **VISIBLE** | Provides transparent feedback to candidate and objective ranking metric for recruiter. |
| **Peer Students' Match Percentages** | **HIDDEN** | **VISIBLE** (All applicants to own job) | **VISIBLE** (Universal across all jobs) | Protects student privacy; prevents peer comparison anxiety and harassment. |
| **Matched Skills List** | **VISIBLE** | **VISIBLE** | **VISIBLE** | Validates candidate strengths and confirms credential presence. |
| **Missing Skills List** | **VISIBLE** | **VISIBLE** | **VISIBLE** | Provides students actionable learning targets; helps recruiters identify training gaps. |
| **Matching Skills Count (X of Y)** | **VISIBLE** | **VISIBLE** | **VISIBLE** | Quantifies concrete coverage beyond percentage calculation. |
| **Total Required Skills Count** | **VISIBLE** | **VISIBLE** | **VISIBLE** | Contextualizes the depth of job technical requirements. |
| **Analysis Timestamp** | **VISIBLE** | **VISIBLE** | **VISIBLE** | Verifies freshness of document evaluation. |
| **Ranked Applicant List (by ATS Score)** | **HIDDEN** | **VISIBLE** | **VISIBLE** | Exclusive recruiter tool for batch shortlisting; hidden from candidates to prevent competitive friction. |
| **Raw Extracted Resume Text** | **VISIBLE** (Own text) | **VISIBLE** (Own applicants) | **VISIBLE** (Universal audit) | Allows auditing text extraction quality; hidden across peer candidates. |
| **Internal Recruiter Evaluation Notes** | **HIDDEN** | **VISIBLE** (Own team) | **VISIBLE** | Private recruiter interview feedback strictly isolated from candidate visibility. |
| **Algorithmic Weight Configuration ($W_M / W_P$)** | **HIDDEN** | **VISIBLE** (Own JD settings) | **VISIBLE** (Full system audit) | Prevents students from reverse-engineering keyword weights. |

---

### 9.9 Persona-Specific Display Contexts

#### A. Student Experience (Diagnostic & Learning Context)
* **Where Displayed:**
  1. On the Job Details page via the "Check Resume Compatibility" modal (pre-application preview).
  2. On the "My Applications" tracking dashboard under the specific application card (post-application review).
* **Objective:** Serve as an educational diagnostic. Students see their match score, celebrate matched competencies, and receive explicit guidance on missing technologies to address before interviews or for subsequent drives.
* **Guards:** Students **never** see where they rank numerically among applicant peers (e.g., they see *"78% Match"*, but do *not* see *"Rank #14 of 120 applicants"*), eliminating unnecessary competitive anxiety.

#### B. Recruiter Experience (Screening & Triage Context)
* **Where Displayed:**
  1. As a sortable column in the Applicant Pool Table (`ATS Score %`).
  2. Inside the Candidate Profile Drawer as an expandable diagnostic panel.
  3. In the drive-level analytics histogram showing candidate score distribution across bands.
* **Objective:** Enable rapid, objective candidate shortlisting. Recruiters can sort descending by ATS score, set threshold filters (e.g., display applicants with score $\ge 75\%$), and quickly inspect candidate strengths and missing competencies before conducting technical interviews.
* **Guards:** Scoped strictly to candidates who applied to their own job postings.

#### C. TPC Admin Experience (Governance & Audit Context)
* **Where Displayed:**
  1. In the Universal Applications table.
  2. In the ATS Algorithm Audit console.
  3. In candidate appeal investigation panels.
* **Objective:** Ensure institutional recruitment integrity, resolve scoring fairness disputes, inspect unreadable/scanned PDF error rates, and monitor whether corporate skill expectations align with university curriculum competencies.

---

## 10. Resume Management & Document Pipeline Requirements

This section specifies the technical, operational, and security requirements governing student **Resume Document Ingestion, Storage, Text Parsing, Versioning, and Deletion**.

---

### 10.1 Allowed File Types & MIME Validation

* **Exclusively Permitted Format:** Portable Document Format (`.pdf`).
* **Explicitly Prohibited Formats:** Microsoft Word (`.doc`, `.docx`), Rich Text Format (`.rtf`), Plain Text (`.txt`), OpenDocument (`.odt`), and Image files (`.png`, `.jpg`, `.jpeg`).
* **Strict Two-Tier Validation Protocol:**
  1. **Client-Side Pre-Check:** File input component validates file extension (`.pdf`) and MIME type (`application/pdf`) prior to network transmission.
  2. **Server-Side Binary Magic Bytes Verification:** The server-side multipart stream inspector reads the first 5 bytes of the binary header. The document is accepted if and only if the header begins with `%PDF-` (hex signature `25 50 44 46 2D`). Renamed executables or spoofed image files (e.g., an image renamed to `resume.pdf`) are atomically rejected with HTTP 415 Unsupported Media Type.

---

### 10.2 File Size Constraints & Capacity Considerations

* **Maximum Hard File Size Limit:** **5.00 MB** ($5,242,880\text{ bytes}$).
* **Recommended Optimal Range:** $200\text{ KB} - 1.50\text{ MB}$.
* **System Rationale:** A standard 1-to-2-page text-based PDF containing vector fonts and professional formatting consumes between $150\text{ KB}$ and $1\text{ MB}$. A 5 MB ceiling provides sufficient headroom for high-resolution university crests and vector graphics while preventing denial-of-service (DoS) memory exhaustion, storage ballooning, and network latency during peak application rushes.
* **Enforcement Mechanism:**
  - Client-side validation halts oversized uploads before bandwidth consumption.
  - Server-side streaming request limits terminate incoming payloads exceeding 5 MB with HTTP 413 Payload Too Large.

---

### 10.3 Resume Upload & Ingestion Workflow

The upload workflow operates across six sequential steps:

```
[Student selects .pdf file]
             │
             ▼
[1. Client-Side Validation: File type == .pdf AND Size <= 5 MB]
             │
             ├── FAILS ──► Display inline error; halt upload.
             └── PASSES
             │
             ▼
[2. Multipart Stream Upload to Backend (/api/resumes/upload)]
             │
             ▼
[3. Server-Side Magic Bytes & Antivirus Scan]
             │
             ├── FAILS ──► Return HTTP 415 / 400; discard payload.
             └── PASSES
             │
             ▼
[4. Dispatch to Cloud Storage (Cloudinary)]
             │ Returns persistent, private document reference URL
             ▼
[5. Server-Side Plain-Text Extraction & Taxonomy Indexing]
             │ Extracts text, tokens, and candidate skills
             ▼
[6. Database Persistence & Default Selection]
             │ Inserts record into Resumes table
             │ Sets as student's Default Active Resume
             ▼
[Success Confirmation to Student: "Resume uploaded & parsed successfully (14 skills detected)"]
```

---

### 10.4 Versioning & Old Resume Replacement Rules

Students often maintain distinct resume variants tailored for specific career pathways (e.g., Software Development vs. Data Science vs. Core Engineering).

1. **Multi-Version Allowance:** Each student may maintain up to **3 active resume versions** concurrently in their document repository.
2. **Default Active Resume Concept:**
   - One resume is explicitly designated as the `Default Active Resume`.
   - The default resume is automatically pre-selected when the student opens the application modal for a new drive.
   - The student can switch the default flag at any time between their uploaded versions.
3. **Application Snapshot Immutability Principle:**
   - When a student applies for a job, the system binds an **immutable snapshot reference** of the specific resume selected at that moment to the application record.
   - If the student subsequently uploads a new resume, replaces an existing document, or alters their default resume, **previously submitted applications remain completely unaffected**. Recruiters continue to view the exact document version submitted at application time.

---

### 10.5 Resume Deletion & Archival Policy

To preserve institutional audit integrity and prevent broken document links during active selection rounds, the platform enforces strict referential integrity rules for resume deletion:

* **Deletable Resumes:** A student can delete an uploaded resume **only if it is not linked to any active application** (i.e., applications in `APPLIED`, `ATS_SHORTLISTED`, `INTERVIEW_SCHEDULED`, or `OFFER_EXTENDED` stages).
* **Locked Resumes:** If a resume is linked to an active application, the delete button is disabled with a security tooltip:
  *"This resume is currently attached to an active recruitment drive for [Company Name]. It cannot be deleted until the recruitment process concludes."*
* **Soft Deletion Mechanism:** When an eligible resume is deleted by a student, the system executes a **Soft Delete** (`is_deleted: true`), removing it from the student's active selection list while preserving the underlying storage asset for institutional compliance audits.
* **Administrative Purge Authority:** Only TPC Admins have the authority to permanently purge corrupted, offensive, or policy-violating resume documents from cloud storage.

---

### 10.6 Document Storage Architecture

* **Primary Storage Provider:** **Cloudinary** (configured with private asset policies).
* **Storage Hierarchy & Directory Partitioning:**
  `campus_portal/resumes/{student_roll_number}/{resume_uuid}.pdf`
* **Access Control & Bucket Security:**
  - Storage buckets/directories are configured with **strict non-public access controls**.
  - Direct public URLs to resumes return HTTP 403 Forbidden.
  - Document viewing and downloading are mediated exclusively via the application backend or cryptographically signed time-limited URLs (see Section 10.9).
* **File Naming Standard:**
  When exported or downloaded by recruiters, files are automatically renamed to an institutional convention:
  `{RollNumber}_{StudentFullName}_Resume.pdf` (e.g., `2022CSE084_Priya_Patel_Resume.pdf`).

---

### 10.7 Extracted Text Lifecycle & Indexing

1. **Storage of Parsed Text:**
   - The plain text parsed from the PDF is stored alongside the document metadata in the database (`extracted_text` column).
   - Storing the extracted text eliminates the need to re-download and re-parse the heavy PDF file every time an ATS score is previewed or re-evaluated.
2. **Text Normalization Standards:**
   - Control characters and zero-width spaces are stripped.
   - Text is normalized into standard UTF-8 character encoding.
   - Extracted metrics recorded: `character_count`, `word_count`, `line_count`, `parsing_duration_ms`.
3. **Database Security:**
   - Raw extracted text is accessible only to authenticated backend services and authorized role holders (Student owner, hiring recruiter, TPC Admin).

---

### 10.8 Exception Handling: Corrupt, Encrypted, Empty & Failing PDFs

The ingestion pipeline handles non-standard and adversarial document states through deterministic fallback protocols:

| Document Anomaly | Detection Mechanism | System Action & Storage State | User Guidance & Error Presentation |
| :--- | :--- | :--- | :--- |
| **Corrupted / Truncated PDF** | PDF parsing stream crashes or throws malformed EOF/trailer error. | Ingestion aborted atomically; cloud file discarded; no database record created. | Modal error alert: *"File corrupted: The uploaded PDF could not be processed. Please open the file in your PDF viewer, re-export as a fresh PDF, and try again."* |
| **Password-Protected / Encrypted PDF** | Parser detects `/Encrypt` dictionary flag in document header or throws decryption challenge. | Ingestion aborted immediately; cloud upload cancelled. | Modal error alert: *"Encrypted Document: Your PDF is password-protected or encrypted. Please remove password security in your document settings before uploading."* |
| **Scanned Image-Only PDF (Empty Resume)** | Parsing completes without error, but total extracted alphanumeric characters $< 50$. | Upload and storage succeed, but text layer is flagged as `empty_scanned`. ATS score defaults to `0%`. | Prominent amber warning banner: *"Warning: No readable text detected in your PDF. This appears to be a scanned image. Because recruiters use automated text matching, your resume will score 0%. Please upload a standard text-based PDF generated from Word, Google Docs, or LaTeX."* |
| **Malformed Font Encoding (Garbled Characters)** | Parser extracts non-printable Unicode symbols (e.g., `` or mojibake) due to missing embedded font encodings. | Document stored, but extracted text passes quality heuristic. If recognizable words $< 20\%$, flagged as `unreadable_fonts`. | Warning prompt: *"Text extraction detected unreadable custom fonts. Ensure standard fonts (Arial, Times New Roman, Calibri, Computer Modern) are used."* |
| **Extraction Timeout** | PDF parser hangs for $> 5$ seconds (e.g., deep recursive vector graphics). | Asynchronous timeout worker aborts extraction thread; logs incident. | Error alert: *"Document processing timed out. Please simplify complex graphical elements in your resume and retry."* |

---

### 10.9 Privacy, Confidentiality & Access Control Requirements

Resumes contain highly sensitive Personally Identifiable Information (PII), including student phone numbers, residential addresses, personal email IDs, educational records, and employment histories. The platform enforces rigid security standards:

1. **Signed Temporary URLs (Expiring Links):**
   - Resumes are served to authorized recruiters and students **exclusively via cryptographically signed temporary URLs**.
   - Signed URL expiration window is capped at **15 minutes**.
   - Direct permanent links to cloud storage are never exposed in frontend network payloads or HTML DOM trees.
2. **Access Scoping Isolation:**
   - A student can access only their own uploaded resume documents.
   - A recruiter can access resumes **only** for students who have formally submitted an application to that recruiter's specific job posting. Unrestricted browsing or scraping of the student body's resumes is architecturally prohibited.
   - TPC Admins have universal document access for institutional accreditation and verification compliance.
3. **Prevention of Public Search Engine Indexing:**
   - The cloud storage bucket serves `X-Robots-Tag: noindex, nofollow` headers on all document fetches.
   - Portals enforce `robots.txt` disallowing web crawlers from scanning `/resumes/` routes.
4. **Audit Logging for Document Access:**
   - Every download, preview, or export of a candidate's resume generates a permanent access audit entry: `[timestamp, accessor_user_id, student_id, resume_id, ip_address]`.

---

## 11. Interview Scheduling & Round Management Requirements

This section specifies the functional and operational requirements for the **Interview Management System**. The system enables recruiters and TPC Admins to coordinate, schedule, monitor, and record multi-round interviews for shortlisted candidates without embedding in-browser video conferencing infrastructure.

---

### 11.1 Scheduling Entities & Slot Attributes

Every scheduled interview record encapsulates the operational parameters required for candidate attendance and corporate evaluation:

| Attribute | Data Type / Format | Status | Validation & Business Constraints | Operational Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **Interview Date** | Date (`YYYY-MM-DD`) | **Mandatory** | Must be a valid future date; must not clash with institutional blackout days (semester exams). | Establishes the recruitment calendar day. |
| **Start Time & End Time** | Time (`HH:MM`, 24h format) | **Mandatory** | `end_time > start_time`; duration typically $30$, $45$, or $60$ minutes; standardized in institutional timezone (IST). | Defines the specific time slot allocated to the candidate. |
| **Interview Type** | Enumeration | **Mandatory** | `Virtual Video Conference` or `In-Person (Campus Physical)` | Dictates whether candidates attend at a campus facility or connect remotely. |
| **Meeting URL / Link** | URL (`https://...`) | **Conditional** | Mandatory if Type is `Virtual`; must begin with `https://` (e.g., Zoom, Google Meet, MS Teams). | Connects candidate and panel via external communication platforms. |
| **Meeting Passcode / Pin** | String ($0-50$ chars) | **Optional** | Plain text; optional security access credentials | Disclosed to the assigned candidate alongside the meeting URL. |
| **Campus Venue / Room** | String ($3-100$ chars) | **Conditional** | Mandatory if Type is `In-Person`; e.g., "Placement Cell, Room 204, Tech Tower". | Physical location for on-campus reporting and verification. |
| **Round Name / Sequence** | String ($3-50$ chars) | **Mandatory** | e.g., "Round 1: Technical & Algorithms", "Round 2: System Design", "HR Round" | Informs candidate of evaluation focus and tracks multi-round progression. |
| **Target Application** | Foreign Key (`app_id`) | **Mandatory** | Candidate application must be in `ATS_SHORTLISTED` or active `INTERVIEW_SCHEDULED` state. | Ties interview event directly to candidate application history. |
| **Interviewer Panel Names** | String ($0-150$ chars) | **Optional** | Names of corporate evaluators or alumni panel leads | Provides context for panel assignment and reporting logs. |
| **Special Instructions** | Text ($0-500$ chars) | **Optional** | e.g., "Keep 2 printed copies of resume, government ID, and college smart card ready." | Logistical preparation instructions for candidate. |

---

### 11.2 Interview Lifecycle State Machine (Status)

Every interview slot transitions through a defined lifecycle:

```
                  ┌─────────────┐
                  │  SCHEDULED  │
                  └──┬───────┬──┘
                     │       │ (Candidate Reschedule Request)
(Candidate Confirms) │       ▼
                     │  ┌────────────────────────┐
                     │  │  RESCHEDULE_REQUESTED  │
                     │  └────────────┬───────────┘
                     │               │ (Recruiter / TPC Accepts)
                     ▼               ▼
           ┌────────────────┐  ┌─────────────┐
           │   CONFIRMED    │  │ RESCHEDULED ├─────────┐
           └───────┬────────┘  └─────────────┘         │
                   │                                   │
                   ├───────────────────────────────────┘
                   │
                   ▼
       ┌───────────────────────┐
       │      IN_PROGRESS      │
       └─────┬───────────┬─────┘
             │           │ (Candidate Absent)
(Panel Logs) │           ▼
             │     ┌───────────┐
             │     │  NO_SHOW  │
             │     └───────────┘
             ▼
      ┌─────────────┐               ┌─────────────┐
      │  COMPLETED  │               │  CANCELLED  │
      └─────────────┘               └─────────────┘
```

1. **`SCHEDULED`:** Recruiter or TPC Admin creates the slot and assigns the candidate. Candidate notification is dispatched.
2. **`CONFIRMED`:** Candidate acknowledges the schedule and confirms attendance via the portal.
3. **`RESCHEDULE_REQUESTED`:** Candidate flags an academic conflict or emergency and requests an alternate time slot with written justification.
4. **`RESCHEDULED`:** Recruiter or TPC Admin updates the slot timestamp; new calendar notifications are dispatched.
5. **`IN_PROGRESS`:** The interview slot's start time has arrived on the calendar day.
6. **`COMPLETED`:** The interview has concluded, and the interviewer panel has submitted their round evaluation ratings and scorecard.
7. **`CANCELLED`:** The slot was officially cancelled by the recruiter or TPC Admin prior to execution.
8. **`NO_SHOW`:** The candidate failed to attend the scheduled interview without prior approved rescheduling. Triggers an automated placement cell attendance inquiry.

---

### 11.3 Candidate Notification & Calendar Integration

Prompt, reliable communication is essential to prevent missed interviews during high-pressure placement seasons:

1. **Automated Notification Triggers:**
   - **Initial Assignment:** Dispatched immediately upon publishing the interview schedule.
   - **Reschedule Alert:** Dispatched when a slot's date, time, venue, or link is modified.
   - **Cancellation Alert:** Dispatched immediately if a slot is rescinded.
   - **Proactive Reminders:** Automated reminders dispatched **24 hours** and **2 hours** prior to the scheduled start time.
2. **Multi-Channel Delivery:**
   - **In-App Persistent Alert:** High-priority notification banner displayed on the student portal navigation bar.
   - **Institutional Email:** Formatted transactional email containing full round details, interviewer names, and location/meeting credentials.
   - **Calendar Integration (`.ics` File Attachment):** Outgoing emails include an RFC 5545-compliant `.ics` calendar invitation file, allowing candidates and recruiters to add the interview directly to Google Calendar, Apple Calendar, or Outlook with a single click.

---

### 11.4 Rescheduling Workflows & Conflict Resolution

#### A. Recruiter-Initiated Reschedule
- **Preconditions:** Interview is in `SCHEDULED` or `CONFIRMED` state; scheduled start time has not passed.
- **Workflow:**
  1. Recruiter opens the interview slot and clicks "Reschedule Slot".
  2. Recruiter selects a new Date, Start Time, and End Time, and provides a brief reason (e.g., "Panel availability shifted").
  3. The system executes the collision detector: ensures the candidate does not have a concurrent interview with another company at the new time.
  4. Recruiter commits the change.
  5. Status updates to `RESCHEDULED`, and an urgent notification + updated `.ics` invite are dispatched to the student.

#### B. Candidate-Initiated Reschedule Request
- **Preconditions:** Interview is in `SCHEDULED` state; request submitted $\ge 4\text{ hours}$ before the scheduled slot.
- **Workflow:**
  1. Student clicks "Request Reschedule" on their interview card.
  2. Student selects a standardized reason category: `University Semester Exam Clash`, `Medical Emergency`, `Overlapping Verified Campus Drive`.
  3. Student inputs mandatory written details (minimum 20 characters) and uploads supporting documentation if applicable (e.g., exam timetable).
  4. Status transitions to `RESCHEDULE_REQUESTED`.
  5. The request is routed to both the Recruiter and TPC Admin.
  6. Recruiter can accept the request by assigning a new open slot, or TPC Admin can intervene to de-conflict competing corporate schedules.

#### C. Automated Schedule Collision Detection
- The backend scheduler automatically checks for temporal overlap across all concurrent drives:
  $$\text{Slot A start} < \text{Slot B end} \;\land\; \text{Slot A end} > \text{Slot B start}$$
- If a candidate is already assigned to Company A at 10:00 AM–10:45 AM, the system blocks Company B from booking that candidate between 9:45 AM and 11:00 AM (incorporating a mandatory 15-minute buffer between campus interviews).

---

### 11.5 Cancellation Protocols

1. **Who Can Cancel:**
   - **Recruiter:** Can cancel interview slots for their own drives prior to execution.
   - **TPC Admin:** Can cancel any interview slot institution-wide to enforce academic priorities or resolve disciplinary issues.
   - **Students Cannot Unilaterally Cancel:** Students cannot click "Cancel". They may only request a reschedule with documented cause. Unexcused absence is categorized as a `NO_SHOW`.
2. **Mandatory Cancellation Reason:**
   - Cancelling a slot requires selecting a reason: `Corporate Hiring Freeze`, `Drive Withdrawn`, `Candidate Advanced via Offline Evaluation`, `Administrative Interruption`.
3. **Post-Cancellation System State:**
   - The slot status transitions to `CANCELLED`.
   - The candidate receives an immediate alert detailing the cancellation reason.
   - The candidate's application record does not default to rejected; it remains in its previous valid stage awaiting recruiter determination or drive closure.

---

### 11.6 Interview Completion, Scorecards & Progression Workflow

Following the execution of an interview, the panel records structured candidate feedback:

1. **Marking Completion:**
   - The recruiter accesses the slot scorecard and clicks "Record Interview Feedback".
   - Status updates to `COMPLETED`.
2. **Standardized Evaluation Scorecard:**
   - **Technical Competency Rating:** Numeric score (Scale 1–5, from *Unsatisfactory* to *Exceptional*).
   - **Problem Solving & Analytical Ability:** Numeric score (Scale 1–5).
   - **Communication & Professional Demeanor:** Numeric score (Scale 1–5).
   - **Internal Qualitative Remarks:** Free-form notes documenting specific technical questions asked, coding strengths, and behavioral observations.
3. **Round Decision Recommendation:**
   The interviewer selects one of three actionable progression recommendations:
   - **`Pass to Next Round`:** Prompts the recruiter to schedule the subsequent round (e.g., Round 1 $\rightarrow$ Round 2).
   - **`Recommend for Offer`:** Clears candidate for formal employment offer issuance (see Section 4.13 & Section 8).
   - **`Disqualify / Reject`:** Transitions candidate application stage to `REJECTED`, dispatches a formal rejection notification, and closes the candidacy for this drive.

---

### 11.7 Explicit Architectural Scope Boundary: No In-Browser Video Conferencing

To maintain system reliability, high performance, and rapid implementation focus, the platform establishes an **explicit non-functional scope boundary**:

* **EXPLICITLY OUT OF SCOPE:** In-browser WebRTC peer-to-peer video streaming, video recording, real-time screen sharing, in-browser audio transcription, or media server infrastructure (e.g., Janus, Kurento).
* **IN-SCOPE / ARCHITECTURAL APPROACH:** The platform acts as the **scheduling and logistics coordinator**. It captures, validates, and distributes external meeting links (Zoom, Google Meet, Microsoft Teams, Webex) and physical campus venue locations, synchronizing attendance, scorecards, and pipeline progression while delegating live video streaming to proven enterprise communication tools.

---

## 12. Offer Management & Placement Policy Enforcement Requirements

The **Offer Management System** governs the issuance, institutional vetting, candidate decision tracking, and programmatic policy locking associated with formal employment offers. It ensures that corporate commitments match published drive parameters and programmatically enforces institutional placement mandates such as the "One Student, One Job" rule and Dream Tier upgrade regulations.

---

### 12.1 Offer Creation & Attribute Specification

Offers are created by authorized corporate recruiters following final selection rounds (or logged directly by TPC Admins for off-campus/direct recruitment).

#### A. Preconditions for Offer Creation
- The candidate application must be in an interview-cleared stage (`INTERVIEW_SCHEDULED` final round passed).
- The candidate must not hold a conflicting accepted offer in an equal or higher placement tier.
- The sponsoring recruiter must have active, approved organizational standing.

#### B. Structured Offer Data Schema
Every offer record requires explicit compensation, logistical, and document attributes:

| Field Name | Type / Format | Status | Validation & Business Constraints | Operational & Policy Role |
| :--- | :--- | :--- | :--- | :--- |
| **Gross Annual CTC (INR)** | Currency / Number | **Mandatory** | Numeric value $> 0$; e.g., ₹14,50,000 | Baseline metric for institutional placement statistics and tier categorization. |
| **Fixed Base Salary (INR)** | Currency / Number | **Mandatory** | Numeric value $> 0$; must be $\le$ Gross CTC | Guaranteed annual base compensation excluding performance bonuses. |
| **Performance Variable Bonus**| Currency / Number | **Optional** | Defaults to ₹0; numeric | Discloses performance-linked incentive compensation. |
| **Joining / Retention Bonus** | Currency / Number | **Optional** | Defaults to ₹0; numeric | One-time joining incentive and clawback disclosures. |
| **Monthly Internship Stipend** | Currency / Number | **Optional** | Defaults to ₹0 (for full-time direct); numeric | Compensation during pre-graduation internship period (e.g., ₹50,000/month). |
| **Tentative Joining Date** | Date (`YYYY-MM-DD`) | **Mandatory** | Must be a future date post-graduation | Official employment start date for placement gazette. |
| **Primary Work Location** | String ($2-100$ chars) | **Mandatory** | Text (e.g., "Hyderabad", "Bengaluru", "Pune") | Geographic posting assigned to candidate. |
| **Offer Letter Document** | File (`.pdf`, $\le 5\text{ MB}$) | **Mandatory** | MIME: `application/pdf`; signed official letterhead | Legal and institutional verification asset reviewed by TPC Admin. |
| **Decision Window Expiration** | ISO-8601 Timestamp | **Mandatory** | Typically $48$ to $72$ hours from TPC release | Countdown deadline for candidate acceptance or decline. |
| **Placement Tier Category** | Enumeration | **System-Set** | `Regular`, `Dream`, `Super Dream` | Evaluated automatically against institutional CTC brackets. |

#### C. Explicit Scope Boundary: No Payroll or Payment Processing
To preserve architectural focus and compliance boundaries:
* **EXPLICITLY OUT OF SCOPE:** Banking account capture, direct deposit processing, tax withholding calculations, provident fund (PF/ESI) setups, payroll generation, or signing bonus escrow disbursements.
* **IN-SCOPE / ARCHITECTURAL APPROACH:** The platform manages **recruitment status and institutional policy compliance**. Once an offer is accepted, the candidate record transitions to `Placed`, and employee onboarding/payroll workflows are transferred to the hiring company's external HR Information Systems (HRIS).

---

### 12.2 Offer Lifecycle State Machine (Offer Status)

Every extended offer transitions through a tightly regulated state machine:

```
                     ┌──────────────────────────────┐
                     │   PENDING_TPC_VERIFICATION   │
                     └──────────────┬───────────────┘
                                    │ (TPC Approves Offer)
                                    ▼
                         ┌──────────────────────┐
                         │   OFFER_EXTENDED     │
                         │   (Active / Open)    │
                         └──────┬────────┬──────┘
                                │        │
       ┌────────────────────────┘        └────────────────────────┐
       │ (Candidate Accepts)             │ (Candidate Declines)   │ (Timer Lapses)
       ▼                                 ▼                        ▼
 ┌───────────┐                     ┌───────────┐            ┌───────────┐
 │ ACCEPTED  │                     │ DECLINED  │            │  EXPIRED  │
 └─────┬─────┘                     └───────────┘            └───────────┘
       │
       ▼
 [Institutional Policy Cascade]
 - Student marked PLACED
 - Competing apps AUTO_WITHDRAWN
 - Profile locked for lower tiers
```

1. **`PENDING_TPC_VERIFICATION`:** The recruiter has submitted the offer terms and uploaded the offer letter. The offer is queued for TPC administrative review and is **invisible to the student**.
2. **`OFFER_EXTENDED` (Active / Open):** TPC Admin has verified that terms match drive criteria. The offer is officially released to the student's portal with an active countdown timer.
3. **`ACCEPTED` (Terminal Success):** The student formally accepts the offer within the decision window, triggering institutional placement locks.
4. **`DECLINED` (Terminal Rejection):** The student formally rejects the offer, submitting a mandatory written reason.
5. **`EXPIRED` (Terminal Expiration):** The candidate failed to take action before the decision deadline elapsed. The offer closes automatically.
6. **`REVOKED` (Administrative Exception):** The offer was formally rescinded by TPC Admin or the Recruiter prior to student acceptance due to documented dispute or corporate crisis.

---

### 12.3 Candidate Notification & Multi-Channel Delivery

The moment an offer transitions from `PENDING_TPC_VERIFICATION` to `OFFER_EXTENDED`:

1. **Instant In-App Alert:** A prominent modal banner appears on the student's dashboard:
   *"Official Employment Offer Extended: [Company Name] has offered you the role of [Role Title] with a CTC of ₹[Amount]. Action required within [Hours] hours."*
2. **Transactional Email Dispatch:** An automated email is dispatched to the student's registered institutional email containing:
   - Summary of compensation terms (Fixed Base, Variable, Total CTC, Location).
   - Secure temporary signed URL to preview and download the official Offer Letter PDF.
   - Decision deadline countdown and direct portal link to record the decision.
3. **TPC & Recruiter Confirmation:** Both the recruiter and TPC Admin receive receipt confirmations indicating that the offer was successfully delivered to the candidate's account.

---

### 12.4 Candidate Acceptance Workflow & System Cascades

Accepting an offer is an irreversible institutional milestone. To prevent accidental clicks, the platform enforces a **two-step confirmation protocol**:

#### A. Acceptance Flow
1. The student navigates to the "Offers" tab and opens the active offer.
2. The student reviews itemized compensation terms and downloads the official PDF offer letter.
3. The student clicks "Accept Offer".
4. A critical confirmation modal appears:
   > **ATTENTION: BINDING INSTITUTIONAL PLACEMENT ACCEPTANCE**
   > Accepting this offer from **[Company Name]** for **₹[CTC]** will formally mark you as **PLACED** in the **[Tier Name]** category.
   >
   > Under university placement policy:
   > - Your remaining active applications for lower or equal tier drives will be **automatically withdrawn**.
   > - You will be **locked** from applying to future drives unless they qualify as an institutional Dream Tier upgrade.
   >
   > To confirm your acceptance, type **"ACCEPT"** below:
   > `[                      ]`
5. The student types `"ACCEPT"` and clicks "Confirm Acceptance".

#### B. Automated Institutional Policy Cascades
Upon confirmation, the backend executes an atomic database transaction:
1. **Offer State Mutation:** Transitions `Offer.status` to `ACCEPTED`.
2. **Application State Mutation:** Transitions corresponding `Application.status` to `ACCEPTED`.
3. **Student Profile Mutation:** Updates `Student.placement_status` to `PLACED`.
4. **Placement Gazette Entry:** Persists permanent placement record (`placed_company_id`, `placed_ctc`, `placed_tier`, `placed_date`).
5. **Policy Auto-Withdrawal Cascade:**
   - Queries all other active applications submitted by this student where $\text{Drive.tier} \le \text{Accepted.tier}$.
   - Atomically updates their states to `AUTO_WITHDRAWN` with audit reason: `"Withdrawn by institutional policy following acceptance of offer from [Company Name]"`.
   - Releases scheduled interview slots with other companies so recruiters can backfill candidates.
6. **Stakeholder Broadcast:** Dispatches instant acceptance notifications to the hiring recruiter and TPC Admin.

---

### 12.5 Candidate Decline Workflow

If a candidate decides not to accept an extended offer:

1. The student clicks "Decline Offer" on the active offer view.
2. The system prompts for a **Mandatory Written Justification**:
   - The student selects a standardized Category: `Pursuing Higher Studies (GATE/CAT/GRE)`, `Location / Relocation Constraints`, `Role / Technology Preference`, `Personal / Family Reasons`.
   - The student provides mandatory explanatory text (minimum 25 characters).
3. The student confirms the decline action.
4. The system executes the decline mutation:
   - Updates `Offer.status` to `DECLINED`.
   - Updates `Application.status` to `DECLINED`.
   - Decrements the student's institutional offer decline quota (per university placement rules, students may only decline 1 campus offer before mandatory debarment).
5. The recruiter receives an immediate alert detailing the decline and justification, enabling them to extend an offer to the next ranked candidate.

---

### 12.6 Offer Expiration Protocols

1. **Automated Expiration Scheduler:**
   - An asynchronous background job monitors open offers against their `decision_deadline` timestamps.
2. **Proactive Candidate Warnings:**
   - When **24 hours** remain: System dispatches high-priority reminder alert.
   - When **6 hours** remain: System issues urgent alert: *"Final Warning: Your offer from [Company Name] expires in 6 hours."*
3. **Expiration Execution:**
   - If the timer lapses without student action, the system transitions `Offer.status` to `EXPIRED`.
   - The application status transitions to `DECLINED` (classified as `Declined via Decision Window Expiration`).
   - The recruiter is notified that the candidate failed to respond, unlocking the vacancy for waitlisted candidates.

---

### 12.7 Synchronized Matrix: Offer Status vs. Application Status vs. Student Placement Standing

The table below defines how offer mutations dynamically synchronize with application records and universal student placement standing:

| Offer Event / State | Offer Status | Application Status | Student Placement Status | Application Quota & Competing Applications |
| :--- | :--- | :--- | :--- | :--- |
| **Recruiter Submits Terms** | `PENDING_TPC_VERIFICATION` | `INTERVIEW_SCHEDULED` | `UNPLACED` | Inactive / Hidden from student; no quota impact. |
| **TPC Approves Offer** | `OFFER_EXTENDED` | `OFFER_EXTENDED` | `UNPLACED` | Active offer hold; decision countdown running. |
| **Student Accepts Offer** | `ACCEPTED` | `ACCEPTED` | **`PLACED`** | **CASCADES:** Competing apps in $\le$ tier $\rightarrow$ `AUTO_WITHDRAWN`. Student profile locked. |
| **Student Declines Offer** | `DECLINED` | `DECLINED` | `UNPLACED` | Candidacy ends for this drive; student decline quota decremented. |
| **Decision Window Lapses** | `EXPIRED` | `DECLINED` | `UNPLACED` | Offer voided; treated functionally as student decline. |
| **Offer Revoked (Dispute)**| `REVOKED` | `REJECTED` | `UNPLACED` | Offer voided by TPC Admin; application terminated with audit log. |

---

## 13. TPC Admin Placement Analytics & Dashboard Requirements (V1 Scope)

The **TPC Admin Analytics Suite** delivers real-time operational visibility and placement performance tracking across the institution. The V1 scope prioritizes direct transactional aggregations necessary for day-to-day placement operations and statutory reporting, avoiding speculative business intelligence, predictive machine learning forecasts, or complex OLAP data warehouses.

---

### 13.1 V1 Scope & Architectural Boundary

1. **In-Scope (V1 Focus):**
   - High-level executive KPI summary cards.
   - Real-time pipeline stage distribution of applications.
   - Department/branch comparative placement breakdowns.
   - Granular per-job recruitment performance metrics table.
   - Dynamic cohort filtering (by Graduation Batch, Branch, and Placement Tier).
2. **Explicitly Out of Scope for V1:**
   - Multi-year regression modeling and salary trend predictive forecasting.
   - Student salary negotiation analytics.
   - Natural language query interfaces (AI chat analytics).
   - External data warehouse (Snowflake/BigQuery) pipeline syncs.

---

### 13.2 Core Institutional KPI Metric Cards

The top of the TPC Admin Dashboard renders a row of standardized summary KPI cards computed dynamically across the selected filter scope:

| Metric Name | Display Format | Underlying Calculation / Source | Operational Significance |
| :--- | :--- | :--- | :--- |
| **Total Students** | Integer (e.g., `1,250`) | Total registered students in selected batch; includes sub-counts: `Verified` ($1,180$), `Pending` ($55$), `Debarred` ($15$). | Quantifies the total eligible talent pool under institutional management. |
| **Placed Students** | Integer & % (e.g., `850 (68.0%)`) | Count of unique students with `Student.placement_status == PLACED` $\div$ Total Eligible Students. | Primary institutional success metric reported to university leadership and accreditation bodies. |
| **Total Jobs / Drives** | Integer (e.g., `48`) | Count of all published drives; includes sub-badges: `Active` ($18$), `Closed` ($28$), `Pending Approval` ($2$). | Monitors recruitment drive volume and corporate engagement breadth. |
| **Total Applications** | Integer (e.g., `4,920`) | Universal count of all submitted application records across all drives. | Measures total student participation and application load. |
| **ATS Shortlisted** | Integer (e.g., `1,410`) | Total unique applications that reached `ATS_SHORTLISTED` stage or higher. | Tracks conversion efficiency from initial submission to screening. |
| **Interviews Scheduled** | Integer (e.g., `920`) | Count of all interview slots generated; includes sub-counts: `Completed` ($780$), `Upcoming` ($140$). | Monitors operational panel workload and campus facility utilization. |
| **Total Offers Extended** | Integer (e.g., `510`) | Total offers issued by recruiters that passed TPC verification (`Offer.status != PENDING_TPC_VERIFICATION`). | Quantifies gross corporate hiring intent before candidate decisions. |
| **Accepted Offers** | Integer (e.g., `460`) | Total offers with `Offer.status == ACCEPTED` (confirmed hires). | Quantifies net corporate recruitment yield for the placement season. |
| **Average CTC (INR)** | Currency (e.g., `₹9.45 LPA`) | Arithmetic mean of `placed_ctc` across all unique placed students. | Key institutional compensation benchmark. |
| **Highest CTC (INR)** | Currency (e.g., `₹44.00 LPA`) | Maximum single `placed_ctc` recorded in the selected batch. | Headline institutional achievement figure. |

---

### 13.3 Application Pipeline Distribution by Status

To monitor recruitment pipeline velocity and identify bottlenecks (e.g., companies stalling on shortlists), the dashboard renders an interactive **Application Funnel & Status Distribution**:

```
TOTAL APPLICATIONS SUBMITTED: 4,920
==========================================================================
Stage                       Count    Percentage of Total Pool
--------------------------------------------------------------------------
APPLIED                     1,540    ████████████████░░░░░░░░  (31.3%)
ATS_SHORTLISTED               860    █████████░░░░░░░░░░░░░░░  (17.5%)
INTERVIEW_SCHEDULED           610    ██████░░░░░░░░░░░░░░░░░░  (12.4%)
OFFER_EXTENDED                 50    █░░░░░░░░░░░░░░░░░░░░░░░  ( 1.0%)
ACCEPTED (Placed)             460    █████░░░░░░░░░░░░░░░░░░░  ( 9.3%)
DECLINED (Student Exit)        35    ░░░░░░░░░░░░░░░░░░░░░░░░  ( 0.7%)
REJECTED (Disqualified)       980    ██████████░░░░░░░░░░░░░░  (19.9%)
AUTO_WITHDRAWN (Cascade)      385    ████░░░░░░░░░░░░░░░░░░░░  ( 7.8%)
==========================================================================
```

**Diagnostic Utility:** If the `APPLIED` count remains disproportionately high for a specific drive days after the deadline, TPC Admin can immediately identify that the recruiter has not completed candidate shortlisting and issue a reminder.

---

### 13.4 Department & Academic Cohort Breakdown

Provides comparative department-level metrics to ensure equitable placement across all engineering and academic disciplines:

| Academic Branch / Department | Total Students | Verified Eligible | Placed Count | Placement % | Average CTC (INR) | Highest CTC (INR) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Computer Science (CSE)** | 240 | 235 | 215 | **91.5%** | ₹14.20 LPA | ₹44.00 LPA |
| **Information Tech (IT)** | 180 | 176 | 154 | **87.5%** | ₹12.80 LPA | ₹38.00 LPA |
| **Electronics & Comm (ECE)** | 210 | 202 | 162 | **80.2%** | ₹9.10 LPA | ₹24.00 LPA |
| **Electrical Eng (EE)** | 160 | 152 | 108 | **71.1%** | ₹7.40 LPA | ₹18.00 LPA |
| **Mechanical Eng (ME)** | 220 | 210 | 132 | **62.9%** | ₹6.80 LPA | ₹16.50 LPA |
| **Civil Eng (CE)** | 140 | 130 | 68 | **52.3%** | ₹5.90 LPA | ₹11.00 LPA |
| **Chemical Eng (CHE)** | 100 | 95 | 58 | **61.1%** | ₹7.10 LPA | ₹14.00 LPA |
| **TOTAL INSTITUTION** | **1,250** | **1,200** | **897** | **74.8%** | **₹9.45 LPA** | **₹44.00 LPA** |

---

### 13.5 Job-Level Recruitment Performance Table

The dashboard includes a detailed, sortable table summarizing recruitment yield across every individual corporate campus drive:

```
=============================================================================================================================
Company & Role               Tier          CTC       Applied  ATS Shortlist  Interviewed  Offered  Accepted  Yield Rate
-----------------------------------------------------------------------------------------------------------------------------
Google - Cloud Software Eng  Super Dream   ₹32.0 LPA   412         45            28          8        8        1.9%
Microsoft - Support Eng      Dream         ₹16.5 LPA   520         60            42         14       12        2.3%
Amazon - SDE Intern + PPO    Super Dream   ₹28.0 LPA   485         52            35         11       10        2.1%
TCS - Digital Developer      Regular       ₹7.0 LPA    850        210           180         65       58        6.8%
Infosys - Specialist Prog    Regular       ₹9.5 LPA    720        165           140         48       44        6.1%
L&T - Core Graduate Trainee  Regular       ₹6.2 LPA    290         75            60         22       20        6.9%
=============================================================================================================================
```

* **Yield Rate:** Computed as $\frac{\text{Accepted Offers}}{\text{Total Applications}} \times 100$. Allows TPC to evaluate which corporate partnerships yield the highest conversion per applicant.

---

### 13.6 Global Filtering & Slicing Controls (V1 Parameters)

All dashboard metrics, charts, and tables dynamically re-calculate based on three primary persistent filter controls:

1. **Graduation Batch / Year:** Dropdown selector (e.g., `2026 Batch [Active]`, `2025 Batch`, `2024 Batch`). Enables year-over-year institutional comparisons.
2. **Academic Department / Branch:** Multi-select checklist allowing admins to isolate individual departments (e.g., view statistics solely for Mechanical and Civil Engineering).
3. **Placement Tier Category:** Single-select filter (`All Tiers`, `Regular Only`, `Dream Only`, `Super Dream Only`) to evaluate performance within specific salary brackets.
4. **Drive Type Selector:** (`All Opportunities`, `On-Campus Drives Only`, `Alumni Referrals Only`).

---

### 13.7 Data Freshness, Query Performance & Edge Handling

1. **Near Real-Time Computation:**
   - Standard queries aggregate directly against transactional tables with indexed foreign keys (`student_id`, `job_id`, `batch_year`, `branch`).
   - Query response time must execute in **under 1.5 seconds** across databases containing up to $50,000$ application rows.
2. **Zero-Data State Handling:**
   - At the beginning of a new placement season (zero jobs or applications posted), the dashboard displays clean, informative empty-state placeholders:
     - Metric cards render `0` with helper text: *"No active applications recorded for the selected batch."*
     - Tables display: *"No recruitment drives published for this cohort yet. Click 'Create Campus Drive' to begin."*
   - Division-by-zero errors (e.g., computing placement % when eligible students $= 0$) are strictly guarded, returning `0.0%`.
3. **Accreditation Export Integration:**
   - A persistent header button, **"Export Placement Executive Summary (Excel)"**, allows TPC Admins to download the aggregated KPI matrix, branch breakdown, and company yield tables directly as an audit-ready multi-tab `.xlsx` workbook.

---

## 14. Candidate & Application Data Export Requirements

The **Data Export Engine** enables TPC Admins (and authorized recruiters) to extract structured, filtered candidate rosters and application records for institutional audits, accreditation filings (NIRF/NAAC), corporate interview panel packages, and administrative decision-making.

---

### 14.1 Supported Filtering Criteria

The export engine provides multi-dimensional filtering, allowing administrators to scope exported datasets precisely:

| Filter Dimension | Supported Operator / Controls | Valid Values / Example | Business Objective |
| :--- | :--- | :--- | :--- |
| **Graduation Batch / Year** | Single Select / Multi-Select | `2026 Batch`, `2025 Batch` | Scope export to specific graduating classes. |
| **Academic Department** | Multi-Select Checklist | `CSE`, `IT`, `ECE`, `EE`, `ME`, `CE` | Generate branch-specific placement rosters. |
| **Verified CGPA Range** | Range Slider / Dual Inputs | Min: `0.00` to Max: `10.00` (e.g., $\ge 7.50$) | Filter candidate pools by academic cutoff. |
| **Active Backlog Limit** | Dropdown / Equality | `= 0`, $\le 1$, $\le 2$, `Any` | Isolate students with zero active backlogs. |
| **History of Backlogs** | Boolean / Toggle | `Cleared Backlogs Permitted: Yes/No` | Separate students with immaculate academic histories. |
| **Secondary Schooling %** | Range Inputs | 10th $\% \ge 60.0$, 12th $\% \ge 60.0$ | Satisfy legacy corporate cutoff criteria. |
| **Placement Standing** | Multi-Select Checklist | `Unplaced`, `Placed`, `Opted Out`, `Debarred` | Generate unplaced intervention lists or placed gazettes. |
| **Placement Tier** | Single Select | `All Tiers`, `Regular`, `Dream`, `Super Dream` | Filter placed students by salary tier bracket. |
| **Specific Corporate Drive** | Searchable Dropdown | Target Drive (e.g., "Google - Cloud SDE") | Export applicant pool for a specific company. |
| **Application Stage** | Multi-Select Checklist | `Applied`, `Shortlisted`, `Round 1`, `Offered`, etc. | Export candidate pools at a specific selection stage. |
| **Minimum ATS Match Score**| Numeric Threshold | $\ge 50\%$, $\ge 70\%$, $\ge 80\%$ | Export only top-ranked resume-matched applicants. |
| **Gender / Diversity** | Single Select | `All`, `Female Only`, `Male Only` | Generate corporate diversity hiring packages. |

---

### 14.2 Exported Data Field Schemas

The export engine provides three standard pre-built export schemas alongside a custom column selector:

#### Schema A: Institutional Candidate Master Roster
*Used for placement audits, branch reviews, and university gazettes:*
1. University Roll Number / Registration ID
2. Full Student Name
3. Official Institutional Email Address
4. Contact Mobile Phone Number
5. Academic Department / Branch
6. Degree Program (B.Tech, M.Tech, MCA)
7. Graduation Batch Year
8. Verified Cumulative CGPA (scale of 10.00)
9. Active Standing Backlog Count
10. Historical Cleared Backlog Count
11. 10th Standard Percentage & Board
12. 12th Standard / Diploma Percentage & Board
13. Current Placement Status (`UNPLACED` vs. `PLACED` vs. `OPTED_OUT`)
14. Placed Organization Name (if placed)
15. Placed Job Title / Designation (if placed)
16. Placed Annual Gross CTC (INR)
17. Placement Tier (`Regular`, `Dream`, `Super Dream`)
18. Profile TPC Verification Status (`Verified`, `Pending`, `Debarred`)

#### Schema B: Company Drive Applicant Shortlist Roster
*Used for corporate recruiters and visiting interview panels:*
1. Application ID
2. Student University Roll Number
3. Candidate Full Name
4. Department / Branch
5. Verified CGPA
6. Active Backlogs
7. Application Submission Date & Time (ISO-8601)
8. Selected Resume Document URL (Expiring Secure Signed Link)
9. ATS Composite Match Score (%)
10. Matched Skills Count vs. Required Skills Count (e.g., "8 of 10")
11. Current Lifecycle Stage (`Applied`, `Shortlisted`, `Round 1`, etc.)
12. Interview Slot Date & Time (if scheduled)
13. Interview Mode & Room / Meeting Link (if assigned)
14. Offer Status (`None`, `Offered`, `Accepted`, `Declined`)

#### Schema C: Accreditation Compliance Roster (NIRF / NAAC Format)
*Standardized tabular layout strictly adhering to statutory reporting schemas:*
- Academic Year | Program Name | Roll Number | Student Name | Graduating Cohort | Placement Type (On-Campus / Off-Campus) | Employer Name | Median CTC (INR) | Offer Letter Reference ID.

---

### 14.3 Technical Format Requirements: CSV vs. Excel

The export engine supports two structured file formats, each engineered with strict technical formatting standards:

#### A. Comma-Separated Values (`.csv`) Requirements
- **Character Encoding:** **UTF-8 with Byte Order Mark (BOM)** (`EF BB BF`). The BOM prefix is mandatory so that international character sets and Indian names open correctly in Microsoft Excel on Windows without character corruption.
- **Delimiter:** Standard comma (`,`).
- **Field Quoting:** All string fields containing commas, line breaks, or double quotes must be enclosed in double quotes (`"`). Literal double quotes within fields must be escaped as `""`.
- **MIME Type:** `text/csv; charset=utf-8`.
- **File Naming Standard:** `{ReportType}_{BatchYear}_{Timestamp}.csv` (e.g., `Eligible_Candidates_CSE_2026_20260924_123000.csv`).

#### B. Microsoft Excel (`.xlsx`) Requirements
- **Binary Standard:** Native Office Open XML (`.xlsx`) format.
- **Visual Styling & Formatting:**
  - **Header Row:** Bold white text with dark navy background fill (`#1E3A8A`), row height 28pt, vertical alignment centered.
  - **Auto-Filter:** Row 1 must have native Excel auto-filtering enabled across all columns.
  - **Column Widths:** Auto-fitted to contents with a minimum width of 12 characters and padding.
  - **Frozen Panes:** Top header row frozen (`Freeze Panes`) so headers remain visible when scrolling through thousands of candidate rows.
- **Data Type Fidelity:**
  - CGPA and percentages formatted natively as numbers (scale 2 decimals: `0.00`).
  - Monetary values formatted natively as currency (`₹#,##,##0`).
  - Timestamps formatted as localized date strings (`YYYY-MM-DD HH:MM`).
- **Multi-Tab Workbooks:** Master reports generate two worksheets:
  - Tab 1: `Executive KPI Summary` (Aggregated counts, averages, and branch percentages).
  - Tab 2: `Candidate Roster` (Individual student records).

---

### 14.4 Authorization & Access Control Matrix

Export rights are strictly governed to protect student privacy and proprietary corporate candidate pools:

| User Role | Export Permissions | Data Scope Boundary | Permitted Templates |
| :--- | :--- | :--- | :--- |
| **TPC Admin** | **Full Universal Access** | All students, all corporate drives, all historical batches institution-wide. | Master Roster, Drive Shortlists, NIRF/NAAC Gazette, Custom Column Builder. |
| **Recruiter / Alumni** | **Scoped Access Only** | Can export **only candidates who applied to their own job postings**. | Applicant Shortlist Roster for own drives; bundled resume ZIP archives. |
| **Student** | **Zero Batch Export Access** | Cannot export bulk datasets or peer records under any circumstance. | Can only download own personal placement verification slip / profile summary PDF. |

---

### 14.5 Empty Result Handling Protocol

If a user configures a set of filters that matches zero candidate or application records:

1. **Pre-Export Validation Check:**
   - When the user clicks "Generate & Download Export", the server immediately executes a `COUNT(*)` query against the filtered criteria.
2. **User Feedback (Zero Match Scenario):**
   - If `COUNT == 0`, the system halts file generation and displays an informative modal alert:
     *"No records found matching the selected filter criteria. Please adjust your branch, CGPA, or status filters and try again."*
   - Prevents generation and download of meaningless 0-byte files.
3. **Forced Download Edge Case:**
   - If an automated scheduled report or API query requests an empty dataset, the system generates a valid `.xlsx` or `.csv` file containing:
     - Row 1: Standard column headers.
     - Row 2: Informational notice: `[No candidate records matched the specified filter parameters]`.
     - Returns HTTP 200 with metadata header `X-Record-Count: 0`.

---

### 14.6 Sensitive Information Protection & Security Governance

Candidate exports aggregate sensitive PII and academic records. The platform enforces rigid data protection guardrails:

#### A. Exclusion of High-Risk System Secrets
The following attributes are **strictly excluded** from export payloads and can never be selected via the custom column builder:
- Password hashes and salt values.
- Session tokens, JWT secrets, and reset tokens.
- Internal administrative system audit logs.
- Recruiter internal evaluation notes (unless explicitly running a recruiter-internal panel export).

#### B. Formula Injection Prevention (CWE-1236 / CSV Injection)
Adversarial candidates might input malicious payloads into text fields (e.g., student name or portfolio link) such as `=cmd|' /C calc'!A0` or `@SUM(...)` designed to execute arbitrary code when opened in Excel.
- **Sanitization Rule:** The export engine inspects the first character of every string field.
- If the first character is an unquoted `=`, `+`, `-`, `@`, `\t`, or `\r`, the engine automatically prepends a single apostrophe (`'`) or wraps the cell in text formatting to neutralize executable formula interpretation.

#### C. Ephemeral In-Memory Streaming (No Server Disk Leaks)
- Exported files are generated on-the-fly and streamed directly to the client's HTTP response buffer.
- Files are **never saved permanently on application server disk drives**, eliminating the risk of unencrypted data dumps lingering in server temporary directories (`/tmp`).

#### D. Permanent Administrative Export Audit Logging
Every data export transaction is recorded in the institutional audit log:
```json
{
  "event": "DATA_EXPORT_GENERATED",
  "admin_id": "usr_tpc_admin_04",
  "export_format": "XLSX",
  "template_type": "MASTER_CANDIDATE_ROSTER",
  "filter_criteria": {
    "batch": 2026,
    "departments": ["CSE", "IT"],
    "min_cgpa": 7.50,
    "placement_status": "ALL"
  },
  "exported_record_count": 411,
  "client_ip_address": "192.168.1.45",
  "timestamp": "2026-09-24T00:14:00Z"
}
```

---

## 15. Notification System Requirements

The **Notification System** is an event-driven messaging service that keeps Students, Recruiters, and TPC Admins synchronized across critical recruitment lifecycle milestones. The system ensures timely communication, reduces interview no-shows, and guarantees auditability.

---

### 15.1 Architectural Delivery Channels & Specifications

The platform employs a dual-channel notification strategy: **In-App Alerts** and **Transactional Email Notifications**.

#### A. In-App Notification Requirements
* **Notification Center (Bell Icon):** Displayed prominently on the global navigation header for all authenticated personas.
* **Unread Counter Badge:** Real-time visual badge displaying the count of unread alerts (e.g., `3`). Updates dynamically upon receiving new alerts.
* **Chronological Alert Drawer:** Clicking the bell icon opens a scrollable drawer displaying recent notifications sorted in descending order (`created_at`).
* **Interactive Deep-Links (`action_url`):** Every in-app alert is an actionable link. Clicking the notification automatically:
  1. Marks the notification as `READ`.
  2. Routes the user directly to the relevant resource (e.g., specific Application view, Interview calendar slot, or Offer decision modal).
* **Batch Operations:** A "Mark all as read" button allows users to clear unread badges in a single click.
* **Persistence & Retention:** In-app notifications are persisted in the database (`Notifications` table) and retained for the duration of the active academic placement season (180 days).

#### B. Transactional Email Notification Requirements
* **Standardized Responsive Layout:** All outgoing emails use a clean, responsive HTML email template featuring:
  - Official University & Placement Cell header branding.
  - Clear, concise subject lines with recognizable prefixes (e.g., `[Campus Placements]`).
  - Itemized event details (Company, Role, Date, Time, Venue).
  - Prominent Call-to-Action (CTA) button linking directly to the portal page.
  - Plain-text fallback version for legacy email clients.
* **Asynchronous Execution:** Email dispatch must execute asynchronously (non-blocking) so that user interactions and API response times remain sub-second.
* **Calendar Integration:** Outgoing interview-related emails must attach a standardized RFC 5545 `.ics` file for 1-click addition to candidate calendars.

---

### 15.2 Event-Driven Notification Matrix

The table below defines every core recruitment lifecycle event, specifying the triggering condition, target recipients, in-app payload, and transactional email specifications:

| Event Identifier | Triggering Condition | Target Recipient(s) | Urgency / Priority | In-App Notification Spec | Transactional Email Spec |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **EVT-01: Application Submitted** | Student clicks "Submit Application" and server successfully persists application record. | **Student** (Confirmation)<br>**Recruiter** (Pool Alert) | **Medium** | **To Student:** *"Application Submitted: Successfully applied for [Job Title] at [Company]."<br>URL: `/student/applications/[app_id]`* | **Subject:** `[Placements] Application Confirmed: [Job Title] - [Company]`<br>**Body:** Confirms receipt of application, snapshots verified credentials, and provides portal tracking link. |
| **EVT-02: ATS Shortlisted** | Recruiter or system transitions application stage to `ATS_SHORTLISTED`. | **Student** | **High** | **To Student:** *"Congratulations! You have been shortlisted by [Company] for [Job Title]."<br>URL: `/student/applications/[app_id]`* | **Subject:** `[Placements] Shortlisted: [Company] - [Job Title]`<br>**Body:** Informs student of advancement, provides overview of upcoming interview process, and reminds them to monitor calendar. |
| **EVT-03: Application Rejected** | Recruiter marks application as `REJECTED` at screening or interview stages. | **Student** | **Low** | **To Student:** *"Application Status Update: [Company] has concluded review for [Job Title]."<br>URL: `/student/applications/[app_id]`* | **Subject:** `[Placements] Application Update: [Company] - [Job Title]`<br>**Body:** Respectful, professional notification that candidate was not selected, with encouraging guidance to apply for upcoming active drives. |
| **EVT-04: Interview Scheduled** | Recruiter/Admin publishes interview schedule and assigns time slot to candidate. | **Student** | **Urgent** | **To Student:** *"Interview Scheduled: [Round Name] with [Company] on [Date] at [Time]."<br>URL: `/student/interviews/[interview_id]`* | **Subject:** `[ACTION REQUIRED] Interview Scheduled: [Company] - [Round Name]`<br>**Body:** Date, start/end time, mode (Virtual link or Campus room), preparation instructions, and `.ics` calendar attachment. |
| **EVT-05: Interview Rescheduled** | Recruiter/Admin updates date, time, or venue for an existing interview slot. | **Student** | **Urgent** | **To Student:** *"Interview Rescheduled: Your [Company] interview has moved to [New Date/Time]."<br>URL: `/student/interviews/[interview_id]`* | **Subject:** `[URGENT] Rescheduled Interview: [Company] - [Round Name]`<br>**Body:** Highlights updated time/venue diff, details reschedule reason, and includes updated `.ics` calendar invitation. |
| **EVT-06: Interview Cancelled** | Recruiter or TPC Admin formally cancels a scheduled interview slot. | **Student** | **High** | **To Student:** *"Interview Cancelled: [Round Name] with [Company] on [Date] has been cancelled."* | **Subject:** `[Notice] Interview Cancelled: [Company] - [Round Name]`<br>**Body:** Communicates cancellation reason; reassures candidate regarding application status. |
| **EVT-07: Offer Extended** | Recruiter issues offer and TPC Admin verifies and releases it to candidate. | **Student** | **Critical** | **To Student:** *"Official Job Offer Extended: [Company] has offered you [Role] (₹[CTC])!"<br>URL: `/student/offers/[offer_id]`* | **Subject:** `[CRITICAL ACTION] Official Job Offer Extended: [Company]`<br>**Body:** Summary of compensation package, decision deadline countdown (e.g., 72 hours), and direct link to review and accept/decline. |
| **EVT-08: Offer Accepted** | Student confirms binding offer acceptance in the portal. | **Recruiter**<br>**TPC Admin** | **High** | **To Recruiter & TPC:** *"Offer Accepted! Candidate [Student Name] has accepted the offer for [Role]."<br>URL: `/recruiter/drives/[job_id]/applicants`* | **Subject:** `[Placement Confirmed] Candidate Accepted Offer: [Student Name] - [Company]`<br>**Body:** Formal confirmation that student accepted the offer; details automated placement lock status. |
| **EVT-09: Offer Declined** | Student formally declines extended offer or decision window expires. | **Recruiter**<br>**TPC Admin** | **High** | **To Recruiter:** *"Offer Declined: Candidate [Student Name] has declined the offer for [Role]. Reason: [Category]."* | **Subject:** `[Notice] Offer Declined: [Student Name] - [Company]`<br>**Body:** Details candidate decline reason; alerts recruiter to backfill vacancy from waitlist. |

---

### 15.3 Notification Lifecycle & State Management

1. **At-Least-Once Delivery Guarantee:**
   - In-app notification creation and application lifecycle mutations execute within the same database transaction, guaranteeing that an in-app notification is never lost if a state change succeeds.
2. **State Mutations:**
   - Default State: `UNREAD` (`is_read: false`).
   - Read State: `READ` (`is_read: true`, `read_at: timestamp`).
3. **Badge Synchronization:**
   - Opening the notification drawer or clicking an individual item decrements the global unread badge count in real time.
4. **Anti-Spam Batch Aggregation (Recruiter Experience):**
   - If a recruiter bulk-shortlists 40 candidates in a single action, the system dispatches 40 individualized in-app/email alerts to the respective candidates.
   - For the recruiter, the system generates **one consolidated summary alert**: *"Batch Action Complete: 40 candidates successfully advanced to Shortlisted for [Role]."*

---

### 15.4 Notification Failure & Privacy Handling

1. **Email Delivery Failures (Bounces):**
   - If an institutional email address bounces or is temporarily unreachable, the system logs the delivery failure in the notification audit log.
   - The in-app notification remains fully operational as the primary resilient communication channel.
2. **Sensitive Data Protection in Emails:**
   - Password hashes, detailed interviewer scorecard notes, and private student academic backlogs are **never** included in email subject lines or notification previews.
   - Direct download links to resumes or offer letters in emails are always signed, time-limited tokens ($\le 15\text{ minutes}$), requiring portal authentication for access.

---

## 16. Platform Security Requirements

To ensure institutional data integrity, protect student privacy, and maintain fair recruitment operations, the platform implements a pragmatic, defense-in-depth security architecture. These requirements are tailored for a production-grade campus placement portal without unnecessary enterprise over-engineering.

---

### 16.1 Authentication Architecture

1. **Multi-Persona Authentication:**
   - Unified authentication boundary supporting three distinct roles: `STUDENT`, `RECRUITER`, and `TPC_ADMIN`.
   - **Student Identity:** Authenticated via institutional email (`@college.edu`). Accounts are either pre-provisioned via TPC bulk roster upload or registered with mandatory email domain validation.
   - **Recruiter Identity:** Registered with company work email. Accounts remain in a `PENDING_APPROVAL` state until verified and activated by TPC Admin.
   - **TPC Admin Identity:** Bootstrapped via secure environment seeding; administrative privileges cannot be self-assigned.
2. **Session Invalidation & Logout:**
   - Explicit logout immediately clears client-side tokens/cookies and adds the active JWT ID (`jti`) or session token to an in-memory/database revocation blacklist until expiration.
   - Password reset or security update automatically revokes all active sessions for that user account.

---

### 16.2 Password Security Standards

1. **Hashing Algorithm:**
   - Passwords must be hashed using **bcrypt** with a minimum work factor (salt rounds) of **12** (or **Argon2id** with memory cost $\ge 64\text{ MB}$, time cost $\ge 3$).
   - Raw plaintext passwords must never touch disk, persistent storage, or application log files.
2. **Password Complexity Policy:**
   - Minimum length: 8 characters (maximum: 64 characters to prevent hash-DoS).
   - Mandatory character diversity: At least one uppercase letter (`A-Z`), one lowercase letter (`a-z`), one numeric digit (`0-9`), and one special symbol (`!@#$%^&*()_+-=[]{}`).
3. **Authentication Feedback Protection:**
   - Login failure responses must use identical, non-revealing error messages (e.g., *"Invalid email or password"*) to prevent user enumeration.

---

### 16.3 JWT & Session Token Security

1. **Storage & Transmission:**
   - Tokens must be stored in **`HttpOnly`**, **`Secure`** (HTTPS only), and **`SameSite=Lax`** cookies to neutralize client-side script theft (XSS mitigation).
   - Alternatively, when utilizing `Authorization: Bearer <token>` headers, tokens must reside strictly in memory (React runtime state) and never in unencrypted `localStorage` or `sessionStorage`.
2. **Token Lifecycle & Expiration:**
   - **Access Token:** Short-lived validity of **15 to 30 minutes**.
   - **Refresh Token:** Longer validity of **7 days**, stored securely in an `HttpOnly` cookie with automatic token rotation (old refresh token invalidated upon issuing a new one).
3. **Token Payload Minimization & Scope (JWT = Identity + Role Context):**
   - Payloads must contain only non-sensitive identity and authentication context: `sub` (User ID), `role` (`STUDENT` | `RECRUITER` | `TPC_ADMIN`), and standard token metadata (`iat`, `exp`, `token_version`).
   - Never store passwords, CGPA, phone numbers, or private student identifiers inside the JWT payload.
4. **Authoritative State Principle (Database = Authoritative Mutable State):**
   - JWT MUST NOT be treated as the authoritative source for mutable, critical account, placement, or application state.
   - All mutable, security-sensitive, or placement-sensitive state must be stored in and validated against the database in real time.
   - **DB-Authoritative State Elements:**
     - Account verification & organization standing (e.g., recruiter approved vs. pending verification; active vs. suspended organization).
     - Debarred / disciplinary status (e.g., student debarred by TPC Admin for disciplinary violations).
     - Placement policy state (e.g., student marked placed and locked from lower/equal tier drives).
     - Application status and stage progression (`APPLIED`, `ATS_SHORTLISTED`, `INTERVIEW_SCHEDULED`, `OFFER_EXTENDED`, `ACCEPTED`, `REJECTED`).
     - Eligibility evaluation results (`ELIGIBLE` vs. `INELIGIBLE`).
     - Job opportunity lifecycle state (`PENDING_APPROVAL` vs. `ACTIVE` vs. `CLOSED`).
   - **Server-Side Validation for Sensitive Operations:**
     - For any sensitive or state-mutating operations (applying to drives, approving jobs, scheduling interviews, extending/accepting offers, viewing restricted pools), the backend MUST query and validate live database state rather than trusting stale JWT claims.
     - **Debarred Student Block:** A student whose JWT claims `role: STUDENT` but whose DB state is `is_debarred: true` must be blocked immediately from sensitive placement actions.
     - **Suspended Recruiter Block:** A recruiter whose JWT was issued while active, but whose organization or account was subsequently set to `SUSPENDED` in the database, must not continue performing protected recruiter actions merely because an unexpired JWT exists.
     - **Authoritative Application & Placement State:** Application stage progression and placement lock status must always come directly from current database state, never from JWT or client-side storage.
     - **Authoritative Eligibility Gating:** Eligibility must always be evaluated by the eligibility engine against current database records before application creation.
5. **Secret Management:**
   - JWT signing keys must be strong, cryptographically random strings (minimum 256 bits / 32 bytes) loaded strictly from environment variables (`JWT_SECRET`). Hardcoding secrets is strictly prohibited.

---

### 16.4 Role-Based Access Control (RBAC)

1. **Middleware-Enforced Boundaries & Authoritative State Checks:**
   - Every protected API route must pass through an authentication gate followed by a role authorization middleware (e.g., `verifyRole(['TPC_ADMIN'])`).
   - **Role Routing vs. Authoritative State:** While the user's role is extracted from the JWT for initial routing and boundary checks, any sensitive or state-altering operation must verify the user's authoritative record in the database before proceeding.
2. **Role Boundaries & Capabilities:**
   - `STUDENT`: Read-only access to published eligible drives; CRUD operations strictly limited to own profile, own resume versions, and own applications.
   - `RECRUITER`: Read/write access strictly limited to own company job postings, applicant pools for their postings, and interview schedules created by their organization.
   - `TPC_ADMIN`: Full administrative read/write oversight across all students, recruiters, drives, stages, audit logs, and analytics.

---

### 16.5 Resource Ownership & Insecure Direct Object Reference (IDOR) Defense

1. **Strict Ownership Verification:**
   - Role authorization alone is insufficient; every endpoint manipulating a specific entity (`/api/applications/:id`, `/api/resumes/:id`, `/api/interviews/:id`) must verify resource ownership at the data-access layer.
2. **Ownership Rules:**
   - **Student Access:** A student requesting `/api/applications/:id` must satisfy `application.student_id === req.user.id`.
   - **Recruiter Access:** A recruiter updating `/api/applications/:id/stage` must verify `application.job.recruiter_id === req.user.id` (or matches their approved company ID).
3. **Non-Enumerative Responses:**
   - If an authenticated user attempts to access a valid resource ID that belongs to another entity, the API must return **`404 Not Found`** (or `403 Forbidden` without details) to prevent attacker ID enumeration.

---

### 16.6 API Route Authorization Standards

| Route Namespace | Authentication Required | Authorized Roles | Resource Ownership Check |
| :--- | :--- | :--- | :--- |
| `/api/auth/*` (Login, Register) | No | Public | N/A |
| `/api/student/profile` | Yes | `STUDENT` | Yes (`req.user.id`) |
| `/api/student/resumes/*` | Yes | `STUDENT` | Yes (`resume.student_id === req.user.id`) |
| `/api/student/applications` | Yes | `STUDENT` | Yes (`app.student_id === req.user.id`) |
| `/api/jobs` (Active drives) | Yes | `STUDENT`, `RECRUITER`, `TPC_ADMIN` | Filtered by student eligibility |
| `/api/recruiter/jobs/*` | Yes | `RECRUITER`, `TPC_ADMIN` | Recruiter can only modify own jobs |
| `/api/recruiter/interviews/*` | Yes | `RECRUITER`, `TPC_ADMIN` | Recruiter can only manage own interviews |
| `/api/admin/*` | Yes | `TPC_ADMIN` | TPC Admin global authorization |

---

### 16.7 Resume & File Upload Security

1. **Allowed File Format:**
   - Strictly `.pdf` documents. All executable formats (`.exe`, `.sh`, `.bat`), script-bearing files (`.html`, `.svg`, `.js`), and editable office files (`.docx`, `.odt`) are rejected.
2. **Multi-Layer Validation Pipeline:**
   - **Extension Inspection:** Filename must end in `.pdf` (case-insensitive).
   - **MIME Type Validation:** Request `Content-Type` header must equal `application/pdf`.
   - **Magic Byte Verification:** File header must begin with `%PDF-` (`0x25 0x50 0x44 0x46`). Files failing magic byte checks are rejected immediately prior to storage or processing.
3. **Payload Size Hard Cap:**
   - Maximum upload size: **5 MB**. Files exceeding this threshold are rejected at the reverse proxy / API gateway before reaching memory buffers (`413 Payload Too Large`).
4. **Storage Isolation & Execution Prevention:**
   - Uploaded resumes must never be saved into the web server’s publicly executable document root.
   - Files are stored in secure cloud storage (Cloudinary Private Storage or AWS S3 Private Bucket) with randomized UUID file keys (e.g., `resumes/a9b2c3d4-e5f6-7890.pdf`).
   - Resumes are served to authorized viewers exclusively via short-lived signed URLs ($\le 15\text{ minutes}$) with `Content-Disposition: inline` and `X-Content-Type-Options: nosniff`.

---

### 16.8 Input Validation & Sanitization

1. **Schema-Driven Controller Boundary:**
   - All inbound JSON payloads and query parameters must be validated against strict schemas (e.g., **Zod** or **Joi**) before reaching business logic or service layers.
2. **Payload Whitelisting:**
   - Unknown or unwhitelisted payload fields must be stripped or trigger a validation error (`400 Bad Request`).
3. **Data Type & Range Constraints:**
   - CGPA: Validated as floating-point number $0.00 \le \text{CGPA} \le 10.00$ with max 2 decimal places.
   - Batch Year: Validated as a 4-digit positive integer ($\text{Current Year} - 2 \le \text{Batch} \le \text{Current Year} + 4$).
   - Mobile Numbers: Sanitized and matched against standard E.164 / 10-digit formats.
   - Text inputs (Job titles, descriptions, candidate names): Trimmed of leading/trailing whitespace and length-bounded.

---

### 16.9 SQL / NoSQL Injection Defense

1. **Strict Parameterization:**
   - All database queries must be executed via modern ORM / query-builder abstractions (e.g., **Prisma**, **Drizzle**, or **Mongoose**) that utilize prepared statements and parameterized queries under the hood.
2. **Ban on Dynamic String Concatenation:**
   - Raw string interpolation inside queries (e.g., `"SELECT * FROM users WHERE email = '" + input + "'"` ) is strictly prohibited in the codebase.
3. **Export Formula Injection Defense:**
   - For CSV exports, any cell beginning with dangerous formula characters (`=`, `+`, `-`, `@`, `\t`, `\r`) must be prepended with a single quote (`'`) to neutralize CSV Injection (CWE-1236).

---

### 16.10 Cross-Site Scripting (XSS) Prevention

1. **Context-Aware Output Encoding:**
   - React JSX auto-escaping must be leveraged for rendering all dynamic text nodes (e.g., student names, company names, job titles).
2. **Rich Text / Description Sanitization:**
   - If job descriptions support formatting, incoming HTML strings must be sanitized using **DOMPurify** before storage and before rendering (`dangerouslySetInnerHTML`).
3. **HTTP Security Headers:**
   - The application server must deliver modern security headers via Helmet middleware:
     - `X-Content-Type-Options: nosniff` (prevents MIME sniffing).
     - `X-Frame-Options: DENY` (prevents Clickjacking).
     - `Content-Security-Policy (CSP)`: Restricts script execution to authorized domains.
     - `Referrer-Policy: strict-origin-when-cross-origin`.

---

### 16.11 Cross-Site Request Forgery (CSRF) Mitigation

1. **Cookie-Based Sessions:**
   - Auth cookies must be configured with `SameSite=Lax` or `SameSite=Strict`.
   - In conjunction with modern browsers, `SameSite=Lax` effectively prevents cross-site POST/PUT/DELETE requests from transmitting authentication credentials.
2. **Bearer Token Architecture:**
   - When API requests use `Authorization: Bearer <token>`, standard browser CSRF attacks are fundamentally mitigated because browsers never attach custom authorization headers to cross-site requests automatically.

---

### 16.12 Rate Limiting & Denial-of-Service (DoS) Protection

To prevent brute-force attacks and resource exhaustion, tier-based rate limiting is enforced at the API gateway / Express middleware layer:

| Route Scope | Rate Limit Window | Max Requests | Violation Action | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **Auth Routes (`/api/auth/login`)** | 15 minutes | 5 attempts | `429 Too Many Requests` (Lock IP/email 15 min) | Mitigates credential stuffing & brute-force |
| **Password Reset (`/api/auth/forgot-password`)** | 60 minutes | 3 attempts | `429 Too Many Requests` | Prevents email bombarding / spam |
| **File Upload (`/api/student/resumes`)** | 60 minutes | 10 uploads | `429 Too Many Requests` | Prevents storage/parsing resource exhaustion |
| **Search & ATS Recalculation** | 1 minute | 30 requests | `429 Too Many Requests` | Protects CPU during PDF parsing & matching |
| **Global Standard API Routes** | 1 minute | 100 requests | `429 Too Many Requests` | General platform stability and DoS defense |

---

### 16.13 Sensitive Data Protection & Privacy

1. **Data in Transit:**
   - All communication between client and server must be encrypted over **TLS 1.3** (HTTPS enforced). Plain HTTP requests must be automatically redirected with a `301 Moved Permanently`.
2. **Data at Rest:**
   - Student academic transcripts, resumes, and system secrets must reside in encrypted databases and encrypted cloud storage volumes (AES-256).
3. **PII Visibility Scoping:**
   - Candidate personal phone numbers and private emails are concealed from public directories. They are only exposed to recruiters for candidates who have **actively applied** to their drive.
   - Interviewer notes, private scores, and placement cell disciplinary remarks are strictly hidden from student interfaces.
4. **Environment Secrets:**
   - Database credentials, cloud API keys, and JWT secrets must be loaded from `.env` files and never committed to version control (`.gitignore` enforcement).

---

### 16.14 Secure Error Responses & Information Leakage Prevention

1. **Production Error Masking:**
   - In production environments (`NODE_ENV === 'production'`), detailed stack traces, database schema names, SQL errors, and library versions must never be exposed to API consumers.
2. **Standardized Error Envelope:**
   - All API error responses must adhere to a predictable, non-revealing JSON format:
     ```json
     {
       "success": false,
       "error": {
         "code": "RESOURCE_NOT_FOUND",
         "message": "The requested resource could not be found."
       },
       "timestamp": "2026-09-24T00:16:00Z"
     }
     ```
3. **HTTP Status Code Precision:**
   - Use correct HTTP semantics: `400 Bad Request` (validation error), `401 Unauthorized` (missing/invalid token), `403 Forbidden` (insufficient role/ownership), `404 Not Found` (item absent), `429 Too Many Requests` (rate limited), and `500 Internal Server Error` (unexpected crash).

---

### 16.15 Auditability & Security Logging

1. **Immutable Audit Trail:**
   - Critical lifecycle and administrative events must generate an immutable audit log record stored in the database.
2. **Audited Event Catalog:**
   - TPC Admin stage overrides and candidate debarments.
   - Recruiter offer extensions, deadline changes, and salary adjustments.
   - Student offer acceptances and declines.
   - Bulk student roster imports and GPA modifications.
   - Candidate data exports (recording admin ID, filter parameters, and record count).
3. **Audit Log Data Schema:**
   ```json
   {
     "log_id": "aud_9f8e7d6c5b",
     "timestamp": "2026-09-24T00:16:00Z",
     "actor_id": "usr_admin_01",
     "actor_role": "TPC_ADMIN",
     "action": "STUDENT_DEBARRED",
     "target_resource": "STUDENT_RECORD",
     "target_id": "usr_stu_104",
     "ip_address": "192.168.1.50",
     "user_agent": "Mozilla/5.0 ...",
     "details": {
       "reason": "Placement Policy Violation - Unreported External Offer",
       "previous_status": "ELIGIBLE",
       "new_status": "DEBARRED"
     }
   }
   ```

---

## 17. Non-Functional Requirements (NFRs)

The Non-Functional Requirements specify the quality attributes, operational limits, performance targets, and architectural constraints governing the platform. These criteria are calibrated specifically for a college placement portal serving typical university cohorts (1,500–3,000 students) with high-integrity, production-style rigor while avoiding exaggerated enterprise SLA claims.

---

### 17.1 Performance Requirements

1. **API Response Latencies (p95):**
   - **Standard Read/Write Endpoints:** $\le 250\text{ ms}$ under normal operating conditions for standard operations (student profile fetch, job postings catalog, application status query).
   - **Complex Analytical Queries:** $\le 1.0\text{ s}$ for the TPC Admin analytics dashboard, branch-wise placement breakdown, and export query aggregations.
   - **Dynamic Eligibility Engine:** $\le 150\text{ ms}$ per evaluation run per student-job pairing.
2. **ATS Resume Parsing & Matching:**
   - **Turnaround Time:** $\le 2.5\text{ seconds}$ for complete end-to-end processing (PDF text extraction, keyword tokenization, canonical skill resolution, and Jaccard similarity scoring) for standard 1–2 page resumes ($\le 5\text{ MB}$).
3. **Frontend Page Load & Rendering:**
   - **First Contentful Paint (FCP):** $\le 1.5\text{ seconds}$ on standard broadband / campus Wi-Fi connections.
   - **Time to Interactive (TTI):** $\le 2.5\text{ seconds}$.
   - **Optimistic UI Updates:** Instant visual feedback (loading skeletons or spinners) for all asynchronous mutations (application submissions, shortlist toggles).

---

### 17.2 Scalability & Concurrency Targets

1. **Cohort & Record Sizing (Typical University Profile):**
   - Active student roster: **1,500 to 3,000 students** per graduating batch.
   - Concurrent active recruitment drives: **20 to 50 companies** recruiting in parallel during peak recruitment season.
   - Lifetime applications per season: **15,000 to 30,000 application records**.
2. **Concurrency Peak Rush (Deadline Surges):**
   - The platform must support **500 to 800 concurrent active users** during critical surge windows (e.g., the final 15 minutes before a Tier-1 "Dream" drive closes, or immediately following an interview shortlist announcement).
3. **Stateless Scalability:**
   - The application backend must remain strictly stateless. Session state resides in client tokens/cookies, enabling horizontal scaling across multiple Node.js/Express process instances behind an Nginx reverse proxy or load balancer.
4. **Database Connection Pooling:**
   - Database connections must be managed via connection pooling (e.g., Prisma connection pool or pgBouncer) capped at 20 connections per instance to avoid connection exhaustion under surge loads.

---

### 17.3 Security Standards Alignment

*The platform adheres to the full security specification detailed in Section 16, emphasizing:*
1. **Encryption Standards:** Mandatory TLS 1.3 in transit with HSTS; AES-256 for data at rest.
2. **Identity Boundaries:** Strict RBAC separating Student, Recruiter, and TPC Admin with route-level middleware and resource ownership (IDOR) verification on all entity mutations.
3. **Injection Immunity:** 100% prepared statements via ORM abstractions; formula sanitization for CSV exports (CWE-1236); DOMPurify sanitization for rich text descriptions.
4. **Credential Safety:** bcrypt password hashing with work factor $\ge 12$; no sensitive claims or secrets stored in JWT payloads.

---

### 17.4 Availability & Maintenance Windows

1. **Availability Target:**
   - **99.5% Uptime** during the active placement season (typically August to April), equivalent to less than 3.6 hours of unplanned downtime per month.
2. **Zero Unscheduled Downtime Windows:**
   - Zero unplanned downtime permitted during active application deadline countdowns (the final 2 hours of any published drive).
3. **Scheduled Maintenance Protocol:**
   - Maintenance activities, migrations, and system upgrades must be scheduled during low-traffic overnight hours (**02:00 AM – 05:00 AM IST**).
   - TPC Admins and students must be notified via a persistent in-app banner at least **24 hours in advance** of scheduled maintenance.

---

### 17.5 Reliability & Fault Tolerance

1. **ACID Transactional Guarantees:**
   - Multi-table domain mutations must execute inside atomic database transactions:
     - Student application submission (application record creation + resume/CGPA snapshot persistence).
     - Student offer acceptance (offer status update + student placement lock + cascading auto-withdrawal of all competing active applications in lower tiers).
   - If any step fails, the entire transaction is rolled back cleanly to preserve state consistency.
2. **Graceful Service Degradation:**
   - Auxiliary service failures must never block core transactional workflows. If the external email SMTP service is down, the student's application submission or offer acceptance must still succeed, with the failed notification queued or logged.
3. **Idempotent Operations:**
   - State-changing API endpoints (e.g., `/api/applications/:id/accept-offer`) must be idempotent to prevent race conditions or duplicate submissions caused by aggressive double-clicks.
4. **Automated Retry Mechanisms:**
   - Transient cloud storage uploads (resume PDFs) and outgoing transactional email dispatch must incorporate exponential backoff retries (maximum 3 attempts).

---

### 17.6 Maintainability & Code Quality

1. **Modular Layered Architecture:**
   - Strict separation of concerns following standard clean architecture patterns:
     `Routes` $\rightarrow$ `Middleware (Auth/RBAC)` $\rightarrow$ `Controllers` $\rightarrow$ `Services (Business Logic)` $\rightarrow$ `Data Access Layer (ORM/DB)`.
2. **Contract Consistency:**
   - Strict TypeScript interfaces and schemas shared or mapped between frontend client code and backend validation layers (e.g., Zod schemas).
3. **Automated Test Coverage:**
   - Automated unit and integration testing covering $\ge 80\%$ of core business logic:
     - Eligibility Engine rule evaluation and back-log threshold validations.
     - Application Lifecycle state machine transition table and illegal jump defenses.
     - ATS Jaccard similarity mathematical calculations and scoring penalty caps.
     - Offer acceptance policy cascade and auto-withdrawal triggers.
4. **Environment Isolation & Configuration:**
   - Strict adherence to 12-Factor App principles. Zero hardcoded secrets, API endpoints, or database URLs in codebase; fully validated `.env` configuration on application startup.

---

### 17.7 Observability & Telemetry

1. **Structured JSON Logging:**
   - Standardized JSON logs emitted to stdout/stderr:
     ```json
     {
       "timestamp": "2026-09-24T00:17:00Z",
       "level": "INFO",
       "correlation_id": "req_8a7b6c5d4e",
       "method": "POST",
       "path": "/api/student/applications",
       "status_code": 201,
       "latency_ms": 112,
       "user_id": "usr_stu_42",
       "role": "STUDENT"
     }
     ```
2. **System Health Probes:**
   - `/api/health`: Returns HTTP 200 with service status, database connectivity verification, memory utilization, and server uptime.
3. **Centralized Error Monitoring:**
   - Unhandled exceptions must be captured with stack traces and request correlation IDs in server-side logs, while returning a sanitized `500 Internal Server Error` to the client.
4. **Audit Log Inspection:**
   - Dedicated administrative audit log interface allowing TPC Admins to filter, inspect, and trace all administrative overrides, debarments, and policy modifications.

---

### 17.8 Usability & Interaction Standards

1. **Primary Persona Optimization:**
   - **Desktop-First Optimization:** Recruiter and TPC Admin workflows (multi-column applicant tables, resume inspection drawers, interview schedule matrices) optimized for desktop viewport resolutions ($\ge 1280\times 800\text{px}$).
   - **Responsive Hybrid Support:** Student portals fully responsive across mobile, tablet, and desktop viewports, ensuring seamless status tracking and interview calendar access on smartphones ($\ge 375\text{px}$).
2. **Action Transparency & Feedback:**
   - Every user mutation must yield immediate visual feedback within $\le 100\text{ ms}$ (loading state, button spinner, or disabled action state).
   - Success and error results must be signaled via non-intrusive toast notifications (auto-dismiss after 4 seconds).
3. **Contextual Error Explanations:**
   - Ineligible students must receive clear, actionable diagnostics explaining exact unmet criteria (e.g., *"Ineligible: Minimum CGPA requirement is 7.50; your verified CGPA is 7.12"*), eliminating mystery rejections.
4. **Confirmation Gates for Destructive Actions:**
   - Two-step confirmation dialogs required for irreversible actions:
     - Student declining an offer (with mandatory reason selection).
     - Student accepting an offer (explicit confirmation that competing applications will be withdrawn).
     - Admin debarring a candidate or overriding an application stage.

---

### 17.9 Accessibility (A11y) Standards

1. **Compliance Target:**
   - Adherence to **WCAG 2.1 Level AA** standards across core student-facing workflows.
2. **Visual Contrast:**
   - Text and interactive elements must maintain a minimum contrast ratio of **4.5:1** against backgrounds for standard text, and **3:1** for large text and UI components.
3. **Full Keyboard Navigability:**
   - All interactive controls (navigation links, filter dropdowns, modal dialogs, application forms) must be fully operable using keyboard inputs alone (`Tab`, `Shift+Tab`, `Enter`, `Space`, `Escape`).
   - Modal dialogs must trap keyboard focus while open and restore focus upon dismissal.
4. **Semantic HTML & Screen Reader Support:**
   - Use of semantic elements (`<nav>`, `<main>`, `<article>`, `<section>`, `<header>`, `<footer>`).
   - Accessible form controls with explicit `<label for="...">` associations.
   - Screen-reader accessible alert indicators for form validation errors and status updates using `aria-live="polite"`.

---

### 17.10 Data Integrity & Consistency Rules

1. **Relational Constraints & Foreign Keys:**
   - Enforce database-level foreign key constraints (`ON DELETE RESTRICT` or soft deletes) to eliminate orphaned records across students, jobs, applications, and interviews.
2. **Snapshot Immutability:**
   - Application submissions must snapshot academic credentials (verified CGPA, active backlog count, branch) and the selected resume file reference at the time of submission. Subsequent student profile edits or resume uploads must not mutate historical application snapshots.
3. **Anti-Collision & Unique Constraints:**
   - Database-level unique constraints preventing duplicate applications:
     `UNIQUE(student_id, job_id)`.
   - Interview scheduling collision prevention: Database checks ensuring a student cannot have overlapping active interview time slots ($\pm 15\text{ minute}$ buffer).
4. **Backup & Recovery Protocol:**
   - Automated daily database backups with retention for 30 days during active placement season.
   - Recovery Time Objective (RTO): $\le 2\text{ hours}$.
   - Recovery Point Objective (RPO): $\le 24\text{ hours}$ (or last nightly snapshot).

---

## 18. Comprehensive Edge-Case & Exception Catalog

This catalog details the system's explicit deterministic behavior across unexpected inputs, network glitches, race conditions, edge states, and malicious attempts. Every edge case is defined with its **Scenario**, **Expected Behavior**, and the underlying **Reason**.

---

### 18.1 Authentication Edge Cases

#### EC-AUTH-01: Login Attempt on Unapproved Recruiter Account
* **Scenario:** A newly registered recruiter attempts to log in before the TPC Admin has verified and activated their company account.
* **Expected Behavior:** Authentication is rejected with HTTP `403 Forbidden` and message: *"Your account is pending verification by the Training & Placement Cell. You will receive an email once approved."* Session token is not issued.
* **Reason:** Prevents unvetted or fraudulent entities from accessing student pools, resumes, or posting bogus recruitment drives.

#### EC-AUTH-02: Simultaneous Login from Multiple Browsers / IP Addresses
* **Scenario:** A student logs into the portal from a campus lab computer while already logged in on their personal mobile phone.
* **Expected Behavior:** Both sessions remain active if valid refresh tokens exist, or the system limits concurrent sessions to 3 active devices per user. Password changes invalidate all active sessions immediately.
* **Reason:** Students frequently switch between campus lab systems and personal mobile devices during recruitment days.

#### EC-AUTH-03: Token Expiry During Multi-Step Application or Offer Decision
* **Scenario:** A student’s 15-minute access token expires while reviewing an offer letter modal or filling an application form.
* **Expected Behavior:** The client background interceptor automatically utilizes the `HttpOnly` refresh token to acquire a new access token without reloading the page or losing form state. If the refresh token is also expired, the user is redirected to `/login` with a `redirect_to` query parameter preserving destination context.
* **Reason:** Prevents loss of candidate form inputs during prolonged reading sessions while maintaining tight access token lifecycles.

#### EC-AUTH-04: Debarred or State-Modified Student Operating with Active JWT
* **Scenario:** A student holds a valid, unexpired 15-minute JWT. Five minutes later, TPC Admin debars the student for disciplinary violations. The student immediately attempts to submit an application.
* **Expected Behavior:** Although the JWT signature and expiration timestamp are valid, the backend verifies authoritative database state for `is_debarred` before executing the application submission. The request is rejected with HTTP `403 Forbidden`: *"Access Denied: Your account is debarred from placement activities."*
* **Reason:** JWT carries identity and role context, but mutable critical state (debarment, placement lock, recruiter verification, drive status) must be validated against the authoritative database state for sensitive/mutable operations.

---

### 18.2 Registration Edge Cases

#### EC-REG-01: Student Registration with Non-Institutional Email
* **Scenario:** A student attempts to self-register using `candidate@gmail.com` instead of the mandated `@college.edu` domain.
* **Expected Behavior:** Request rejected with HTTP `400 Bad Request`: *"Registration is restricted to official college email domains (@college.edu)."*
* **Reason:** Enforces institutional boundary control and prevents unauthorized external sign-ups.

#### EC-REG-02: Student Roll Number or University ID Collision
* **Scenario:** A student attempts to register with a Roll Number (`2022-CSE-045`) that already exists in the pre-provisioned registrar master roster.
* **Expected Behavior:** If the pre-provisioned account is unactivated, the system sends an account activation link to the student's institutional email. If already claimed, the system rejects registration with: *"An account with this Roll Number is already registered. Please use forgot password or contact TPC."*
* **Reason:** Roll numbers are immutable academic identifiers; duplicate records cause cross-student academic grade contamination.

#### EC-REG-03: Recruiter Registers with Free Webmail (Gmail/Yahoo/Hotmail)
* **Scenario:** An employer attempts to register an enterprise account using `@yahoo.com` or `@gmail.com`.
* **Expected Behavior:** Request rejected with HTTP `400 Bad Request`: *"Recruiters must register with an official corporate domain email. Free webmail providers are not permitted."*
* **Reason:** Blocks spam, unauthorized student impersonation of companies, and unverified third-party recruiters.

---

### 18.3 Profile Management Edge Cases

#### EC-PROF-01: Student Attempts Direct Self-Edit of CGPA or Active Backlogs
* **Scenario:** A student attempts to alter their verified CGPA from `6.40` to `8.50` via the profile interface or by directly calling `PUT /api/student/profile`.
* **Expected Behavior:** The payload field is stripped by the controller schema, or the API returns HTTP `403 Forbidden`: *"Academic records (CGPA, Branch, Backlogs) are read-only and can only be amended by TPC Admin via official registrar verification."*
* **Reason:** Eliminates academic fraud and preserves absolute trust in placement eligibility evaluations.

#### EC-PROF-02: Profile Edit While Applications Are Under Active Review
* **Scenario:** A student updates their mobile number or portfolio GitHub link while their application is in `INTERVIEW_SCHEDULED` stage.
* **Expected Behavior:** The live student profile updates immediately for future communication; however, the historical application record retains its immutable academic snapshot (CGPA and submitted resume version remain frozen).
* **Reason:** Contact details must stay current to prevent missed interview invitations, but historical screening criteria must remain tamper-proof.

---

### 18.4 Job & Drive Management Edge Cases

#### EC-JOB-01: Application Submitted at the Exact Second of Drive Expiry
* **Scenario:** A student submits an application at `23:59:59.950` where the drive deadline is `23:59:59.000`.
* **Expected Behavior:** The request is rejected at the database transaction layer with HTTP `400 Bad Request`: *"The application deadline for this drive has passed. Submissions are closed."*
* **Reason:** Precise server-side deadline enforcement prevents disputes regarding post-deadline submissions.

#### EC-JOB-02: Recruiter Modifies CGPA Eligibility Criteria After Students Have Applied
* **Scenario:** A company increases minimum CGPA from `7.00` to `7.50` after 50 students have already applied.
* **Expected Behavior:**
  1. If applications exist, the system requires TPC Admin approval to alter eligibility rules.
  2. Previously submitted applications are flagged with a banner: *"Grandfathered Under Initial Posting Rules"* or the recruiter must initiate a formal administrative rule change that explicitly notifies affected students.
* **Reason:** Unilateral post-facto criteria changes by companies lead to student grievances and administrative chaos.

#### EC-JOB-03: Recruiter Closes Drive Prematurely
* **Scenario:** A recruiter clicks "Close Drive" 24 hours prior to the publicized application deadline.
* **Expected Behavior:** Premature closure requires TPC Admin confirmation. If approved, the job state changes to `CLOSED`, an automated notification is sent to eligible students who have not yet applied, and the public drive card shows *"Drive closed early by company request"*.
* **Reason:** Protects students who planned submissions around publicized institutional deadlines.

#### EC-JOB-04: Student Attempts Application to Drive in PENDING_APPROVAL Status
* **Scenario:** A student guesses or directly inputs the job ID of a newly posted recruiter drive that is still in `PENDING_APPROVAL` status (awaiting TPC Admin review).
* **Expected Behavior:** The backend verifies drive status in the database. Because `status === 'PENDING_APPROVAL'` and not `ACTIVE`, the API rejects the request with HTTP `404 Not Found` or `400 Bad Request`: *"This drive is pending administrative approval and is not open for applications."*
* **Reason:** Recruiter-created jobs MUST NOT become `ACTIVE` immediately upon creation; only TPC Admin approval transitions them to `ACTIVE` and unlocks student applications.

---

### 18.5 Eligibility Engine Edge Cases

#### EC-ELIG-01: Student Meets CGPA When Rounded Up, But Fails Exact Truncation
* **Scenario:** A job requires minimum CGPA `7.50`. The student's official CGPA is `7.495`.
* **Expected Behavior:** The system evaluates strictly to 2 decimal places without rounding: `7.49 < 7.50` $\rightarrow$ **INELIGIBLE**. The diagnostic message displays: *"Ineligible: CGPA 7.49 is below the required 7.50 threshold."*
* **Reason:** University placement rules enforce strict truncation or explicit institutional rounding policies to prevent borderline qualification disputes.

#### EC-ELIG-02: Student Clears Backlog, but University Registrar Has Not Yet Re-evaluated
* **Scenario:** A student passed supplementary exams yesterday, but the TPC database still lists `1` active backlog. A job allows `0` backlogs.
* **Expected Behavior:** The engine marks the student **INELIGIBLE** based on the current verified database record. A CTA button displays: *"Request Academic Record Update from TPC Admin"*. Once TPC updates the verified count to `0`, the student becomes eligible immediately.
* **Reason:** Self-declaration of backlog clearance without registrar verification cannot override institutional eligibility locks.

#### EC-ELIG-03: Student Already Holds an Offer in the Same Placement Tier
* **Scenario:** A student with an accepted Offer of ₹10 LPA (Tier-2) attempts to apply for another Tier-2 job (₹11 LPA). College policy permits upgrades only to Tier-1 (Dream $\ge ₹15\text{ LPA}$).
* **Expected Behavior:** The system marks the student **INELIGIBLE**: *"Policy Restriction: You already hold an accepted offer in Tier-2. College policy only permits upgrades to Tier-1 (> ₹15.0 LPA)."*
* **Reason:** Prevents offer hoarding and ensures equitable distribution of job opportunities across the cohort.

---

### 18.6 Application Lifecycle Edge Cases

#### EC-APP-01: Rapid Double-Clicking the "Submit Application" Button
* **Scenario:** A nervous student clicks the "Submit Application" button 5 times within 300 milliseconds over high-latency Wi-Fi.
* **Expected Behavior:** The UI button disables instantly on first click. The database enforces a `UNIQUE(student_id, job_id)` constraint. The backend processes the first request and returns HTTP `201 Created`; subsequent concurrent requests fail cleanly or return the existing application without creating duplicate records.
* **Reason:** Guarantees database integrity and prevents duplicate application entries.

#### EC-APP-02: Student Debarred by TPC Admin While Application Is In-Flight
* **Scenario:** A student applies for a job, but is subsequently debarred for a disciplinary violation while their application is in `ATS_SHORTLISTED` stage.
* **Expected Behavior:** The student's active applications across all drives transition automatically to `REJECTED` or `DEBARRED`. The recruiter applicant table updates with a status badge *"Disqualified by TPC"*, preventing interview scheduling.
* **Reason:** Administrative disciplinary sanctions supersede ongoing drive workflows across the entire platform.

---

### 18.7 Resume Upload Edge Cases

#### EC-RES-01: Uploading a File with a `.pdf` Extension That Is Actually an Executable
* **Scenario:** An attacker renames `malware.exe` to `resume.pdf` and uploads it.
* **Expected Behavior:** The upload service inspects the file header bytes. Because the initial 4 bytes are not `%PDF-` (`0x25 0x50 0x44 0x46`), the upload is immediately rejected with HTTP `400 Bad Request`: *"Invalid file format. The file content does not match a valid PDF document."* File is deleted from temp storage immediately.
* **Reason:** File extension spoofing is a common vector for remote code execution and server compromise.

#### EC-RES-02: Uploading a 5.01 MB PDF File
* **Scenario:** A student uploads a richly formatted resume PDF of size 5,250,000 bytes (5.01 MB).
* **Expected Behavior:** Rejected at the API gateway / Multer middleware layer with HTTP `413 Payload Too Large`: *"File size exceeds the 5 MB limit. Please compress your PDF before uploading."*
* **Reason:** Protects server memory buffers and prevents denial-of-service through oversized payloads.

#### EC-RES-03: Deleting a Resume That Is Attached to an Active Application
* **Scenario:** A student attempts to delete Resume Version 1 from their profile settings, but that specific resume was submitted for an ongoing drive in `ATS_SHORTLISTED` stage.
* **Expected Behavior:** The system performs a **soft-delete** on the student's profile (it disappears from their active resume selector), but the underlying PDF file and snapshot reference remain preserved in storage for the recruiter's active drive review.
* **Reason:** Preserves recruiters' access to the exact document submitted for historical evaluation.

---

### 18.8 PDF Parsing Edge Cases

#### EC-PARSE-01: Password-Protected / Encrypted PDF Upload
* **Scenario:** A student exports an encrypted, password-protected PDF from their design software and uploads it.
* **Expected Behavior:** PDF text parser detects encryption and fails extraction. The system flags the resume status as `PARSING_ERROR` with message: *"Uploaded PDF is password-protected. Please remove password protection and re-upload."*
* **Reason:** Automated text extraction engines cannot decrypt arbitrary password-protected files.

#### EC-PARSE-02: Scanned Image-Only PDF (Zero Selectable Text)
* **Scenario:** A student scans a paper resume into a flat JPEG wrapped inside a PDF envelope (0 extractable text characters).
* **Expected Behavior:** Text extraction yields an empty string or $< 20$ characters. The system marks parsing status as `IMAGE_ONLY_DETECTED`: *"No selectable text found in resume. Please upload a PDF generated directly from a word processor (Word, Google Docs, LaTeX) to enable automated matching."* ATS match score is set to 0%.
* **Reason:** Initial V1 parser does not run OCR; candidate must be alerted immediately so they can upload a readable PDF.

#### EC-PARSE-03: Corrupt PDF with Truncated EOF Marker
* **Scenario:** Network disconnects mid-upload, leaving a truncated PDF missing the `%%EOF` marker.
* **Expected Behavior:** Parser throws a file corruption exception. The API catches the error, deletes the partial file, and returns HTTP `422 Unprocessable Entity`: *"Corrupted PDF file. Please verify the file opens locally and re-upload."*
* **Reason:** Prevents corrupted binaries from crashing downstream processing pipelines.

---

### 18.9 ATS Resume Matching Edge Cases

#### EC-ATS-01: Job Description Has Zero Required Skills Specified
* **Scenario:** A recruiter creates an exploratory walk-in drive and enters an open-ended description without configuring required skills.
* **Expected Behavior:** The ATS Engine recognizes an empty skill requirement set. It bypasses Jaccard calculation and assigns a neutral default score: `100% (No specific technical filter defined)`. The recruiter applicant list displays *"No skill filter applied"*.
* **Reason:** Division by zero in Jaccard similarity ($\frac{|R \cap \emptyset|}{|R \cup \emptyset|}$) must be guarded against mathematically.

#### EC-ATS-02: Resume Contains Massive White-Text Keyword Stuffing
* **Scenario:** A candidate embeds 300 skill keywords in 1pt white font at the bottom of their resume.
* **Expected Behavior:** V1 deterministic matcher counts token matches, but the overall score is governed by the Jaccard denominator ($|R \cup J|$). Extra irrelevant terms expand the denominator, limiting artificial score inflation. Furthermore, the recruiter viewing the resume in the portal review drawer visually spots discrepancies between stated experience and claimed token lists.
* **Reason:** Token-based Jaccard similarity inherently penalizes excessive vocabulary expansion compared to plain keyword tallying.

#### EC-ATS-03: Skill Synonyms and Aliases
* **Scenario:** Job requires `Node.js` and `PostgreSQL`. Student resume lists `NodeJS` and `Postgres`.
* **Expected Behavior:** The pre-processing pipeline resolves aliases via the Canonical Skill Taxonomy dictionary (`NodeJS` $\rightarrow$ `node.js`, `Postgres` $\rightarrow$ `postgresql`), scoring them as valid 100% matches.
* **Reason:** Ensures candidates are not penalized for common typographical conventions or abbreviation differences.

---

### 18.10 Interview Management Edge Cases

#### EC-INT-01: Scheduling an Interview During an Existing Confirmed Slot (Collision)
* **Scenario:** Recruiter A schedules Candidate X for a Technical Interview on Friday at 10:00 AM – 10:45 AM. Recruiter B attempts to schedule the same Candidate X for a Round 1 Interview on Friday at 10:30 AM.
* **Expected Behavior:** The scheduling engine detects an overlap (violates the $\pm 15\text{ minute}$ collision buffer) and rejects Recruiter B's request: *"Conflict Detected: Candidate X already has an interview scheduled with Company A between 10:00 AM and 10:45 AM. Earliest available slot is 11:00 AM."*
* **Reason:** Prevents candidate no-shows and double-booking during multi-company placement drives.

#### EC-INT-02: Interview Rescheduled with Less Than 2 Hours Notice
* **Scenario:** A recruiter changes an interview time from 3:00 PM to 1:30 PM at 12:30 PM on the same day.
* **Expected Behavior:** The system prompts the recruiter with a confirmation warning: *"Short-Notice Reschedule Warning: Rescheduling within 2 hours may lead to candidate unavailability."* Upon confirmation, the system dispatches an **URGENT** priority in-app notification and transactional SMS/Email with updated `.ics` calendar invite.
* **Reason:** Keeps the process functional while highlighting operational risks of short-notice timing shifts.

#### EC-INT-03: Recruiter Enters Invalid or Malformed Video Meeting URL
* **Scenario:** Recruiter types `meet.google` or `zoom-call-room` without a valid HTTP/HTTPS protocol prefix.
* **Expected Behavior:** Input validation schema rejects the entry: *"Please enter a fully-qualified URL (e.g., https://meet.google.com/abc-defg-hij)."*
* **Reason:** Prevents broken clickable links that strand candidates at interview time.

---

### 18.11 Offer Management Edge Cases

#### EC-OFF-01: Student Accepts an Offer While Holding Pending Offers from Other Companies
* **Scenario:** Student receives Offer 1 (₹12 LPA) and Offer 2 (₹14 LPA). Student formally accepts Offer 2 in the portal.
* **Expected Behavior:**
  1. Offer 2 state mutates to `ACCEPTED`.
  2. Student placement status locks to `PLACED`.
  3. Offer 1 is automatically transitioned to `AUTO_DECLINED` with reason *"Candidate accepted competing offer at Company 2"*.
  4. All other active applications for lower or equal tiers transition to `AUTO_WITHDRAWN`.
  5. Recruiters 1 and 2 are immediately notified.
* **Reason:** Enforces college "One Student, One Job" placement policy instantaneously and releases held vacancies to waitlisted candidates.

#### EC-OFF-02: Offer Expiration Deadline Passes Without Student Action
* **Scenario:** Recruiter sets a 48-hour acceptance window. The deadline passes with the student taking no action.
* **Expected Behavior:** At the exact expiration timestamp, the system transitions offer status from `EXTENDED` to `EXPIRED`. The candidate's decision buttons disappear. Recruiter is notified: *"Offer Expired: Candidate failed to respond within 48 hours."* Recruiter can now extend an offer to the next waitlisted candidate.
* **Reason:** Prevents recruitment drives from stalling indefinitely due to inactive or unresponsive candidates.

#### EC-OFF-03: Recruiter Attempts to Revoke an Offer Already Formally Accepted
* **Scenario:** A recruiter attempts to click "Revoke Offer" after the candidate has signed/confirmed acceptance.
* **Expected Behavior:** Direct recruiter revocation is blocked: *"Action Denied: This offer has already been formally accepted by the candidate. Revocations require official TPC Admin intervention and placement cell review."*
* **Reason:** Protects students from unilateral corporate offer cancellations without placement cell oversight.

---

### 18.12 Notification Edge Cases

#### EC-NOTIF-01: Recruiter Dispatches Bulk Shortlist to 150 Candidates Simultaneously
* **Scenario:** Recruiter clicks "Shortlist All Selected" for 150 candidates.
* **Expected Behavior:** The backend dispatches 150 individualized candidate in-app/email alerts via background job queue. The recruiter receives **1 single summary notification**: *"Batch Complete: 150 candidates shortlisted for [Role]."*
* **Reason:** Prevents the recruiter’s notification drawer from being spammed with 150 separate alerts.

#### EC-NOTIF-02: Institutional Email Server Down (SMTP Bounce/Timeout)
* **Scenario:** The university mail server experiences an outage during interview dispatch.
* **Expected Behavior:** The email dispatch service retries with exponential backoff (up to 3 times) and logs the failure in the audit log. Crucially, the **In-App Notification Center remains fully intact and visible** to the student immediately upon next portal visit.
* **Reason:** Dual-channel redundancy ensures critical recruitment milestones are accessible even during external email infrastructure failures.

---

### 18.13 TPC Admin Operations Edge Cases

#### EC-ADMIN-01: Bulk CSV Student Roster Upload Contains Malformed Rows
* **Scenario:** Admin uploads a 500-student CSV where Row 42 has an invalid email format and Row 118 has a negative CGPA (`-2.5`).
* **Expected Behavior:** The upload pipeline executes with partial-success validation:
  1. Valid rows (498 students) are staged/imported.
  2. Failed rows are skipped and returned in a downloadable error report (`import_errors.csv`) specifying: `Row 42: Invalid email format`, `Row 118: CGPA must be between 0.00 and 10.00`.
  3. No corrupted data enters the active student database.
* **Reason:** Prevents an entire batch import of hundreds of students from failing due to minor typos in a few rows.

#### EC-ADMIN-02: Admin Accidentally Debars a Student in Error
* **Scenario:** Admin selects the wrong student from a dropdown and clicks "Debar Candidate", then realizes the mistake 10 minutes later.
* **Expected Behavior:** The admin interface provides a *"Reinstate Student"* action requiring a mandatory justification note. Reinstating restores the student's eligibility status, logs both actions in the audit trail, but requires the admin to manually re-activate any specific applications that were withdrawn during the debarment window.
* **Reason:** Human errors in administrative offices must be reversible with clear audit accountability.

---

### 18.14 Data Export Edge Cases

#### EC-EXP-01: Export Query Yields Zero Records Matching Filter Criteria
* **Scenario:** Admin filters by `Batch: 2026`, `Department: Civil Engineering`, `CGPA >= 9.80` and clicks "Export to Excel". No students match.
* **Expected Behavior:** Instead of generating an empty or broken 0-byte file, the UI displays an informative alert: *"No records match your selected filter criteria. Export was not generated."*
* **Reason:** Prevents user confusion from downloading blank spreadsheet files.

#### EC-EXP-02: Candidate Stored Name Contains Excel Formula Injection (`=cmd|' /C ...'!A1`)
* **Scenario:** A malicious user sets their name or project title to `=SUM(A1:A10)` or `=cmd|' /C calc'!A0` and admin exports the roster to CSV.
* **Expected Behavior:** The export generator detects leading characters (`=`, `+`, `-`, `@`, `\t`, `\r`) and automatically prepends an apostrophe (`'`) to neutralize executable formula behavior in Microsoft Excel and Google Sheets.
* **Reason:** Neutralizes CSV Formula Injection (CWE-1236) and protects administrative workstations from spreadsheet macro exploits.

---

### 18.15 Concurrent Actions & Race Conditions

#### EC-CONC-01: Two Recruiters Reviewing Same Pool Shortlist Candidate Simultaneously
* **Scenario:** Recruiter A and Recruiter B both open the applicant roster at 10:00 AM. Recruiter A marks Candidate 1 as `ATS_SHORTLISTED`. 5 seconds later, Recruiter B marks Candidate 1 as `REJECTED`.
* **Expected Behavior:** The system uses optimistic concurrency control (via `updated_at` timestamp / version counter). Recruiter B's mutation fails or displays a toast: *"Status has already been updated by Recruiter A. Please refresh to view current status."*
* **Reason:** Eliminates state collisions when multiple team members manage a shared company recruitment drive.

#### EC-CONC-02: Student Accepts Offer at Exact Moment Company Tries to Revoke It
* **Scenario:** Student submits acceptance at `14:00:01.100`. Recruiter submits revocation at `14:00:01.150`.
* **Expected Behavior:** The database transaction locks the offer record row (`SELECT ... FOR UPDATE`). The student's acceptance commits first, transitioning the state to `ACCEPTED`. The recruiter's subsequent request fails with HTTP `409 Conflict`: *"Cannot revoke offer: Candidate has already confirmed acceptance."*
* **Reason:** Row-level database locks ensure deterministic ordering and prevent invalid post-acceptance revoking.

---

### 18.16 Duplicate Operations

#### EC-DUP-01: Duplicate Application Submission on Network Timeout
* **Scenario:** Student submits application, but response packet drops. Student immediately clicks "Submit" again.
* **Expected Behavior:** Because the database has a unique index on `(student_id, job_id)`, the second insert is rejected or handled idempotently. The API returns HTTP `200 OK` with the existing application details.
* **Reason:** Network hiccups should never create duplicate applications for the same candidate.

#### EC-DUP-02: Resending Identical Interview Invitation
* **Scenario:** A recruiter clicks "Send Invite" multiple times rapidly.
* **Expected Behavior:** The interview service checks if an active interview slot already exists for that application round. If yes, it updates the existing slot rather than creating duplicate calendar invites.
* **Reason:** Prevents candidate calendars from being cluttered with redundant meeting invitations.

---

### 18.17 Invalid Inputs & Malformed Payloads

#### EC-INP-01: Negative or Excessively High CGPA Input
* **Scenario:** A client submits `{ "cgpa": -1.5 }` or `{ "cgpa": 11.2 }`.
* **Expected Behavior:** Controller schema validation rejects payload with HTTP `400 Bad Request`: *"CGPA must be a valid number between 0.00 and 10.00."*
* **Reason:** Bounds checking guarantees eligibility engine mathematical calculations remain valid.

#### EC-INP-02: Malformed JSON Payload with Extra Unrecognized Fields
* **Scenario:** An attacker submits `{ "job_id": 12, "is_admin_override": true, "bypass_eligibility": true }`.
* **Expected Behavior:** Zod schema strips unknown properties or rejects the request with HTTP `400 Bad Request`. Unwhitelisted fields never propagate to the database.
* **Reason:** Protects against Mass Assignment vulnerabilities (CWE-915).

#### EC-INP-03: Extremely Long String Inputs (Buffer Overflow Simulation)
* **Scenario:** A user submits a cover note or job title consisting of 100,000 repeating characters.
* **Expected Behavior:** Schema validation rejects the payload immediately: *"Input string exceeds maximum allowable length of 500 characters."*
* **Reason:** Prevents database performance degradation, text rendering overflows, and memory consumption attacks.

---

## 19. Project Scope & Phasing Matrix (MVP vs. V2 vs. Future)

To ensure the system can be realistically and robustly implemented within **approximately 10 days by a single developer leveraging AI coding assistance**, the functional requirements are divided into three phased tiers:
- **MVP (Must Have):** The core end-to-end placement and resume matching loop.
- **V2 (Should Have):** Operational workflows (interviews, formal offers, exports, notifications) that complete the institutional lifecycle.
- **Future (Nice to Have):** Advanced enterprise capabilities (semantic LLMs, WebSockets, background queues, SIS SSO) that represent long-term enhancements.

---

### 19.1 Phasing Categorization Matrix

| Feature Area | MVP (Must Have)<br>*(Days 1–10)* | V2 (Should Have)<br>*(Post-MVP Polish)* | Future (Nice to Have)<br>*(Roadmap / V3)* |
| :--- | :--- | :--- | :--- |
| **Authentication & RBAC** | Email/Password, JWT in HttpOnly cookies (identity + role context), DB-authoritative validation for mutable critical state (debarment, placement lock, recruiter verification), 3 roles (`STUDENT`, `RECRUITER`, `TPC_ADMIN`), seed admin. | Password reset via email, session token revocation blacklist. | Campus LDAP / SAML 2.0 SSO, OAuth2 (Google/GitHub), 2FA/MFA. |
| **Student Profiles** | Academic profile (CGPA, Branch, Batch, Backlogs), contact info, resume selector. | Multi-resume management (up to 3 versions), academic discrepancy dispute request flow. | Automated LinkedIn/GitHub portfolio sync, verified credential badges. |
| **Recruiter Operations** | Company registration (TPC approval gate), post recruitment drives (`PENDING_APPROVAL` status), TPC Admin drive approval (`ACTIVE`), define required skills and criteria. | Multi-member hiring teams, customizable interview rounds, company logo upload. | Automated external job board syndication, candidate CRM messaging. |
| **Eligibility Engine** | Deterministic pre-application evaluation (CGPA, Branch, Batch, Backlogs). Clear separation: Eligibility Gate (`ELIGIBLE` / `INELIGIBLE`) vs. Application Lifecycle. Transparent diagnostic failure explanations. | Tier upgrade policy locks (Tier-1 Dream upgrade rules), past debarment checks. | Configurable weighted composite eligibility rules, branch equivalence mapping. |
| **Resume & File Storage** | Single active PDF upload, extension & magic-byte checks, 5 MB cap, local/Cloudinary storage. | Multiple resume versions, download preview modal, soft deletion. | Automated resume anonymization (blind recruitment mode), virus scanning integration. |
| **PDF Parsing Engine** | Synchronous PDF text extraction (`pdf-parse`), whitespace cleaning, lowercase normalization. | Scanned image detection warnings, encrypted PDF detection with user feedback. | OCR fallback for scanned resumes (Tesseract.js), structured section extraction (Education, Experience). |
| **ATS Resume Matcher** | Deterministic Jaccard similarity based on normalized skill sets ($\frac{\|\text{ResumeSkills} \cap \text{RequiredSkills}\|}{\|\text{ResumeSkills} \cup \text{RequiredSkills}\|}$), explainable matched vs. missing skills breakdown, fully reproducible without external AI/LLMs. | Weighted scoring (70% mandatory / 30% preferred skills), frequency weighting, penalty dampener for missing core skills. | Hybrid AI / LLM Semantic Embeddings, vector similarity, experience tenure weighting, soft-skills extraction. |
| **Application Lifecycle** | Post-eligibility application states (`APPLIED` $\rightarrow$ `ATS_SHORTLISTED` $\rightarrow$ `INTERVIEW_SCHEDULED` $\rightarrow$ `OFFER_EXTENDED` $\rightarrow$ `ACCEPTED`, plus `REJECTED`), atomic submission, duplicate prevention. Strictly separated from Eligibility. | Full 9-state machine transitions, self-service withdrawal workflows (`DECLINED`, `AUTO_WITHDRAWN`). | Stage-based automated feedback surveys, candidate withdrawal self-service with cooling period. |
| **Interview Management** | Basic interview status badge on recruiter dashboard. | Multi-round scheduling, date/time picker, collision buffer ($\pm 15\text{ min}$), video link fields, candidate calendar view. | In-browser WebRTC video interviewing, interviewer shared scorecard rubrics, automated calendar sync. |
| **Offer Management** | Recruiter advances selected candidate toward `OFFER_EXTENDED` / selection processing. Upon candidate acceptance: Application status $\rightarrow$ `ACCEPTED`, and student `placement_status` $\rightarrow$ `PLACED`. | Formal offer creation (CTC, role, deadline), TPC approval gate, student 2-step accept/decline modal, cascading auto-withdrawals. | Digital signature capture (DocuSign/HelloSign API), automated formal offer letter PDF generator. |
| **Notifications** | On-screen success/error toasts, state badges on application cards. | In-App Notification Center (drawer, unread counter badge), transactional email dispatch (SMTP/Nodemailer). | Real-time WebSockets / Server-Sent Events (SSE), SMS/WhatsApp dispatch integration. |
| **Admin Analytics** | 4 primary KPI cards (Total Students, Active Drives, Total Applications, Placed Count), basic student/recruiter lists. | Comprehensive analytics dashboard (branch-wise placement matrix, funnel conversion chart, job yield table). | Predictive placement forecast modeling, historical multi-year trend analytics, cohort comparison charts. |
| **Data Exports** | Basic student table CSV export. | Multi-filtered CSV and Excel (.xlsx) export with formula injection sanitization (CWE-1236) and audit logging. | Scheduled automated email reports, custom drag-and-drop export column builder. |
| **System Architecture** | Monolithic Node.js/Express + Relational DB (PostgreSQL/MySQL with Prisma), synchronous processing. | Database connection pooling, optimistic concurrency locks, comprehensive structured JSON logging. | Distributed background worker queues (BullMQ + Redis), multi-region active-active read replicas. |

---

### 19.2 Detailed Justification by Scope Tier

#### A. MVP — MUST HAVE (Target: ~10 Days, 1 Developer + AI Assistant)
The MVP scope encompasses the minimal end-to-end transactional loop required to substantiate the project’s identity: a functional campus placement portal with an automated resume-job matching engine.

1. **Authentication & RBAC (JWT Identity + DB Authoritative State):**
   - *Why in MVP:* Fundamental prerequisite. JWT carries identity and role context, while the database authoritatively enforces critical mutable state (debarment, placement status, recruiter verification) to prevent unauthorized student submissions or recruiter actions.
2. **Student & Recruiter Management with Job Approval Lifecycle:**
   - *Why in MVP:* Defines the primary personas. Recruiters submit drives with initial status `PENDING_APPROVAL`; TPC Admin reviews and approves to `ACTIVE` (or rejects). Students are strictly prevented from viewing or applying to unapproved drives.
3. **Core Eligibility Engine (Pre-Application Gate):**
   - *Why in MVP:* This is the core domain differentiator. Evaluates whether a student is `ELIGIBLE` or `INELIGIBLE` based on CGPA, Branch, Batch, and Backlogs *prior* to application submission. Eligibility is strictly separate from the subsequent application lifecycle.
4. **Resume Upload & Validation (PDF Only, 5 MB):**
   - *Why in MVP:* Required input artifact for the ATS engine. Magic byte verification ensures security without third-party enterprise malware scanners.
5. **Deterministic PDF Text Extraction:**
   - *Why in MVP:* Direct functional dependency for the ATS matcher. Must reliably extract clean text tokens from standard PDFs using lightweight libraries (`pdf-parse`).
6. **Deterministic ATS Matcher (Pure Jaccard Similarity):**
   - *Why in MVP:* The primary technical showcase of the application. Calculates an objective, explainable, and reproducible match percentage using normalized skill sets: $\frac{|\text{ResumeSkills} \cap \text{RequiredSkills}|}{|\text{ResumeSkills} \cup \text{RequiredSkills}|}$, identifies matched vs. missing skills, and runs completely independent of external AI/LLMs.
7. **Application Submission & Progression:**
   - *Why in MVP:* Closes the primary business loop: Eligible student applies (`APPLIED`) $\rightarrow$ ATS score computed $\rightarrow$ Recruiter shortlists (`ATS_SHORTLISTED`) or rejects (`REJECTED`).
8. **Basic TPC Admin Overview & Approvals:**
   - *Why in MVP:* Proves administrative governance: TPC Admin approves recruiters, reviews pending jobs (`PENDING_APPROVAL` $\rightarrow$ `ACTIVE`), and monitors core placement metrics (total students, drives, applications).

---

#### B. V2 — SHOULD HAVE (Post-MVP Operational Polish)
Features that elevate the platform to a complete, production-ready operational management suite, scheduled immediately after the 10-day MVP milestone.

1. **Interview Management & Scheduling:**
   - *Why in V2:* While vital for full recruitment coordination, interviews can be coordinated externally or via direct communication during an initial 10-day MVP. Implementing collision detection and calendar generation adds UI and logic complexity that distracts from the core ATS loop.
2. **Formal Offer Management & Cascading Auto-Withdrawals:**
   - *Why in V2:* Automating "One Student, One Job" policy cascades across competing applications is a high-value institutional feature, but involves intricate multi-table ACID transactions and modal gates that require careful testing to avoid edge-case state corruption.
3. **In-App Notification Center & Transactional Emails:**
   - *Why in V2:* Notification centers and SMTP mail servers introduce asynchronous queuing, template formatting, bounce handling, and email provider configurations that slow down initial rapid prototyping. On-screen toasts and real-time dashboard status badges suffice for MVP.
4. **Advanced Data Exports (Excel / CSV with CWE-1236 Sanitization):**
   - *Why in V2:* Basic CSV exports satisfy initial data auditing; full multi-column Excel template builders and formula sanitization are institutional convenience tools best implemented in V2.
5. **Multi-Resume Management (3 Versions):**
   - *Why in V2:* Supporting multiple resume versions per student complicates storage tracking and application snapshot associations. A single active resume per candidate is completely sufficient for MVP validation.

---

#### C. FUTURE — NICE TO HAVE (Advanced Enterprise & Research Roadmap)
Advanced features reserved for future major iterations or research extensions.

1. **AI / LLM Semantic Resume Matching:**
   - *Why in Future:* Semantic embeddings (OpenAI, HuggingFace) add third-party API costs, network latency, non-deterministic scores, and token rate limit bottlenecks. A deterministic, explainable Jaccard algorithm is faster, transparent, free to run, and pedagogically sound for V1.
2. **In-Browser WebRTC Video Conferencing:**
   - *Why in Future:* Building native video calling involves massive engineering overhead (STUN/TURN servers, media streaming, bandwidth costs). Real-world university recruitment universally relies on external Google Meet, Zoom, or Microsoft Teams links.
3. **Real-Time WebSockets (Socket.io):**
   - *Why in Future:* Stateful socket connections complicate horizontal scaling and serverless deployment. Client-side polling or route-transition fetching is far more robust for typical placement portal loads.
4. **Distributed Background Worker Queues (BullMQ / Redis):**
   - *Why in Future:* For 1,500–3,000 students, PDF extraction and Jaccard scoring take $<2.5\text{ seconds}$ synchronously. Running dedicated Redis worker instances introduces infrastructure bloat that is unnecessary for a fresher portfolio project.
5. **University ERP / SIS Single Sign-On (SSO):**
   - *Why in Future:* Requires access to internal institutional directory servers (Active Directory / Banner) that cannot be simulated or deployed on public developer platforms.

---

## 20. Explicit Out-of-Scope Boundaries

To maintain engineering focus, prevent scope creep, and ensure the delivery of a high-quality, production-ready system within the project timeline, the following domains and features are **explicitly declared out of scope** for the initial platform release. While these represent logical post-hiring workflows or enterprise integrations, they do not belong in the core campus placement portal.

---

### 20.1 Out-of-Scope Feature Catalog

| Excluded Domain / Feature | Scope Boundary & Rationale | Future Extension Horizon |
| :--- | :--- | :--- |
| **1. Payroll & Compensation Disbursement** | The portal’s responsibility terminates when an offer is formally accepted. Calculating tax deductions, provident funds, bonuses, direct bank deposits, or issuing salary slips belongs to enterprise accounting software (e.g., SAP, QuickBooks), not a campus placement system. | Future integration via webhooks to third-party payroll APIs once candidates transition to active employee rosters. |
| **2. Post-Hiring Employee Management** | Workflows involving post-joining employee onboarding, probationary performance reviews, department transfers, team allocation, and employee offboarding are organizational HR responsibilities outside academic placements. | Future potential integration with enterprise Human Resource Information Systems (HRIS) such as Workday or BambooHR. |
| **3. Attendance & Time Tracking After Hiring** | Logging employee daily work hours, biometric punch-in sync, shift scheduling, and leave approvals post-employment are operational workplace management tasks completely unrelated to placement drives. | Out of scope indefinitely; candidate responsibility transfers to employer's internal tools upon joining. |
| **4. Full HR Management System (HRMS)** | Comprehensive HRMS functionality (benefits administration, corporate training programs, disciplinary hearings at the workplace, employee satisfaction surveys) introduces massive domain bloat that distracts from the core placement matching loop. | The portal will remain a specialized **Campus Placement & ATS Engine**, interfacing with HRMS platforms only via standard candidate export files. |
| **5. Financial Transactions & Payment Processing** | The portal handles zero monetary transactions. There are no recruiter drive registration fees, student placement training charges, deposit fees, or banking gateways (e.g., Stripe, Razorpay). All recruitment activities are free or handled via university institutional budgets. | Optional future extension: A paid tier for external off-campus recruiters or sponsored company branding banners. |
| **6. Video Conferencing Infrastructure (WebRTC)** | Building in-browser peer-to-peer video streaming, recording servers, bandwidth adaptation, and media relay nodes (SFU/MCU) requires immense infrastructure and maintenance overhead. The portal deliberately delegates video calls to battle-tested tools (Google Meet, Zoom, MS Teams). | Possible future embed of pre-configured video meeting rooms via daily.co or Jitsi iframe APIs. |
| **7. Native Mobile Applications (iOS / Android)** | Developing native Swift/Kotlin or cross-platform React Native/Flutter apps introduces multi-platform build pipelines, app store review delays, and dual-codebase maintenance during rapid development. The responsive web application fully serves mobile viewports ($\ge 375\text{px}$). | Progressive Web App (PWA) with push notifications for V2; native wrappers only if specialized device hardware access is needed. |
| **8. Complex Enterprise Single Sign-On (SSO)** | Supporting enterprise federated identity standards (SAML 2.0, ADFS, WS-Federation, or Shibboleth/InCommon) requires physical access to college network active directories and enterprise identity providers that cannot be simulated locally. | Standardized OAuth2/OpenID Connect (Google/Microsoft 365) login for campus Google Workspace accounts in V2. |
| **9. Multi-Tenant University SaaS Architecture** | Multi-university tenant partitioning (isolated schemas, custom domain routing, tenant-level billing) adds substantial architectural complexity. The platform is intentionally architected as a **dedicated single-institution placement portal**. | Multi-tenant SaaS partitioning can be introduced in V3 if commercialized across multiple universities. |
| **10. Fully Autonomous AI Hiring Decisions** | The ATS engine is an **objective decision-support tool**, not an autonomous decision maker. The system computes keyword Jaccard scores, but **never automatically issues binding rejection or offer decisions** without human recruiter or TPC Admin review. | Preserves ethical AI boundaries, prevents algorithmic bias lawsuits, and complies with emerging global regulations regarding automated employment decision tools (AEDTs). |

---

### 20.2 Architectural Demarcation Principle

The platform draws a strict conceptual boundary at the **Point of Offer Acceptance**:

```
[ Academic Preparation ] ──► [ Campus Recruitment Portal ] ──► [ Enterprise HR & Payroll ]
  • Coursework                  • Job Posting                   • Employee ID Creation
  • Verified CGPA               • Eligibility Engine            • Tax / Banking Setup
  • Resume Building             • ATS Skill Matching            • Biometric Attendance
                                • Interview Scheduling          • Monthly Salary Slips
                                • Binding Offer Acceptance      • Benefits Administration
                                      │
                                      ▼
                           [ BOUNDARY TERMINATION ]
```

*By terminating system responsibility at the point where a student accepts an offer and is marked `PLACED`, the placement portal retains a razor-sharp focus on solving the core university coordination bottlenecks without straying into corporate operations.*

---

## 21. MVP Acceptance Criteria (Given-When-Then Specification)

This section provides testable, unambiguous acceptance test suites formatted in standard BDD (*Given-When-Then*) syntax for every MVP module. These criteria serve as the formal Definition of Done (DoD) for implementation verification and automated QA testing.

---

### 21.1 Authentication

#### AC-AUTH-01: Successful Student Login
* **Feature:** Student Authentication
* **Requirement:** Authenticate verified students via email and password, issuing an `HttpOnly` JWT cookie.
* **Given:** A registered student with institutional email `alex@college.edu` and password `SecurePass123!`.
* **When:** The student submits valid credentials to `POST /api/auth/login`.
* **Then:** The server returns HTTP `200 OK`, sets an `HttpOnly`, `Secure`, `SameSite=Lax` cookie containing the JWT with role `STUDENT`, and responds with the student user profile payload.

#### AC-AUTH-02: Invalid Credentials Rejection
* **Feature:** Authentication Security
* **Requirement:** Prevent unauthorized login attempts without revealing user existence.
* **Given:** A registered user `alex@college.edu`.
* **When:** The user attempts to log in with an incorrect password `WrongPassword99!`.
* **Then:** The server returns HTTP `401 Unauthorized` with generic error message: *"Invalid email or password"*, sets no session cookies, and logs the failed attempt.

#### AC-AUTH-03: Unapproved Recruiter Account Login Gate
* **Feature:** Recruiter Verification Gate
* **Requirement:** Restrict recruiter access until explicit administrative approval.
* **Given:** A recruiter `recruiter@techcorp.com` whose account status is `PENDING_APPROVAL`.
* **When:** The recruiter submits valid login credentials.
* **Then:** The server returns HTTP `403 Forbidden` with message: *"Account pending verification by TPC Admin"*, and issues no JWT session token.

#### AC-AUTH-04: Authoritative Database State Overrides Stale JWT
* **Feature:** DB-Authoritative Security Enforcement
* **Requirement:** Reject sensitive actions when the database records debarment or invalid state, even if the client presents an unexpired JWT.
* **Given:** A student who holds a valid, unexpired JWT session token, but whose database record was updated to `is_debarred: true` by TPC Admin.
* **When:** The student sends `POST /api/student/applications` using their unexpired JWT.
* **Then:** The backend checks the database authoritative state for `is_debarred`, detects the debarment, immediately rejects the request with HTTP `403 Forbidden`: *"Access Denied: Your account is debarred from placement activities"*, and blocks application creation.

---

### 21.2 Role-Based Access Control (RBAC)

#### AC-RBAC-01: Unauthorized Route Protection (Student Accessing Admin Endpoint)
* **Feature:** Route Authorization Guard
* **Requirement:** Block non-admin personas from administrative controller endpoints.
* **Given:** An authenticated user with role `STUDENT`.
* **When:** The student makes a request to `GET /api/admin/analytics`.
* **Then:** The RBAC middleware intercepts the request and immediately returns HTTP `403 Forbidden` with `{ "error": "Access Denied: Insufficient permissions" }`.

#### AC-RBAC-02: Insecure Direct Object Reference (IDOR) Protection
* **Feature:** Resource Ownership Defense
* **Requirement:** Prevent students from viewing or mutating other students' application records.
* **Given:** Student A (ID: `usr_stu_01`) and an Application belonging to Student B (ID: `app_99`, `student_id: usr_stu_02`).
* **When:** Student A sends `GET /api/student/applications/app_99`.
* **Then:** The controller verifies resource ownership, detects a mismatch (`app.student_id !== req.user.id`), and returns HTTP `404 Not Found` (neutralizing ID enumeration).

---

### 21.3 Student Profile Management

#### AC-PROF-01: View Verified Academic Profile
* **Feature:** Academic Record Display
* **Requirement:** Display verified academic credentials and application eligibility baseline.
* **Given:** An authenticated student with verified CGPA `8.45`, Branch `Computer Science & Engineering`, Batch `2026`, and `0` active backlogs.
* **When:** The student navigates to `/student/profile` or calls `GET /api/student/profile`.
* **Then:** The interface displays the verified CGPA, branch, batch, and backlog status with a green "Verified by Registrar" badge, alongside an uploaded resume selector.

#### AC-PROF-02: Immutability of Verified Academic Fields
* **Feature:** Academic Integrity Guard
* **Requirement:** Prevent client-side modification of official academic marks.
* **Given:** An authenticated student on their profile page.
* **When:** The student sends `PUT /api/student/profile` with `{ "cgpa": 9.99, "active_backlogs": 0 }`.
* **Then:** The server validation schema strips the academic fields or returns HTTP `403 Forbidden`; the database record remains unmodified at `8.45`.

---

### 21.4 Recruiter Profile & Company Verification

#### AC-REC-01: Recruiter Registration with Corporate Email
* **Feature:** Recruiter Onboarding
* **Requirement:** Register corporate recruiters with mandatory company details.
* **Given:** An unregistered recruiter with work email `hr@innovate.io`.
* **When:** The recruiter submits registration with Company Name *"Innovate Inc"*, Website *"https://innovate.io"*, and corporate email.
* **Then:** The server creates a user record with role `RECRUITER`, sets verification status to `PENDING_APPROVAL`, triggers an admin alert, and returns HTTP `201 Created`.

#### AC-REC-02: Rejection of Free Webmail Domains for Recruiters
* **Feature:** Recruiter Domain Validation
* **Requirement:** Enforce corporate domain restrictions on employer accounts.
* **Given:** A visitor attempting to register as a recruiter.
* **When:** The visitor submits the registration form with email `recruiter_talent@gmail.com`.
* **Then:** The validation schema rejects the submission with HTTP `400 Bad Request`: *"Recruiters must register with an official corporate domain email."*

---

### 21.5 Job Creation & Opportunity Definition

#### AC-JOB-01: Recruiter Submits Campus Recruitment Drive (Pending TPC Approval)
* **Feature:** Drive Creation & Pending Status
* **Requirement:** Allow verified recruiters to define job requirements, eligibility rules, and mandatory skill keywords, setting initial status to `PENDING_APPROVAL`.
* **Given:** An authenticated and approved recruiter for *"TechCorp"*.
* **When:** The recruiter posts a drive with Title *"Software Engineer"*, Minimum CGPA `7.50`, Allowed Branches `["CSE", "IT"]`, Batch `2026`, Max Backlogs `0`, and Skills `["React", "Node.js", "PostgreSQL"]`.
* **Then:** The server validates all fields, sets drive status to `PENDING_APPROVAL` (recruiter-created jobs MUST NOT become `ACTIVE` immediately), persists the job record, alerts TPC Admin, and returns HTTP `201 Created`.

#### AC-JOB-02: Rejection of Job Creation with Past Deadline
* **Feature:** Job Deadline Validation
* **Requirement:** Enforce future application deadline timestamps.
* **Given:** An approved recruiter creating a new job posting.
* **When:** The recruiter submits an application deadline set to yesterday's date.
* **Then:** The server rejects the request with HTTP `400 Bad Request`: *"Application deadline must be a future timestamp."*

#### AC-JOB-03: TPC Admin Reviews and Approves Drive (Status Transitions to ACTIVE)
* **Feature:** Drive Approval by TPC Admin
* **Requirement:** Transition job status from `PENDING_APPROVAL` to `ACTIVE` upon explicit administrative review.
* **Given:** A job posting with status `PENDING_APPROVAL`.
* **When:** An authorized TPC Admin reviews the posting and executes approval (`PATCH /api/admin/jobs/:id/status` with `{ "status": "ACTIVE" }`).
* **Then:** The server updates the job status to `ACTIVE`, logs the approval action in the audit trail, and makes the drive visible and open for eligible student applications.

#### AC-JOB-04: Student Blocked from Applying to Drive in PENDING_APPROVAL Status
* **Feature:** Drive Application Gating
* **Requirement:** Prevent students from applying to jobs that are still `PENDING_APPROVAL`.
* **Given:** A job posting with status `PENDING_APPROVAL`.
* **When:** A student attempts to submit an application to the job (`POST /api/student/applications` with `{ "job_id": ... }`).
* **Then:** The server checks the authoritative database state of the job, detects `status === 'PENDING_APPROVAL'`, and rejects the request with HTTP `400 Bad Request` or `404 Not Found`: *"Drive is pending administrative approval and is not open for applications."*

---

### 21.6 Eligibility Evaluation Engine

#### AC-ELIG-01: Fully Qualified Student Evaluated as Eligible
* **Feature:** Eligibility Calculation
* **Requirement:** Evaluate student criteria against job requirements and grant application authorization.
* **Given:** A student with CGPA `8.20`, Branch `CSE`, Batch `2026`, and `0` backlogs; and an active job requiring Min CGPA `7.50`, Branch `["CSE", "IT"]`, Batch `2026`, and Max Backlogs `0`.
* **When:** The student views the job details page at `/student/jobs/:id`.
* **Then:** The Eligibility Engine executes all rules, returns status `ELIGIBLE`, displays a prominent green *"Eligible to Apply"* badge, and activates the *"Submit Application"* CTA button.

#### AC-ELIG-02: Ineligible Student Blocked with Transparent Diagnostic Explanation
* **Feature:** Eligibility Failure Diagnostics
* **Requirement:** Block unqualified student submissions and clearly explain unmet criteria.
* **Given:** A student with CGPA `6.90`, Branch `Mechanical Engineering`, Batch `2026`, and `1` active backlog; and a job requiring Min CGPA `7.50`, Allowed Branches `["CSE", "IT"]`, Max Backlogs `0`.
* **When:** The student views the job details page.
* **Then:** The system marks the student `INELIGIBLE`, disables the *"Apply"* button, and displays an itemized diagnostic card:
  - ❌ *"CGPA 6.90 is below minimum threshold 7.50"*
  - ❌ *"Branch Mechanical Engineering is not in allowed branches (CSE, IT)"*
  - ❌ *"1 active backlog exceeds maximum limit of 0"*

#### AC-ELIG-03: Server-Side Rejection of Ineligible Application Attempt
* **Feature:** Server-Side Eligibility Enforcement
* **Requirement:** Defend against malicious client-side script bypasses.
* **Given:** An authenticated student who does not meet the CGPA cutoff for Job `job_42`.
* **When:** The student attempts to bypass the disabled UI button by directly executing `POST /api/student/applications` with `{ "job_id": "job_42" }`.
* **Then:** The backend Eligibility Service executes the verification rules independently, detects the CGPA deficit, and rejects the request with HTTP `403 Forbidden`: *"Ineligible: Academic criteria not satisfied."*

---

### 21.7 Application Submission & Snapshotting

#### AC-APP-01: Successful Application with Academic Data Freeze
* **Feature:** Application Creation
* **Requirement:** Record application and snapshot academic data and resume reference immutably.
* **Given:** An eligible student with verified CGPA `8.10` and an active uploaded resume `resume_v1.pdf`.
* **When:** The student clicks *"Submit Application"* for an eligible active drive.
* **Then:**
  1. The server atomically inserts an application record with stage `APPLIED`.
  2. The application record snapshots `cgpa: 8.10`, `branch: "CSE"`, and `resume_id: "resume_v1"`.
  3. The ATS Matcher executes in the background and populates the match score.
  4. The client receives HTTP `201 Created` with confirmation toast: *"Application submitted successfully"*.

#### AC-APP-02: Prevention of Duplicate Applications
* **Feature:** Application Idempotency
* **Requirement:** Prevent duplicate submissions for the same candidate and job.
* **Given:** A student who has already applied to Job `job_10`.
* **When:** The student attempts to apply again to `job_10`.
* **Then:** The server detects the existing record via `UNIQUE(student_id, job_id)` constraint, rejects the insert, and returns HTTP `409 Conflict`: *"You have already applied for this position."*

---

### 21.8 Resume Upload & Storage

#### AC-RES-01: Valid PDF Upload & Metadata Storage
* **Feature:** Resume Ingestion
* **Requirement:** Validate and store valid `.pdf` resume documents up to 5 MB.
* **Given:** An authenticated student with an authentic 1.5 MB PDF file starting with `%PDF-` bytes.
* **When:** The student uploads the file to `POST /api/student/resumes`.
* **Then:** The server validates the MIME type, file extension, and magic bytes, saves the document to private storage, creates a database record with status `UPLOADED`, and returns HTTP `201 Created`.

#### AC-RES-02: Rejection of Non-PDF File (Magic Byte Mismatch)
* **Feature:** File Security Validation
* **Requirement:** Block non-PDF files masquerading with `.pdf` file extensions.
* **Given:** An executable binary renamed to `malicious_script.pdf`.
* **When:** The student attempts to upload the file.
* **Then:** The server inspects the initial 4 bytes, detects absence of `%PDF-` (`0x25 0x50 0x44 0x46`), rejects the file with HTTP `400 Bad Request`: *"Invalid file format. File is not a valid PDF."*, and purges temporary disk buffers.

#### AC-RES-03: Rejection of Oversized File (> 5 MB)
* **Feature:** File Size Cap
* **Requirement:** Enforce a strict 5 MB file size boundary.
* **Given:** A valid PDF file measuring 5.8 MB in size.
* **When:** The student uploads the file.
* **Then:** The API gateway rejects the upload with HTTP `413 Payload Too Large`: *"File size exceeds 5 MB limit."*

---

### 21.9 PDF Text Extraction & Cleaning

#### AC-PARSE-01: Extraction of Clean Lowercase Text from Digital PDF
* **Feature:** Text Extraction Pipeline
* **Requirement:** Extract readable text from digital PDF and normalize whitespace and casing.
* **Given:** A valid uploaded digital resume PDF containing text: `"Proficient in Python, React, and SQL database optimization."`.
* **When:** The PDF parsing service processes the document buffer.
* **Then:** The service extracts raw ASCII/UTF-8 text, strips control characters, collapses whitespace, normalizes to lowercase, and yields: `"proficient in python react and sql database optimization"`.

#### AC-PARSE-02: Handling Scanned / Image-Only PDF (Zero Text)
* **Feature:** Parser Error Handling
* **Requirement:** Detect image-only PDFs and notify candidate of unreadable content.
* **Given:** A scanned document wrapped in a PDF containing 0 extractable characters.
* **When:** The PDF parser processes the file.
* **Then:** The parser detects character count $< 20$, flags the resume parsing status as `IMAGE_ONLY_DETECTED`, sets the ATS match score to 0%, and displays a warning to the candidate: *"No selectable text found. Please upload a PDF generated from Word, Google Docs, or LaTeX."*

---

### 21.10 ATS Resume Matcher (Jaccard Similarity)

#### AC-ATS-01: Deterministic Match Score Calculation
* **Feature:** ATS Similarity Scorer
* **Requirement:** Compute canonical skill intersection and Jaccard similarity percentage.
* **Given:**
  - Job required skills: `["react", "node.js", "postgresql", "docker"]` ($|J| = 4$).
  - Candidate resume skills identified: `["react", "node.js", "python", "git"]` ($|R| = 4$).
  - Intersection ($R \cap J$): `["react", "node.js"]` (Count = 2).
  - Union ($R \cup J$): `["react", "node.js", "postgresql", "docker", "python", "git"]` (Count = 6).
* **When:** The ATS engine executes Jaccard formula $J = \frac{|R \cap J|}{|R \cup J|} = \frac{2}{6} \approx 33.33\%$.
* **Then:** The engine records:
  - Match Percentage: `33.33%`
  - Matched Skills: `["react", "node.js"]`
  - Missing Skills: `["postgresql", "docker"]`
  - Total Required Skills: 4
  - Matching Skills Count: 2

#### AC-ATS-02: Synonym & Alias Canonical Normalization
* **Feature:** Skill Taxonomy Resolution
* **Requirement:** Resolve common skill aliases before set comparison.
* **Given:** A job requiring `"PostgreSQL"` and `"Node.js"`; and a resume stating `"Postgres"` and `"NodeJS"`.
* **When:** The ATS tokenization pipeline runs against the Canonical Skill Taxonomy dictionary.
* **Then:** The tokens are normalized to canonical keys `postgresql` and `node.js`, producing a **100%** skill match for those requirements.

---

### 21.11 Recruiter Application Management & Status Transitions

#### AC-STAT-01: Recruiter Advances Candidate to ATS Shortlisted
* **Feature:** Stage Mutation
* **Requirement:** Allow recruiters to shortlist candidates from the applicant review table.
* **Given:** An application in stage `APPLIED` with an ATS match score of `82.5%`.
* **When:** The recruiter clicks *"Shortlist Candidate"* (`PATCH /api/recruiter/applications/:id/stage` with `{ "stage": "ATS_SHORTLISTED" }`).
* **Then:** The application stage mutates to `ATS_SHORTLISTED`, the recruiter applicant list updates in real time, and the candidate's dashboard reflects the shortlisted status.

#### AC-STAT-02: Rejection of Invalid State Jump
* **Feature:** State Machine Integrity
* **Requirement:** Prevent illegal lifecycle transitions that bypass mandatory recruitment workflow stages.
* **Given:** An application in stage `APPLIED`.
* **When:** A recruiter attempts to jump the application stage directly from `APPLIED` to `ACCEPTED` via API without traversing required intermediate stages (`ATS_SHORTLISTED`, `INTERVIEW_SCHEDULED`, `OFFER_EXTENDED`).
* **Then:** The lifecycle state machine validates allowed transitions, rejects the jump, and returns HTTP `422 Unprocessable Entity`: *"Invalid state transition: Application cannot transition directly from APPLIED to ACCEPTED; mandatory intermediate stages are required."*
*(Note: Application progression strictly follows: `APPLIED` $\rightarrow$ `ATS_SHORTLISTED` $\rightarrow$ `INTERVIEW_SCHEDULED` $\rightarrow$ `OFFER_EXTENDED` $\rightarrow$ `ACCEPTED`. Student placement status strictly follows: `ACCEPTED` offer $\rightarrow$ student `placement_status = PLACED`. `PLACED` remains an institutional student/account placement state, not an application lifecycle stage).*

---

### 21.12 TPC Admin Dashboard Overview

#### AC-DASH-01: Accurate Real-Time Placement Metric Tallies
* **Feature:** Administrative Overview
* **Requirement:** Aggregate core recruitment figures across active batches.
* **Given:** A database state containing:
  - 1,200 registered students
  - 25 published jobs
  - 3,450 total applications
  - 180 placed candidates
* **When:** TPC Admin loads `/admin/dashboard`.
* **Then:** The server executes fast aggregate queries and renders 4 primary KPI cards:
  - Total Students: `1,200`
  - Active Drives: `25`
  - Total Applications: `3,450`
  - Students Placed: `180` (Placement Rate: `15.0%`)

#### AC-DASH-02: Recruiter Account Verification Toggle
* **Feature:** Recruiter Approval Flow
* **Requirement:** Allow TPC Admins to inspect and approve pending recruiter registrations.
* **Given:** A recruiter account with status `PENDING_APPROVAL`.
* **When:** TPC Admin clicks *"Approve Recruiter"* on the admin companies panel.
* **Then:** The server mutates the recruiter status to `ACTIVE`, logs the action in the audit trail, and enables recruiter login immediately.

---

### 21.13 Candidate Data CSV Export

#### AC-EXP-01: Filtered Candidate CSV Generation
* **Feature:** Data Export
* **Requirement:** Generate a clean, UTF-8 encoded CSV roster of candidates matching criteria.
* **Given:** 50 candidates matching filters: `Batch: 2026`, `Department: CSE`, `Min CGPA: 8.00`.
* **When:** TPC Admin clicks *"Export CSV"* (`GET /api/admin/exports/candidates?batch=2026&dept=CSE&min_cgpa=8.0`).
* **Then:** The server streams a valid CSV file with `Content-Type: text/csv; charset=utf-8`, `Content-Disposition: attachment; filename="candidates_2026_cse.csv"`, containing standard headers (`Roll Number`, `Full Name`, `Email`, `Department`, `CGPA`, `Status`) and 50 data rows.

#### AC-EXP-02: Neutralization of CSV Formula Injection (CWE-1236)
* **Feature:** Export Security
* **Requirement:** Prepend single quote to prevent executable formula execution in spreadsheets.
* **Given:** A candidate whose name or project title is registered as `=cmd|' /C calc'!A0`.
* **When:** TPC Admin exports the candidate roster to CSV.
* **Then:** The export generator inspects the leading `= ` character and prepends a single quote, writing `"'=cmd|' /C calc'!A0"` into the CSV cell, preventing formula execution upon opening in Excel or Google Sheets.

---

## 22. Definition of Done (DoD) & Engineering Quality Checklist

A feature is **NOT complete merely because the code executes without syntax errors**. In this project, a user story or module is marked as "Done" and ready for integration only when it satisfies all criteria across the following ten engineering dimensions:

---

### 22.1 Engineering Quality Checklist

```
[✓] 1. Functional Implementation
[✓] 2. Input & Schema Validation
[✓] 3. Graceful Error Handling
[✓] 4. RBAC & IDOR Authorization
[✓] 5. Automated Testing
[✓] 6. UI Integration & Responsive UX
[✓] 7. Code Readability & Documentation
[✓] 8. Security Considerations
[✓] 9. Git Hygiene & Semantic Commits
[✓] 10. Manual Verification & Walkthrough
```

---

### 22.2 Itemized Verification Criteria

#### 1. Functional Implementation
- [ ] Core business logic satisfies 100% of the Acceptance Criteria (*Given-When-Then*) defined in Section 21.
- [ ] State transitions conform strictly to the codified State Machine Matrix without illegal bypasses.
- [ ] Database mutations execute atomically within ACID transactions wherever multi-table updates occur (e.g., application submissions, placement locks).
- [ ] Relational integrity is preserved; no orphaned database records created.

#### 2. Input & Schema Validation
- [ ] Inbound request payloads, query parameters, and route params are strictly validated using schema validators (e.g., Zod or Joi) at the controller boundary.
- [ ] Unknown, unwhitelisted payload fields are stripped automatically (defense against Mass Assignment / CWE-915).
- [ ] Data types, lengths, string formats (emails, URLs), and numerical bounds (e.g., CGPA $0.00 \le x \le 10.00$) are strictly enforced.

#### 3. Error Handling & Edge Cases
- [ ] Code handles all relevant edge cases identified in the Section 18 Exception Catalog.
- [ ] Unhandled promise rejections and asynchronous exceptions are caught via centralized Express error-handling middleware.
- [ ] Standardized, predictable JSON error envelopes returned across all endpoints (`{ success: false, error: { code, message } }`).
- [ ] Internal database errors, stack traces, and SQL queries are masked in production mode (`NODE_ENV === 'production'`).
- [ ] Appropriate HTTP status codes are returned (e.g., `400` validation, `401` unauthenticated, `403` forbidden, `404` not found, `409` conflict, `413` payload too large, `422` unprocessable state).

#### 4. Authorization & Ownership (RBAC & IDOR)
- [ ] Route-level authorization middleware verifies the user's role (`STUDENT`, `RECRUITER`, `TPC_ADMIN`) before controller execution.
- [ ] Authorization architecture enforces JWT = identity/role context, while backend checks authoritative database state for mutable critical state (debarred status, placement lock, recruiter verification, drive `PENDING_APPROVAL` status) rather than trusting stale JWT claims.
- [ ] Resource ownership is verified at the data-access layer (e.g., Student A cannot access Student B’s application, Recruiter A cannot alter Recruiter B’s drive).
- [ ] Unauthorized entity access attempts return non-revealing `404 Not Found` (or `403 Forbidden`) to prevent resource ID enumeration.

#### 5. Automated Testing
- [ ] Automated unit tests cover critical business logic:
  - Eligibility engine criteria evaluation.
  - ATS Jaccard similarity mathematical calculations.
  - State machine transition table validation.
- [ ] API integration tests verify both positive paths (HTTP 200/201) and boundary/negative failure paths (HTTP 400/401/403/404/409).
- [ ] Test suite executes locally and passes with zero regressions (`npm test`).

#### 6. UI Integration & User Experience
- [ ] Frontend cleanly consumes backend APIs with loading skeletons, spinners, and disabled button states during network requests.
- [ ] Mutating actions provide clear, immediate visual feedback via non-intrusive toast notifications.
- [ ] Diagnostic explanations are displayed to students when ineligible or rejected.
- [ ] Form inputs display inline, human-readable validation error messages.
- [ ] Layout is fully responsive across both desktop ($\ge 1280\text{px}$) and mobile screens ($\ge 375\text{px}$).
- [ ] Color contrast adheres to WCAG 2.1 Level AA ($\ge 4.5:1$ text contrast ratio).
- [ ] Keyboard navigation (`Tab`, `Enter`, `Escape`) operates correctly with modal focus trapping.

#### 7. Documentation & Code Readability
- [ ] Code is self-documenting with clean naming conventions for variables, interfaces, and functions.
- [ ] Non-obvious domain logic, mathematical formulas (Jaccard similarity weights), and security filters include concise docstrings/comments.
- [ ] Any newly introduced environment variables are documented in `.env.example` with clear descriptions.
- [ ] [requirements.md](file:///c:/Users/happy/Desktop/campus-placement-portal/docs/requirements.md) or API documentation updated if request/response contracts evolved.

#### 8. Security Considerations
- [ ] Passwords hashed using bcrypt (salt rounds $\ge 12$) or Argon2id; plaintext passwords never logged or stored.
- [ ] Tokens stored in `HttpOnly`, `Secure`, `SameSite=Lax` cookies or managed strictly in client memory.
- [ ] All database queries use prepared statements / ORM parameterization (zero string interpolation / SQLi immune).
- [ ] User-generated HTML strings sanitized via DOMPurify before rendering (XSS immune).
- [ ] CSV export fields beginning with dangerous formula characters (`=`, `+`, `-`, `@`) prepended with single quotes (`'`) to neutralize CSV Injection (CWE-1236).
- [ ] File uploads strictly enforce `.pdf` extension, MIME type, and magic bytes (`%PDF-`), capped at 5 MB.

#### 9. Git Hygiene & Semantic Commits
- [ ] Changes are isolated into clean, focused feature branches (`feat/ats-matcher`, `fix/eligibility-backlog-check`).
- [ ] Commits adhere to Conventional Commits standard (e.g., `feat(ats): implement deterministic jaccard similarity scorer`, `test(eligibility): add boundary tests for cgpa truncation`).
- [ ] No temporary debug statements (`console.log`, `print_r`), commented-out legacy code blocks, or scratch files committed.
- [ ] Secrets, `.env` files, and local build artifacts are excluded via `.gitignore`.

#### 10. Manual Verification & Walkthrough
- [ ] The complete end-to-end user flow is manually verified in a real browser session across all involved personas:
  1. Recruiter creates drive with specific eligibility & skill criteria (initial status `PENDING_APPROVAL`).
  2. TPC Admin reviews and approves the drive (status transitions to `ACTIVE`).
  3. Student uploads PDF resume and navigates to the drive.
  4. Student verifies eligibility badge (`ELIGIBLE`) and submits application.
  5. ATS match score (deterministic Jaccard) and matched/missing skills display correctly.
  6. Recruiter logs in, inspects applicant ATS score, and shortlists candidate (`ATS_SHORTLISTED`).
  7. Student dashboard reflects stage advancement to `ATS_SHORTLISTED`.
  8. TPC Admin dashboard KPI counters increment accurately.
- [ ] A concise walkthrough recording or summary is produced to document successful verification.
