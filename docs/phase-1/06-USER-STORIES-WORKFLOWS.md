# User Stories & Workflows - Agentic AI Academic Assistant

## Table of Contents
1. [User Personas](#user-personas)
2. [Student User Stories](#student-user-stories)
3. [Faculty User Stories](#faculty-user-stories)
4. [Admin User Stories](#admin-user-stories)
5. [Key Workflows](#key-workflows)
6. [Use Case Diagrams](#use-case-diagrams)

---

## User Personas

### Persona 1: Arjun (Second-Year CS Student)

**Demographics**:
- Age: 19
- Year: 2nd Year, Computer Science
- CGPA: 8.2
- Attendance: 82% (slightly low)
- Goals: Become an AI Engineer, secure good placement

**Pain Points**:
- Overwhelmed with course materials from multiple sources
- Unsure about which electives to choose
- Struggling with time management between assignments and self-study
- Worried about placement competitiveness

**Needs**:
- Personalized study recommendations
- Clear roadmap for AI/ML career path
- Help managing workload across courses
- Early warning about attendance/performance issues

**Technology Comfort**: High (regular GitHub user, knows Python)

---

### Persona 2: Dr. Sharma (Faculty Member)

**Demographics**:
- Age: 45
- Experience: 15 years of teaching
- Courses: Data Structures, Algorithms, AI
- Students: 120+ per semester

**Pain Points**:
- Time-consuming manual mark entry and attendance tracking
- Difficulty identifying struggling students early
- Unable to provide personalized feedback at scale
- Workload increasing with class size

**Needs**:
- Automated attendance and mark management
- Analytics to identify at-risk students
- Tools to provide timely interventions
- Efficient communication with students and parents

**Technology Comfort**: Medium (prefers simple interfaces)

---

### Persona 3: Priya (Academic Advisor)

**Demographics**:
- Age: 38
- Role: Academic Advisor for 500+ students
- Years in role: 8 years

**Pain Points**:
- Manually counseling hundreds of students
- Limited time for personalized guidance
- Difficulty tracking student progress over time
- Pressure from parents for placement guidance

**Needs**:
- Automated initial counseling and routing
- Student progress dashboards
- Career path recommendations
- Placement outcome tracking

**Technology Comfort**: Medium-High

---

### Persona 4: Vikram (Admin/IT Head)

**Demographics**:
- Age: 42
- Role: IT Director
- Team Size: 5 people

**Pain Points**:
- Manual data management across systems
- Data consistency issues
- Security and compliance concerns
- Limited visibility into system usage

**Needs**:
- Centralized data management
- Automated reporting
- Audit trails and compliance
- System health monitoring

**Technology Comfort**: High

---

## Student User Stories

### US-STUDENT-001: View Personalized Dashboard

**As a** student  
**I want to** see a personalized dashboard with my academic performance at a glance  
**So that** I can quickly understand my current status and areas needing improvement

**Acceptance Criteria**:
- Dashboard shows CGPA, attendance %, pending assignments
- Color-coded alerts for at-risk areas (red, yellow, green)
- Displays upcoming exams and deadlines
- Shows comparison with class average
- Loads within 1 second

**Implementation Details**:
- Fetch data from analytics microservice
- Cache for 5 minutes
- Real-time updates via WebSocket for alerts

---

### US-STUDENT-002: Chat with Academic Advisor Agent

**As a** student  
**I want to** ask questions about my academics and get instant advice from an AI advisor  
**So that** I don't have to wait for office hours or email responses

**Acceptance Criteria**:
- Chat interface accepts natural language questions
- AI provides relevant, accurate answers within 3 seconds
- Conversation history is saved
- Can ask follow-up questions in context
- Can escalate to real advisor if needed

**Example Queries**:
- "Which electives should I take next semester?"
- "How can I improve my CGPA?"
- "What's my placement probability?"
- "Create a study plan for OS exam"

**Implementation Details**:
- Use LangGraph multi-agent framework
- Academic Advisor Agent handles routing
- RAG for contextual knowledge
- Memory system for conversation context

---

### US-STUDENT-003: Get Career Roadmap

**As a** an aspiring AI Engineer  
**I want to** get a personalized roadmap showing courses, certifications, and skills I need  
**So that** I have a clear path to my career goal

**Acceptance Criteria**:
- System suggests courses aligned with goal
- Shows timeline (semesters/years)
- Recommends certifications (AWS, Google Cloud, etc.)
- Lists resources for learning
- Predicts placement chances with recommendations
- Can download as PDF

**Example Output**:
```
AI Engineer Career Roadmap
├── Year 1 (Current)
│   ├── CS451 - Machine Learning (Sem 4)
│   ├── CS452 - Deep Learning (Sem 4)
│   └── Certification: AWS ML Fundamentals
├── Year 2
│   ├── CS551 - Advanced ML (Sem 5)
│   ├── CS552 - NLP (Sem 6)
│   └── Internship: ML Engineer at startup
└── Post-Graduation
    ├── Google Cloud ML Engineer Certification
    └── Senior ML Engineer role (Salary: 15+ LPA)
```

**Implementation Details**:
- Career Agent processes goal
- Queries course database and ML models
- Generates async, polls for completion

---

### US-STUDENT-004: Track Study Progress

**As a** a student with a study plan  
**I want to** track my progress against milestones  
**So that** I stay motivated and on track

**Acceptance Criteria**:
- Progress shown as % complete for each milestone
- Can mark tasks as complete manually
- Automatic detection of task completion from marks/assignments
- Reminders for upcoming milestones
- Ability to reschedule if falling behind

**Implementation Details**:
- Study Plan Agent monitors progress
- Syncs with marks and assignments automatically
- Sends notifications 3 days before milestone

---

### US-STUDENT-005: Receive Automated Alerts

**As a** a student  
**I want to** receive alerts about issues early  
**So that** I can take corrective action before it's too late

**Acceptance Criteria**:
- Alert for low attendance (<75%)
- Alert for low marks in assignments
- Alert for approaching deadlines
- Alert for predicted backlog/poor performance
- Can customize alert preferences (email, SMS, push)
- No spam (max 2 alerts/day unless critical)

**Example Alerts**:
- "Your attendance in CS451 is 70%. Make-up classes available Friday 4-5 PM"
- "You're at risk of backlog in OS. Recommended: Attend labs this week"
- "Final exam for DS in 7 days. Your practice test score: 65%. Review linked resources"

**Implementation Details**:
- Notification Agent runs hourly
- Checks predictions and thresholds
- Multi-channel delivery (email, SMS, push)
- Exponential backoff for retries

---

### US-STUDENT-006: Build and Improve Resume

**As a** a final-year student  
**I want to** build my resume with AI suggestions  
**So that** my resume is polished and ATS-optimized

**Acceptance Criteria**:
- AI suggests resume content based on my profile
- Shows ATS compatibility score
- Provides improvement suggestions
- Can generate multiple versions
- Export as PDF/Word/JSON
- View template examples

**Implementation Details**:
- Resume Agent processes profile
- ML model scores ATS compatibility
- Generates multiple versions with different focuses

---

### US-STUDENT-007: Find and Apply for Internships

**As a** a student  
**I want to** find internships matching my skills and career goals  
**So that** I can gain practical experience

**Acceptance Criteria**:
- Filter by career path, company, stipend, location
- See matching score for each internship
- Can save to favorites
- Easy application workflow
- Get notified about new matching internships
- Deadline reminders

**Implementation Details**:
- Career Agent provides recommendations
- Semantic search on internship descriptions
- Similarity matching with student profile

---

## Faculty User Stories

### US-FACULTY-001: Enter Marks and Attendance

**As a** faculty member  
**I want to** efficiently enter marks and attendance for large classes  
**So that** I don't spend excessive time on administrative work

**Acceptance Criteria**:
- Web form for individual entry
- Bulk upload via CSV
- Mobile app for attendance marking in class
- Can edit/update previously entered data
- Audit trail of changes
- Prevents data entry errors with validations

**Example CSV**:
```
enrollment_id,mark_type,score,max_score
E001,assignment1,18,20
E002,assignment1,19,20
E003,assignment1,17,20
```

**Implementation Details**:
- FastAPI bulk upload endpoint
- Celery job for processing
- Validation and error reporting
- Async updates to student dashboards

---

### US-FACULTY-002: Identify At-Risk Students

**As a** faculty member  
**I want to** get a list of students who are at risk of backlog  
**So that** I can provide timely interventions

**Acceptance Criteria**:
- Dashboard showing risk-flagged students
- Shows reason for each flag (low marks, attendance, etc.)
- Can view student details and performance history
- Can send targeted communications
- Ability to schedule interventions (counseling, labs, etc.)

**Implementation Details**:
- Performance Agent analyzes marks, attendance
- ML model predicts backlog risk
- Weekly reports generated

---

### US-FACULTY-003: Provide Personalized Feedback

**As a** faculty member  
**I want to** provide feedback on student work  
**So that** students understand their mistakes and improve

**Acceptance Criteria**:
- Add detailed feedback to assignments/exams
- Can include rubric-based scoring
- Student gets notified with feedback
- Can request follow-up consultation
- Feedback searchable by student

**Implementation Details**:
- Marks table stores feedback
- Notifications sent to student
- Feedback stored for reference

---

### US-FACULTY-004: Generate Course Reports

**As a** faculty member  
**I want to** generate reports on course performance  
**So that** I can assess teaching effectiveness and student learning

**Acceptance Criteria**:
- Class average, median, distribution graphs
- Student-wise performance
- Comparison with previous offerings
- Export to PDF/Excel
- Identify challenging topics

**Implementation Details**:
- Analytical queries on marks table
- Materialized views for performance
- PDF generation via ReportLab

---

## Admin User Stories

### US-ADMIN-001: Bulk Data Upload

**As a** an administrator  
**I want to** upload bulk student and course data  
**So that** I can manage data efficiently without manual entry

**Acceptance Criteria**:
- Upload CSV files for users, students, courses, enrollments
- Validate data before import
- Show error report for invalid rows
- Option to skip/fix errors
- Rollback capability
- Audit trail of uploads

**Implementation Details**:
- Dedicated bulk upload API
- Celery job for async processing
- Validation against schema
- Transaction rollback on errors

---

### US-ADMIN-002: System Monitoring Dashboard

**As a** an administrator  
**I want to** monitor system health and performance  
**So that** I can proactively address issues

**Acceptance Criteria**:
- Real-time metrics: CPU, memory, disk usage
- API response times and error rates
- Database connection pool status
- Queue depths (Celery, RabbitMQ)
- User activity heatmap
- Alerts for critical issues

**Implementation Details**:
- Prometheus scrapes metrics
- Grafana dashboards
- AlertManager for notifications

---

### US-ADMIN-003: Audit and Compliance Reporting

**As a** an administrator  
**I want to** view audit logs and generate compliance reports  
**So that** I can ensure data integrity and meet regulatory requirements

**Acceptance Criteria**:
- View all data modifications with user, timestamp, old/new values
- Filter by resource type, user, date range
- Export audit logs
- GDPR compliance report (data export, deletion)
- FERPA compliance report

**Implementation Details**:
- Activity logs table
- Immutable audit logs
- GDPR/FERPA data export functionality

---

## Key Workflows

### Workflow 1: First-Time Student Onboarding

```
START
  ↓
[Register Account] → [Email Verification]
  ↓
[Complete Profile] (Branch, Year, Interests)
  ↓
[Enroll in Courses] → [View Enrollments]
  ↓
[Chat Welcome] "Hi! Let me help you succeed..."
  ↓
[Goal Setting] "What's your career goal?"
  ↓
[System Generates]
  • Study Plan
  • Course Recommendations
  • Resource List
  ↓
[Dashboard Setup]
  • Widgets selection
  • Notification preferences
  ↓
END - Ready to use system
```

**Duration**: ~10 minutes  
**Touch Points**: 6  
**Critical Path**: Email verification → Course enrollment

---

### Workflow 2: Semester Planning (Student)

```
START (Beginning of Semester)
  ↓
[View Recommended Courses]
  ↓
[Chat with Advisor]
  "Which courses should I take?"
  "How many credits should I take?"
  ↓
[Get Recommendations]
  • Show course options
  • Highlight prerequisites
  • Show workload estimates
  ↓
[Enroll in Courses]
  ↓
[System Creates Study Plan]
  • Generate schedule
  • Set milestones
  • Link resources
  ↓
[Confirm & Save]
  ↓
END - Ready for semester
```

**Duration**: ~20 minutes  
**Frequency**: Once per semester  
**Success Metric**: All courses enrolled with study plan generated

---

### Workflow 3: Performance Monitoring (Faculty)

```
START (Weekly)
  ↓
[Faculty Reviews Dashboard]
  • At-risk students list
  • Performance trends
  • Attendance summary
  ↓
[Decision Point]
  ├─ All good? → [No action]
  ├─ Some at-risk? → [Next step]
  └─ Critical cases? → [Urgent]
  ↓
[For At-Risk Students]
  ├─ [Review Details]
  │   • Performance history
  │   • Attendance trend
  │   • Assignment status
  ↓
  ├─ [Decide Intervention]
  │   • Send message
  │   • Schedule lab
  │   • Refer to advisor
  ↓
  └─ [Log Action]
  ↓
END
```

**Duration**: ~30 minutes  
**Frequency**: Weekly  
**Success Metric**: At-risk students identified and interventions logged

---

### Workflow 4: Student Performance Prediction

```
START (Every Night @ 2 AM)
  ↓
[Fetch All Active Students]
  ├─ Fetch last semester marks
  ├─ Fetch current attendance
  ├─ Fetch current semester progress
  ↓
[Extract Features]
  • Marks trend
  • Attendance %
  • Assignment completion %
  • Study consistency
  ↓
[Run ML Models]
  • CGPA Prediction (XGBoost)
  • Backlog Risk (LightGBM)
  • Placement Probability (Ensemble)
  ↓
[Cache Results] (Redis)
  ↓
[Generate Alerts]
  For each at-risk student:
  • At-risk of backlog?
  • Attendance below threshold?
  • Placement probability < 50%?
  ↓
[Send Notifications]
  • Email
  • SMS (high priority)
  • In-app alert
  ↓
[Store Results]
  • predictions table
  • notifications table
  ↓
END - ~2 hours for 100k students
```

**Frequency**: Daily (nightly)  
**SLA**: Completion within 2 hours  
**Success Metric**: All students analyzed, alerts sent to 95%+ at-risk students

---

### Workflow 5: Career Path to Placement

```
START (1st Semester)
  ↓
[Student Takes Career Quiz]
  • Interests
  • Skills
  • Long-term goals
  ↓
[System Generates Career Paths]
  (AI Engineer, Data Scientist, DevOps Engineer, etc.)
  ↓
[Student Selects Path]
  ↓
[Generate Roadmap]
  • Recommended courses
  • Required certifications
  • Internship guidance
  • Resources
  ↓
LOOP (Each Semester)
  ├─ [Track Progress]
  ├─ [Update Roadmap]
  ├─ [Suggest Internships]
  └─ [Provide Learning Resources]
  ↓
[Final Year]
  ├─ [Resume Building]
  ├─ [Interview Preparation]
  ├─ [Placement Portals]
  └─ [Company Matching]
  ↓
END - Secured Internship/Placement
```

**Duration**: 2-4 years  
**Touchpoints**: 8+ per year  
**Success Metric**: Internship secured in 3rd year, placement in 4th year

---

## Use Case Diagrams

### Student Use Cases

```
┌─────────────────────────────────────────────────┐
│            AGENTIC ACADEMIC ASSISTANT           │
├─────────────────────────────────────────────────┤
│                                                 │
│         ┌─────────────────┐                    │
│         │    Student      │                    │
│         └────────┬────────┘                    │
│                  │                             │
│     ┌────────────┼────────────┐               │
│     │            │            │               │
│     ▼            ▼            ▼               │
│  [View        [Chat with   [Track           │
│   Dashboard]   AI Advisor]   Progress]       │
│     │            │            │              │
│     └────────────┼────────────┘              │
│                  │                           │
│                  ▼                           │
│          [Get Recommendations]              │
│                  │                           │
│     ┌────────────┼────────────┐             │
│     │            │            │             │
│     ▼            ▼            ▼             │
│  [Receive    [View Study  [Build          │
│   Alerts]     Plans]       Career Path]    │
│                                             │
│     ┌────────────┐                         │
│     │ View Marks │                         │
│     └────────────┘                         │
│                                             │
│     ┌────────────────────────────────┐    │
│     │ Find Internships & Apply        │    │
│     └────────────────────────────────┘    │
│                                             │
└─────────────────────────────────────────────┴───┘
```

### Faculty Use Cases

```
┌─────────────────────────────────────────────────┐
│            AGENTIC ACADEMIC ASSISTANT           │
├─────────────────────────────────────────────────┤
│                                                 │
│         ┌─────────────────┐                    │
│         │     Faculty     │                    │
│         └────────┬────────┘                    │
│                  │                             │
│     ┌────────────┼────────────┐               │
│     │            │            │               │
│     ▼            ▼            ▼               │
│  [Enter      [View At-     [Generate        │
│   Marks]      Risk         Course           │
│     &         Students]    Reports]         │
│   Attendance                                │
│     │            │            │              │
│     └────────────┼────────────┘              │
│                  │                           │
│                  ▼                           │
│        [Identify Interventions]             │
│                  │                           │
│                  ▼                           │
│        [Provide Feedback]                   │
│                                             │
│     ┌────────────────────────────────┐    │
│     │ Communicate with Students      │    │
│     └────────────────────────────────┘    │
│                                             │
└─────────────────────────────────────────────┴───┘
```

### Admin Use Cases

```
┌─────────────────────────────────────────────────┐
│            AGENTIC ACADEMIC ASSISTANT           │
├─────────────────────────────────────────────────┤
│                                                 │
│         ┌─────────────────┐                    │
│         │     Admin       │                    │
│         └────────┬────────┘                    │
│                  │                             │
│     ┌────────────┼────────────┬────────────┐  │
│     │            │            │            │  │
│     ▼            ▼            ▼            ▼  │
│  [Bulk Data  [Monitor     [Generate      [Manage        │
│   Upload]    System]      Reports]       Users]         │
│     │            │            │            │             │
│     └────────────┼────────────┼────────────┘            │
│                  │                                       │
│                  ▼                                       │
│         [View Audit Logs]                              │
│                  │                                       │
│                  ▼                                       │
│    [GDPR/Compliance Reporting]                         │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## User Journey Map: Arjun (2nd Year Student)

```
Time: 8 months into semester

STAGE 1: AWARENESS (Week 1-2)
   ↓
   Activity: Discovers system via orientation
   Touchpoint: Email + Advisor demo
   Goal: Understand how system helps
   Emotion: Curious, skeptical
   Action: Creates account

STAGE 2: ONBOARDING (Week 2-3)
   ↓
   Activity: Completes profile, enrolls courses
   Touchpoint: Welcome wizard, course list
   Goal: Get system ready
   Emotion: Excited, overwhelmed
   Action: Enrolls in 5 courses, sets preferences

STAGE 3: DISCOVERY (Week 3-4)
   ↓
   Activity: Explores AI Advisor
   Touchpoint: Chat interface, recommendations
   Goal: Get advice on courses/career
   Emotion: Impressed, engaged
   Questions Asked:
     • "Which electives should I take?"
     • "What's my placement probability?"
     • "How can I improve my CGPA?"
   Action: Gets career roadmap, creates study plan

STAGE 4: ENGAGEMENT (Week 4-12)
   ↓
   Activity: Regular system usage
   Touchpoints: Dashboard, alerts, study plan
   Goal: Stay on track academically
   Emotion: Motivated, focused
   Key Events:
     • Receives attendance alert (Week 6)
     • Takes advice: Attends more labs
     • Marks improve from 75% to 85% (Week 10)
     • Gets good recommendation for internship search

STAGE 5: OPTIMIZATION (Week 12+)
   ↓
   Activity: Advanced features
   Touchpoints: Resume builder, internship matching
   Goal: Prepare for career opportunities
   Emotion: Confident, proactive
   Action: Builds resume, applies for internships
   Result: Secured internship offer!

SUCCESS METRIC: High engagement, improved grades, career trajectory
```

---

## Summary

**Total User Stories**: 15+  
**Key Workflows**: 5  
**Personas Covered**: 4  
**Total Touchpoints**: 50+  
**Success Metrics**: Clearly defined for each story  

