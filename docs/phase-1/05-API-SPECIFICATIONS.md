# API Specifications - Agentic AI Academic Assistant

## Table of Contents
1. [API Overview](#api-overview)
2. [Authentication APIs](#authentication-apis)
3. [Student APIs](#student-apis)
4. [Academic APIs](#academic-apis)
5. [AI/Chat APIs](#aichat-apis)
6. [Analytics APIs](#analytics-apis)
7. [Career APIs](#career-apis)
8. [Admin APIs](#admin-apis)
9. [Error Handling](#error-handling)
10. [Rate Limiting](#rate-limiting)

---

## API Overview

### Base URL
```
https://api.academic-copilot.com/api/v1
```

### API Versioning
- Version in URL path: `/api/v1`, `/api/v2`
- Deprecation headers: `Deprecation: true`, `Sunset: <date>`

### Authentication
- **Bearer Token (JWT)**: `Authorization: Bearer <token>`
- **Token Expiry**: 1 hour (access), 30 days (refresh)
- **Signature Algorithm**: RS256 (asymmetric)

### Response Format
```json
{
  "status": "success|error|warning",
  "code": 200,
  "message": "Human-readable message",
  "data": { /* response payload */ },
  "errors": [ /* error details if any */ ],
  "meta": {
    "timestamp": "2024-01-15T10:30:00Z",
    "request_id": "req_abc123xyz",
    "version": "1.0"
  }
}
```

### Common Headers
```
Content-Type: application/json
Accept: application/json
Authorization: Bearer <token>
X-Request-ID: <unique-id>
X-Client-Version: 1.0
Accept-Language: en-US
```

---

## Authentication APIs

### 1. User Registration

**POST** `/auth/register`

```json
{
  "email": "student@university.edu",
  "username": "student_username",
  "password": "SecurePassword@123",
  "first_name": "John",
  "last_name": "Doe",
  "role": "student",
  "student_id": "CS001",
  "enrollment_year": 2024
}
```

**Response** (201 Created)
```json
{
  "status": "success",
  "code": 201,
  "data": {
    "id": 123,
    "email": "student@university.edu",
    "username": "student_username",
    "first_name": "John",
    "is_verified": false,
    "verification_email_sent": true,
    "created_at": "2024-01-15T10:30:00Z"
  }
}
```

**Error** (422 Unprocessable Entity)
```json
{
  "status": "error",
  "code": 422,
  "message": "Validation failed",
  "errors": [
    {
      "field": "email",
      "message": "Email already exists",
      "code": "DUPLICATE_EMAIL"
    }
  ]
}
```

---

### 2. User Login

**POST** `/auth/login`

```json
{
  "email": "student@university.edu",
  "password": "SecurePassword@123",
  "remember_me": false
}
```

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "access_token": "eyJhbGciOiJSUzI1NiIs...",
    "refresh_token": "eyJhbGciOiJSUzI1NiIs...",
    "expires_in": 3600,
    "token_type": "Bearer",
    "user": {
      "id": 123,
      "email": "student@university.edu",
      "username": "student_username",
      "first_name": "John",
      "role": "student",
      "avatar_url": "https://cdn.example.com/avatars/123.jpg"
    }
  }
}
```

**Error** (401 Unauthorized)
```json
{
  "status": "error",
  "code": 401,
  "message": "Invalid credentials",
  "errors": [
    {
      "code": "INVALID_CREDENTIALS",
      "message": "Email or password is incorrect"
    }
  ]
}
```

---

### 3. Refresh Token

**POST** `/auth/refresh`

```json
{
  "refresh_token": "eyJhbGciOiJSUzI1NiIs..."
}
```

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "access_token": "eyJhbGciOiJSUzI1NiIs...",
    "expires_in": 3600,
    "token_type": "Bearer"
  }
}
```

---

### 4. Logout

**POST** `/auth/logout`

**Headers**: `Authorization: Bearer <token>`

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "message": "Logged out successfully"
}
```

---

## Student APIs

### 1. Get Student Profile

**GET** `/students/me`

**Headers**: `Authorization: Bearer <token>`

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "id": 123,
    "user": {
      "id": 456,
      "email": "student@university.edu",
      "first_name": "John",
      "last_name": "Doe",
      "avatar_url": "https://cdn.example.com/avatars/456.jpg"
    },
    "student_id": "CS001",
    "enrollment_year": 2024,
    "current_year": 2,
    "current_semester": 4,
    "branch": "Computer Science",
    "specialization": "AI/ML",
    "batch_year": 2022,
    "cgpa": 8.45,
    "total_credits": 120,
    "parent_email": "parent@example.com",
    "parent_phone": "+1234567890",
    "joining_date": "2022-06-15",
    "created_at": "2022-06-15T00:00:00Z",
    "updated_at": "2024-01-15T10:30:00Z"
  }
}
```

---

### 2. Update Student Profile

**PUT** `/students/me`

```json
{
  "first_name": "John",
  "last_name": "Doe",
  "phone": "+1234567890",
  "timezone": "UTC+5:30",
  "parent_email": "parent@example.com",
  "parent_phone": "+9876543210"
}
```

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "message": "Profile updated successfully",
  "data": { /* updated profile */ }
}
```

---

### 3. Get Enrollments

**GET** `/students/me/enrollments`

**Query Parameters**:
- `semester_id`: Filter by semester (optional)
- `status`: Filter by status (active, completed, dropped)
- `page`: Page number (default: 1)
- `limit`: Items per page (default: 20, max: 100)

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": [
    {
      "id": 789,
      "course": {
        "id": 1001,
        "code": "CS201",
        "name": "Data Structures",
        "credits": 4,
        "difficulty_level": "medium",
        "instructor": "Dr. Smith"
      },
      "semester": {
        "id": 501,
        "name": "Semester 4",
        "academic_year": "2023-24"
      },
      "credits": 4,
      "status": "active",
      "gpa": 8.5,
      "created_at": "2024-01-10T00:00:00Z"
    }
  ],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 42,
    "total_pages": 3
  }
}
```

---

### 4. Get Marks

**GET** `/students/me/marks`

**Query Parameters**:
- `enrollment_id`: Filter by enrollment (optional)
- `mark_type`: Filter by type (assignment, quiz, midterm, final)
- `course_id`: Filter by course (optional)

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": [
    {
      "id": 2001,
      "enrollment": {
        "id": 789,
        "course_id": 1001,
        "course_name": "Data Structures"
      },
      "mark_type": "assignment",
      "score": 18,
      "max_score": 20,
      "weight": 0.1,
      "remarks": "Good work on algorithm analysis",
      "marked_date": "2024-01-12",
      "created_at": "2024-01-12T10:00:00Z"
    }
  ]
}
```

---

### 5. Get Attendance

**GET** `/students/me/attendance`

**Query Parameters**:
- `course_id`: Filter by course (optional)
- `month`: Filter by month (YYYY-MM format)

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "overall_percentage": 87.5,
    "courses": [
      {
        "course_id": 1001,
        "course_name": "Data Structures",
        "attendance_percentage": 92.0,
        "total_classes": 50,
        "present": 46,
        "absent": 3,
        "late": 1,
        "excused": 0,
        "last_marked": "2024-01-15T14:30:00Z"
      }
    ]
  }
}
```

---

## Academic APIs

### 1. Get Courses

**GET** `/courses`

**Query Parameters**:
- `semester`: Filter by semester (optional)
- `branch`: Filter by branch (optional)
- `difficulty`: Filter by difficulty (easy, medium, hard)
- `search`: Search by code or name (optional)
- `page`: Page number (default: 1)
- `limit`: Items per page (default: 20, max: 100)

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": [
    {
      "id": 1001,
      "code": "CS201",
      "name": "Data Structures",
      "description": "Study of data structures and algorithms",
      "credits": 4,
      "difficulty_level": "medium",
      "semester": 4,
      "instructor": "Dr. Smith",
      "max_students": 100,
      "prerequisites": ["CS101"],
      "syllabus_url": "https://example.com/syllabus/cs201.pdf"
    }
  ],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 156,
    "total_pages": 8
  }
}
```

---

### 2. Enroll in Course

**POST** `/students/me/enrollments`

```json
{
  "course_id": 1001,
  "semester_id": 501
}
```

**Response** (201 Created)
```json
{
  "status": "success",
  "code": 201,
  "message": "Enrollment successful",
  "data": {
    "id": 789,
    "course_id": 1001,
    "semester_id": 501,
    "status": "active"
  }
}
```

**Error** (422 Unprocessable Entity)
```json
{
  "status": "error",
  "code": 422,
  "errors": [
    {
      "code": "PREREQUISITE_NOT_MET",
      "message": "You must complete CS101 before enrolling in CS201"
    }
  ]
}
```

---

## AI/Chat APIs

### 1. Send Chat Message

**POST** `/chat`

```json
{
  "message": "I want to become an AI Engineer. Can you create a roadmap for me?",
  "context": {
    "conversation_id": "conv_abc123",
    "previous_messages": 5
  }
}
```

**Response** (200 OK) - Streaming with Server-Sent Events (SSE)
```
data: {"type": "thinking", "content": "Analyzing student profile and goals..."}
data: {"type": "response_chunk", "content": "Great! "}
data: {"type": "response_chunk", "content": "I'd be happy to help you "}
data: {"type": "response_chunk", "content": "become an AI Engineer. "}
data: {"type": "agent_action", "action": "analyzing_courses"}
data: {"type": "response_chunk", "content": "Based on your current..."}
data: {"type": "complete", "content": "...", "tokens_used": 245, "response_time_ms": 1250}
```

---

### 2. Get Chat History

**GET** `/chat/history`

**Query Parameters**:
- `limit`: Number of recent messages (default: 50, max: 200)
- `offset`: Pagination offset (default: 0)
- `agent`: Filter by agent name (optional)

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": [
    {
      "id": "msg_123",
      "user_message": "How can I improve my CGPA?",
      "ai_response": "Here are strategies to improve your CGPA...",
      "agent_name": "academic_advisor",
      "tokens_used": 156,
      "response_time_ms": 890,
      "timestamp": "2024-01-15T10:30:00Z"
    }
  ],
  "meta": {
    "total": 342,
    "offset": 0,
    "limit": 50
  }
}
```

---

### 3. Get Recommendations

**GET** `/recommendations`

**Query Parameters**:
- `type`: Filter by recommendation type (course, resource, strategy, career, certification)
- `is_accepted`: Filter by acceptance status (true, false, null)
- `limit`: Items per page (default: 20, max: 100)

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": [
    {
      "id": "rec_456",
      "type": "course",
      "title": "Machine Learning Specialization",
      "content": {
        "course_name": "CS451 - Machine Learning",
        "reason": "Aligns with your AI/ML career goal",
        "difficulty": "medium",
        "expected_hours_per_week": 15
      },
      "confidence": 0.92,
      "is_accepted": null,
      "created_at": "2024-01-15T08:00:00Z"
    }
  ]
}
```

---

### 4. Accept/Reject Recommendation

**PUT** `/recommendations/{recommendation_id}`

```json
{
  "is_accepted": true,
  "feedback": "This looks good, I'll pursue it"
}
```

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "message": "Recommendation updated",
  "data": { /* updated recommendation */ }
}
```

---

### 5. Generate Study Plan

**POST** `/study-plans/generate`

```json
{
  "goal_id": "goal_123",
  "title": "AI Engineer 2-Year Roadmap",
  "duration_weeks": 104,
  "courses_to_include": [1001, 1002, 1003],
  "certifications": ["aws-certified-ml", "google-cloud-ml"]
}
```

**Response** (202 Accepted) - Async Operation
```json
{
  "status": "success",
  "code": 202,
  "message": "Study plan generation in progress",
  "data": {
    "task_id": "task_xyz789",
    "status": "processing",
    "estimated_completion": "2024-01-15T11:00:00Z"
  }
}
```

**Polling for Result**: **GET** `/study-plans/generate/task_xyz789`

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "id": "plan_111",
    "goal_id": "goal_123",
    "title": "AI Engineer 2-Year Roadmap",
    "start_date": "2024-01-16",
    "end_date": "2026-01-15",
    "milestones": [
      {
        "week": 1,
        "title": "Foundation - Python & Math",
        "courses": [1001],
        "resources": ["Python for Data Science", "Linear Algebra Fundamentals"],
        "deliverables": ["Mini projects", "Quiz"]
      }
    ],
    "created_at": "2024-01-15T10:30:00Z"
  }
}
```

---

## Analytics APIs

### 1. Get Dashboard Summary

**GET** `/analytics/dashboard`

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "cgpa": 8.45,
    "cgpa_trend": "up",
    "cgpa_change_pct": 2.1,
    "attendance": 87.5,
    "attendance_status": "at_risk", // below 75%
    "active_courses": 5,
    "assignments_pending": 3,
    "exams_upcoming": 2,
    "performance_vs_class": {
      "your_average": 8.45,
      "class_average": 7.82,
      "percentile": 78
    },
    "predictions": {
      "final_cgpa": {
        "value": 8.2,
        "confidence": 0.89,
        "trend": "stable"
      },
      "backlog_risk": {
        "probability": 0.05,
        "confidence": 0.92
      },
      "placement_chance": {
        "probability": 0.95,
        "confidence": 0.87
      }
    },
    "alerts": [
      {
        "type": "warning",
        "message": "Attendance is below 75%. Make-up classes recommended.",
        "priority": "high"
      }
    ]
  }
}
```

---

### 2. Get Performance Analytics

**GET** `/analytics/performance`

**Query Parameters**:
- `time_range`: (semester, year, custom)
- `start_date`, `end_date`: For custom range

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "cgpa_timeline": [
      { "semester": "Sem 1", "cgpa": 8.0, "date": "2022-06-30" },
      { "semester": "Sem 2", "cgpa": 8.2, "date": "2022-12-15" },
      { "semester": "Sem 3", "cgpa": 8.35, "date": "2023-06-30" },
      { "semester": "Sem 4", "cgpa": 8.45, "date": "2023-12-15" }
    ],
    "subject_wise": [
      {
        "course": "Data Structures",
        "score": 85,
        "class_avg": 75,
        "rank": "A"
      }
    ],
    "attendance_trend": {
      "current": 87.5,
      "previous": 90.0,
      "target": 75.0
    }
  }
}
```

---

### 3. Get Predictions

**GET** `/analytics/predictions`

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": [
    {
      "id": "pred_001",
      "type": "cgpa",
      "title": "Final CGPA Prediction",
      "predicted_value": 8.2,
      "confidence": 0.89,
      "factors": {
        "current_cgpa": 8.45,
        "trend": "stable",
        "subject_difficulty": "medium",
        "study_consistency": 0.92
      },
      "valid_until": "2024-02-15T23:59:59Z"
    },
    {
      "id": "pred_002",
      "type": "backlog_risk",
      "title": "Backlog Risk Assessment",
      "predicted_value": 0.05,
      "confidence": 0.92,
      "factors": {
        "current_performance": "strong",
        "attendance": 0.875,
        "assignment_completion": 0.95
      }
    }
  ]
}
```

---

## Career APIs

### 1. Get Career Paths

**GET** `/career/paths`

**Query Parameters**:
- `interest`: Filter by interest area (optional)
- `skills`: Filter by required skills (optional)

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": [
    {
      "id": "path_001",
      "title": "AI/ML Engineer",
      "description": "Develop AI solutions and machine learning models",
      "skills_required": ["Python", "ML", "Statistics", "Deep Learning"],
      "courses_recommended": [1001, 1002, 1003],
      "certifications": ["AWS ML", "Google Cloud ML"],
      "internships": ["path_001_int_1", "path_001_int_2"],
      "companies": ["Google", "Microsoft", "AWS"],
      "avg_salary": "$120,000",
      "job_market_demand": "very_high",
      "match_percentage": 92
    }
  ]
}
```

---

### 2. Get Internships

**GET** `/internships`

**Query Parameters**:
- `career_path_id`: Filter by career path (optional)
- `deadline_before`: Filter by deadline (optional)
- `eligibility_cgpa`: Filter by CGPA requirement (optional)
- `page`: Pagination (default: 1)

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": [
    {
      "id": "int_001",
      "company_name": "Google India",
      "position": "ML Intern - Search",
      "duration_months": 3,
      "stipend": 100000,
      "location": "Bangalore",
      "deadline": "2024-02-01",
      "requirements": ["Strong DSA", "Python", "ML Basics"],
      "eligibility_cgpa": 8.0,
      "posted_date": "2024-01-10"
    }
  ]
}
```

---

### 3. Build Resume

**POST** `/resumes`

```json
{
  "content": {
    "summary": "Passionate AI enthusiast with...",
    "experience": [],
    "education": {
      "degree": "B.Tech",
      "field": "Computer Science",
      "college": "XYZ University",
      "cgpa": 8.45
    },
    "skills": ["Python", "Machine Learning", "Data Analysis"],
    "certifications": ["AWS ML"]
  }
}
```

**Response** (201 Created)
```json
{
  "status": "success",
  "code": 201,
  "data": {
    "id": "resume_001",
    "version": 1,
    "is_primary": true,
    "ats_score": 0.85,
    "file_url": "https://cdn.example.com/resumes/resume_001.pdf",
    "created_at": "2024-01-15T10:30:00Z"
  }
}
```

---

## Admin APIs

### 1. Get All Users

**GET** `/admin/users`

**Headers**: `Authorization: Bearer <admin_token>`

**Query Parameters**:
- `role`: Filter by role (student, faculty, admin, advisor, staff)
- `is_active`: Filter by active status
- `search`: Search by email or name
- `page`: Pagination

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": [
    {
      "id": 123,
      "email": "student@university.edu",
      "first_name": "John",
      "last_name": "Doe",
      "role": "student",
      "is_active": true,
      "last_login": "2024-01-15T10:30:00Z",
      "created_at": "2022-06-15T00:00:00Z"
    }
  ],
  "meta": {
    "total": 5000,
    "page": 1,
    "limit": 20
  }
}
```

---

### 2. Upload Marks (Bulk)

**POST** `/admin/marks/bulk-upload`

**Content-Type**: `multipart/form-data`

```
course_id: 1001
semester_id: 501
file: <CSV file>
```

**CSV Format**:
```
student_id,mark_type,score,max_score,weight
CS001,assignment,18,20,0.1
CS002,assignment,19,20,0.1
CS003,assignment,17,20,0.1
```

**Response** (202 Accepted)
```json
{
  "status": "success",
  "code": 202,
  "data": {
    "task_id": "bulk_upload_123",
    "total_records": 150,
    "status": "processing",
    "estimated_completion": "2024-01-15T11:00:00Z"
  }
}
```

---

### 3. Get System Reports

**GET** `/admin/reports`

**Query Parameters**:
- `report_type`: (placement_stats, performance_stats, attendance_stats)
- `filters`: Custom filters (JSON)

**Response** (200 OK)
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "report_type": "placement_stats",
    "academic_year": "2023-24",
    "total_students": 500,
    "placed": 475,
    "placement_rate": 95.0,
    "average_ctc": 1250000,
    "highest_ctc": 4000000,
    "lowest_ctc": 500000,
    "top_recruiters": [
      {"company": "Google", "count": 25},
      {"company": "Microsoft", "count": 20}
    ]
  }
}
```

---

## Error Handling

### Standard Error Response

```json
{
  "status": "error",
  "code": 400,
  "message": "Bad Request",
  "errors": [
    {
      "code": "INVALID_INPUT",
      "field": "email",
      "message": "Email format is invalid"
    }
  ],
  "meta": {
    "request_id": "req_abc123xyz",
    "timestamp": "2024-01-15T10:30:00Z"
  }
}
```

### HTTP Status Codes

| Code | Meaning | Example |
|------|---------|---------|
| 200 | OK | Successful GET/PUT |
| 201 | Created | Successful POST |
| 202 | Accepted | Async operation started |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Invalid input |
| 401 | Unauthorized | Missing/invalid token |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource doesn't exist |
| 409 | Conflict | Duplicate resource |
| 422 | Unprocessable Entity | Validation failed |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Server error |
| 503 | Service Unavailable | Maintenance/downtime |

### Error Codes

```
INVALID_INPUT - Input validation failed
UNAUTHORIZED - User not authenticated
FORBIDDEN - User lacks permissions
NOT_FOUND - Resource not found
DUPLICATE_RESOURCE - Resource already exists
PREREQUISITE_NOT_MET - Business rule violated
EXTERNAL_SERVICE_ERROR - Third-party service failed
DATABASE_ERROR - Database operation failed
RATE_LIMIT_EXCEEDED - Too many requests
INTERNAL_SERVER_ERROR - Unexpected server error
```

---

## Rate Limiting

### Rate Limit Headers

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 42
X-RateLimit-Reset: 1705320600
```

### Rate Limits

| Endpoint | Limit | Window |
|----------|-------|--------|
| Authentication | 5 requests | 15 minutes |
| Chat | 30 messages | 1 hour |
| General APIs | 100 requests | 1 minute |
| Bulk Upload | 10 uploads | 1 hour |
| Admin APIs | 50 requests | 1 minute |

### Rate Limit Response (429)

```json
{
  "status": "error",
  "code": 429,
  "message": "Too Many Requests",
  "errors": [
    {
      "code": "RATE_LIMIT_EXCEEDED",
      "message": "You have exceeded the rate limit",
      "retry_after": 60
    }
  ],
  "meta": {
    "reset_time": "2024-01-15T10:31:00Z"
  }
}
```

---

## API Documentation

### Swagger/OpenAPI 3.0

The complete OpenAPI specification is available at:
```
https://api.academic-copilot.com/docs
https://api.academic-copilot.com/docs/json (JSON format)
```

### API Endpoint Summary

**Authentication**: 4 endpoints  
**Student**: 5 endpoints  
**Academic**: 2 endpoints  
**AI/Chat**: 5 endpoints  
**Analytics**: 3 endpoints  
**Career**: 3 endpoints  
**Admin**: 3+ endpoints  

**Total**: 28+ endpoints

---

## Summary

✅ RESTful API design following REST principles  
✅ Comprehensive error handling  
✅ Rate limiting for fairness  
✅ JWT-based authentication  
✅ Pagination support  
✅ Async operations with polling  
✅ OpenAPI 3.0 documentation  
✅ Versioning strategy  

