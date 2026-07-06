# System Architecture - Agentic AI Academic Assistant

## Table of Contents
1. [High-Level Architecture](#high-level-architecture)
2. [Architecture Layers](#architecture-layers)
3. [Component Diagram](#component-diagram)
4. [Technology Stack](#technology-stack)
5. [Data Flow Diagrams](#data-flow-diagrams)
6. [Agent Architecture](#agent-architecture)
7. [Deployment Architecture](#deployment-architecture)

---

## High-Level Architecture

The system follows a **layered microservices architecture** with async task processing and agentic AI workflows.

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │  Web (Next.js)  │ Mobile (React Native) │ Voice Assistant│
│  └──────────────┘  └──────────────┘  └──────────────┘          │
└────────────────────────┬────────────────────────────────────────┘
                         │ HTTPS/WebSocket
┌────────────────────────▼────────────────────────────────────────┐
│                      API GATEWAY LAYER                           │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ • Request/Response Handling                              │  │
│  │ • Authentication & Authorization                         │  │
│  │ • Rate Limiting & Throttling                            │  │
│  │ • Logging & Monitoring                                  │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────┬────────────────────────────────────────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
┌───────▼──────┐  ┌─────▼────────┐  ┌──▼──────────┐
│ CORE API     │  │ AGENT LAYER  │  │ RAG LAYER   │
│ SERVICES     │  │              │  │             │
└───────┬──────┘  └─────┬────────┘  └──┬──────────┘
        │                │                │
        └────────────────┼────────────────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
┌───────▼──────┐  ┌─────▼────────┐  ┌──▼──────────┐
│ DATABASE     │  │ CACHE LAYER  │  │ SEARCH INDEX│
│ (PostgreSQL) │  │ (Redis)      │  │ (Chroma)    │
└──────────────┘  └──────────────┘  └─────────────┘
```

---

## Architecture Layers

### 1. **Presentation Layer**
- **Frontend**: Next.js with React, TypeScript, Tailwind CSS
- **Mobile**: React Native (iOS/Android)
- **Voice**: Speech-to-Text & Text-to-Speech integration
- **Responsibilities**: UI rendering, user interactions, form validation

### 2. **API Gateway Layer**
- **Technology**: FastAPI or Kong API Gateway
- **Responsibilities**:
  - Request/Response routing
  - JWT validation
  - Rate limiting
  - CORS handling
  - Logging & tracing

### 3. **Core API Services Layer**
- **Authentication Service**: User login, token management
- **Student Service**: Profile, marks, attendance
- **Academic Service**: Courses, enrollments, semesters
- **Career Service**: Internships, placements, resume
- **Analytics Service**: Predictions, dashboards
- **Admin Service**: User management, configurations

### 4. **Agent Orchestration Layer**
- **LangGraph Framework**: Multi-agent coordination
- **Agents**:
  - Coordinator Agent (task routing)
  - Academic Advisor Agent (guidance)
  - Study Planner Agent (scheduling)
  - Performance Agent (analytics)
  - Resource Agent (learning materials)
  - Career Agent (career paths)
  - Notification Agent (alerts)
  - Resume Agent (resume building)
- **Memory System**: Vector embeddings + semantic search

### 5. **RAG (Retrieval-Augmented Generation) Layer**
- **Document Processing**: PDF, DOCX, PPTX extraction
- **Chunking**: Semantic chunking (fixed & sliding window)
- **Embeddings**: OpenAI, Gemini, or local models
- **Vector Store**: ChromaDB or Pinecone
- **Retriever**: BM25 + semantic hybrid search
- **LLM Integration**: OpenAI, Gemini, Groq, Ollama

### 6. **ML/Analytics Layer**
- **CGPA Prediction**: XGBoost model
- **Attendance Prediction**: LightGBM model
- **Backlog Risk**: Scikit-learn classifier
- **Placement Prediction**: Ensemble methods
- **Model Training Pipeline**: Automated retraining
- **Feature Store**: Historical metrics

### 7. **Data Layer**
- **Primary Database**: PostgreSQL (OLTP)
- **Cache Layer**: Redis (sessions, frequent queries)
- **Search Index**: Elasticsearch/Chroma (document search)
- **File Storage**: AWS S3 (documents, uploads)
- **Time-Series DB**: InfluxDB (metrics)

### 8. **Infrastructure Layer**
- **Container Orchestration**: Kubernetes
- **Message Queue**: RabbitMQ / Apache Kafka
- **Job Scheduler**: Celery + Redis
- **Logging**: ELK Stack / Loki
- **Monitoring**: Prometheus + Grafana
- **Tracing**: Jaeger

---

## Component Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                        EXTERNAL SERVICES                         │
│  ┌──────────┐ ┌──────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐│
│  │  OpenAI  │ │  Gemini  │ │ Groq    │ │ Ollama  │ │SendGrid ││
│  └──────────┘ └──────────┘ └─────────┘ └─────────┘ └─────────┘│
│  ┌──────────┐ ┌──────────┐ ┌─────────┐                         │
│  │ Firebase │ │ Twilio   │ │  Stripe │                         │
│  └──────────┘ └──────────┘ └─────────┘                         │
└────────────────────┬────────────────────────────────────────────┘
                     │
┌─────────────────────▼────────────────────────────────────────────┐
│                      SERVICE LAYER                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────┐ │
│  │  Auth Service    │  │Student Service   │  │Academic Svc  │ │
│  │ • JWT            │  │ • Profile        │  │ • Courses    │ │
│  │ • 2FA            │  │ • Performance    │  │ • Enrollment │ │
│  │ • OAuth          │  │ • Attendance     │  │ • Marks      │ │
│  └──────────────────┘  └──────────────────┘  └──────────────┘ │
│                                                                  │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────┐ │
│  │Career Service    │  │Analytics Service │  │Admin Service │ │
│  │ • Internships    │  │ • Predictions    │  │ • Users      │ │
│  │ • Placements     │  │ • Dashboards     │  │ • Settings   │ │
│  │ • Resume         │  │ • Reports        │  │ • Audit      │ │
│  └──────────────────┘  └──────────────────┘  └──────────────┘ │
│                                                                  │
└────────────┬─────────────────────────────────────────┬──────────┘
             │                                         │
┌────────────▼─────────────────┐     ┌────────────────▼──────────┐
│   AGENT ORCHESTRATION LAYER  │     │   RAG SYSTEM LAYER       │
├──────────────────────────────┤     ├──────────────────────────┤
│                              │     │                          │
│ ┌─────────────────────────┐ │     │ ┌──────────────────────┐ │
│ │ Coordinator Agent       │ │     │ │Document Processor   │ │
│ │ ┌────────────────────┐ │ │     │ ├──────────────────────┤ │
│ │ │ • Task Router      │ │ │     │ │ • PDF Extractor    │ │
│ │ │ • Memory Manager   │ │ │     │ │ • DOCX Parser      │ │
│ │ │ • Workflow Exec    │ │ │     │ │ • Text Chunking    │ │
│ │ └────────────────────┘ │ │     │ └──────────────────────┘ │
│ │                        │ │     │                          │
│ │ ┌──────┐ ┌──────────┐ │ │     │ ┌──────────────────────┐ │
│ │ │Advisor│ │Planner   │ │ │     │ │Embedding Engine    │ │
│ │ │Agent  │ │Agent     │ │ │     │ │ • OpenAI Embeddings│ │
│ │ └──────┘ └──────────┘ │ │     │ │ • Local Models     │ │
│ │                        │ │     │ └──────────────────────┘ │
│ │ ┌──────┐ ┌──────────┐ │ │     │                          │
│ │ │Career│ │Resource  │ │ │     │ ┌──────────────────────┐ │
│ │ │Agent │ │Agent     │ │ │     │ │Vector Store        │ │
│ │ └──────┘ └──────────┘ │ │     │ │ • ChromaDB         │ │
│ │                        │ │     │ │ • Pinecone         │ │
│ └─────────────────────────┘ │     │ └──────────────────────┘ │
│                              │     │                          │
└──────────────┬───────────────┘     └────────┬─────────────────┘
               │                              │
               └──────────────┬───────────────┘
                              │
┌─────────────────────────────▼─────────────────────────────────────┐
│                      DATA & CACHE LAYER                            │
├───────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌──────────────────┐  ┌──────────────┐  ┌──────────────────┐  │
│  │ PostgreSQL DB    │  │  Redis Cache │  │  Elasticsearch   │  │
│  │ • Users          │  │  • Sessions  │  │  • Document Idx  │  │
│  │ • Students       │  │  • Queries   │  │  • Chat History  │  │
│  │ • Courses        │  │  • ML Models │  │  • Logs          │  │
│  │ • Enrollments    │  │              │  │                  │  │
│  │ • Marks          │  │              │  │                  │  │
│  │ • Attendance     │  │              │  │                  │  │
│  │ • Analytics      │  │              │  │                  │  │
│  └──────────────────┘  └──────────────┘  └──────────────────┘  │
│                                                                   │
│  ┌──────────────────┐  ┌──────────────┐  ┌──────────────────┐  │
│  │ Message Queue    │  │  File Storage│  │  Time-Series DB  │  │
│  │ • RabbitMQ/Kafka │  │  • AWS S3    │  │  • InfluxDB      │  │
│  │ • Events         │  │  • Documents │  │  • Metrics       │  │
│  │ • Tasks          │  │  • Uploads   │  │  • Performance   │  │
│  └──────────────────┘  └──────────────┘  └──────────────────┘  │
│                                                                   │
└───────────────────────────────────────────────────────────────────┘
```

---

## Technology Stack

### Backend
```
Runtime:          Python 3.11+
Web Framework:    FastAPI + Uvicorn
ORM:              SQLAlchemy 2.0
Migrations:       Alembic
API Docs:         OpenAPI/Swagger

Authentication:   PyJWT, python-jose
Validation:       Pydantic v2
CORS:             fastapi.middleware.cors

Task Queue:       Celery
Message Broker:   RabbitMQ / Redis
Job Scheduler:    APScheduler
```

### AI/ML
```
LLM Framework:    LangChain / LangGraph
Embeddings:       sentence-transformers, OpenAI
Vector Store:     Chroma, Pinecone
RAG:              LangChain RAG chain
PDF Processing:   PyPDF, pdfplumber
Document:         python-docx, python-pptx

ML Models:        scikit-learn, XGBoost, LightGBM
Feature Store:    Pandas, Polars
Model Serving:    FastAPI endpoints
Monitoring:       MLflow, Weights & Biases
```

### Frontend
```
Framework:        Next.js 14+
Library:          React 18+
Language:         TypeScript
Styling:          Tailwind CSS
UI Components:    Shadcn/ui
State:            Redux Toolkit, Zustand
HTTP:             Axios, TanStack Query
Charts:           Recharts, D3.js
Forms:            React Hook Form
Mobile:           React Native / Expo
```

### Data & Storage
```
Primary DB:       PostgreSQL 15+
Cache:            Redis 7+
Search:           Elasticsearch 8+ / Chroma
File Storage:     AWS S3 / GCS / MinIO
Time-Series:      InfluxDB
Message Queue:    RabbitMQ 3.12+ / Kafka

Connection Pool:  pgBouncer, PgPool
Backup:           pg_dump, AWS Backup
```

### DevOps & Infrastructure
```
Container:        Docker
Orchestration:    Kubernetes 1.28+
Helm:             Helm charts
IaC:              Terraform
CI/CD:            GitHub Actions / GitLab CI
Monitoring:       Prometheus, Grafana
Logging:          ELK Stack / Loki
Tracing:          Jaeger
```

---

## Data Flow Diagrams

### 1. Chat Flow (Query Processing)

```
User Input (Chat)
      │
      ▼
┌──────────────────┐
│ API Gateway      │ (Request Validation)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ Auth Verification│ (JWT validation)
└────────┬─────────┘
         │
         ▼
┌──────────────────────────┐
│ Coordinator Agent        │ (Route to appropriate agent)
└────────┬─────────────────┘
         │
    ┌────┴──────────┬──────────────┬─────────┐
    │               │              │         │
    ▼               ▼              ▼         ▼
┌─────────┐  ┌─────────┐  ┌─────────┐  ┌────────┐
│Academic │  │Career   │  │Study    │  │Resource│
│Advisor  │  │Agent    │  │Planner  │  │Agent   │
└────┬────┘  └────┬────┘  └────┬────┘  └───┬────┘
     │            │            │           │
     └────┬───────┴────────────┴──────┬────┘
          │                          │
          ▼                          ▼
     ┌──────────────┐        ┌──────────────┐
     │ RAG Retrieval│        │ Knowledge Base│ (Vector DB)
     └──────┬───────┘        └──────┬───────┘
            │                       │
            └─────────┬─────────────┘
                      │
                      ▼
            ┌────────────────────┐
            │ LLM Processing     │ (OpenAI/Gemini/etc)
            └────────┬───────────┘
                     │
                     ▼
            ┌────────────────────┐
            │ Response Formatting│
            └────────┬───────────┘
                     │
                     ▼
            ┌────────────────────┐
            │ Store in History   │ (PostgreSQL + Redis)
            └────────┬───────────┘
                     │
                     ▼
            Response to User (WebSocket/SSE)
```

### 2. Study Plan Generation Flow

```
User: "Create semester roadmap"
           │
           ▼
    ┌─────────────────────┐
    │ Parse Intent        │
    └──────────┬──────────┘
               │
               ▼
    ┌──────────────────────────┐
    │ Fetch Student Data       │ (DB: marks, attendance, goals)
    └──────────┬───────────────┘
               │
               ▼
    ┌──────────────────────────┐
    │ Study Planner Agent      │
    │ ├─ Analyze performance   │
    │ ├─ Predict challenges    │
    │ ├─ Suggest courses       │
    │ └─ Create milestones     │
    └──────────┬───────────────┘
               │
               ▼
    ┌──────────────────────────┐
    │ ML Prediction Service    │ (XGBoost, LightGBM)
    │ ├─ CGPA forecast         │
    │ ├─ Backlog risk          │
    │ └─ Course difficulty     │
    └──────────┬───────────────┘
               │
               ▼
    ┌──────────────────────────┐
    │ Resource Agent           │ (Find learning materials)
    └──────────┬───────────────┘
               │
               ▼
    ┌──────────────────────────┐
    │ Format Plan              │ (JSON structure)
    └──────────┬───────────────┘
               │
               ▼
    ┌──────────────────────────┐
    │ Store Study Plan         │ (PostgreSQL)
    └──────────┬───────────────┘
               │
               ▼
    ┌──────────────────────────┐
    │ Schedule Notifications   │ (Async tasks)
    └──────────┬───────────────┘
               │
               ▼
    Display Roadmap + Recommendations
```

### 3. Performance Prediction Pipeline

```
Scheduled Nightly Job (2 AM)
           │
           ▼
    ┌──────────────────────┐
    │ Fetch All Students   │ (from DB in batches)
    └──────────┬───────────┘
               │
               ▼
    ┌──────────────────────┐
    │ Extract Features     │ (last semester data)
    │ ├─ GPA              │
    │ ├─ Attendance       │
    │ ├─ Assignment marks │
    │ ├─ Study time       │
    │ └─ Course difficulty│
    └──────────┬───────────┘
               │
               ▼
    ┌──────────────────────┐
    │ ML Model Prediction  │
    │ ├─ CGPA Prediction   │ (XGBoost)
    │ ├─ Backlog Risk      │ (LightGBM)
    │ └─ Placement Risk    │ (Ensemble)
    └──────────┬───────────┘
               │
               ▼
    ┌──────────────────────┐
    │ Cache Predictions    │ (Redis)
    └──────────┬───────────┘
               │
               ▼
    ┌──────────────────────┐
    │ Generate Alerts      │ (At-risk students)
    └──────────┬───────────┘
               │
               ▼
    ┌──────────────────────┐
    │ Notification Agent   │ (Send alerts)
    └──────────┬───────────┘
               │
               ▼
    ┌──────────────────────┐
    │ Store Results        │ (analytics_predictions table)
    └──────────────────────┘
```

---

## Agent Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                 AGENT ORCHESTRATION LAYER                       │
│                     (LangGraph Framework)                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │ Coordinator Agent (Main Orchestrator)                  │   │
│  │ ┌────────────────────────────────────────────────────┐ │   │
│  │ │ State Machine:                                     │ │   │
│  │ │ • Observe: Parse user query                       │ │   │
│  │ │ • Think: Understand intent & context              │ │   │
│  │ │ • Plan: Determine agent sequence                  │ │   │
│  │ │ • Execute: Route to specific agents               │ │   │
│  │ │ • Reflect: Evaluate responses                     │ │   │
│  │ │ • Learn: Update knowledge base                    │ │   │
│  │ └────────────────────────────────────────────────────┘ │   │
│  └──────────────────┬────────────────────────────────────┬──┘  │
│                     │                                    │      │
│  ┌──────────────────▼──────────────────────────────────┐ │      │
│  │ AGENT POOL                                         │ │      │
│  │                                                    │ │      │
│  │ ┌─────────────────────────────────────────────┐  │ │      │
│  │ │ 1. Academic Advisor Agent                  │  │ │      │
│  │ │    Responsibilities:                        │  │ │      │
│  │ │    • Analyze academic performance           │  │ │      │
│  │ │    • Suggest courses & electives            │  │ │      │
│  │ │    • Provide guidance on difficult subjects │  │ │      │
│  │ │    • Recommend study strategies             │  │ │      │
│  │ │                                             │  │ │      │
│  │ │    Tools:                                   │  │ │      │
│  │ │    • get_student_profile()                  │  │ │      │
│  │ │    • get_course_recommendations()           │  │ │      │
│  │ │    • get_knowledge_base_chunks()            │  │ │      │
│  │ │    • call_llm()                             │  │ │      │
│  │ │                                             │  │ │      │
│  │ │    Memory:                                  │  │ │      │
│  │ │    • Student profile vector                 │  │ │      │
│  │ │    • Conversation history                   │  │ │      │
│  │ │    • Academic policies                      │  │ │      │
│  │ └─────────────────────────────────────────────┘  │ │      │
│  │                                                    │ │      │
│  │ ┌─────────────────────────────────────────────┐  │ │      │
│  │ │ 2. Study Planner Agent                     │  │ │      │
│  │ │    Responsibilities:                        │  │ │      │
│  │ │    • Create personalized study schedules    │  │ │      │
│  │ │    • Balance workload across subjects       │  │ │      │
│  │ │    • Generate exam prep timelines           │  │ │      │
│  │ │    • Track & adapt plans                    │  │ │      │
│  │ │                                             │  │ │      │
│  │ │    Tools:                                   │  │ │      │
│  │ │    • generate_study_schedule()              │  │ │      │
│  │ │    • get_assignment_deadlines()             │  │ │      │
│  │ │    • calculate_workload_balance()           │  │ │      │
│  │ │    • create_calendar_events()               │  │ │      │
│  │ │                                             │  │ │      │
│  │ │    Memory:                                  │  │ │      │
│  │ │    • Academic calendar                      │  │ │      │
│  │ │    • Student availability                   │  │ │      │
│  │ │    • Previous schedules                     │  │ │      │
│  │ └─────────────────────────────────────────────┘  │ │      │
│  │                                                    │ │      │
│  │ ┌─────────────────────────────────────────────┐  │ │      │
│  │ │ 3. Performance Agent                       │  │ │      │
│  │ │    Responsibilities:                        │  │ │      │
│  │ │    • Analyze marks & attendance trends      │  │ │      │
│  │ │    • Predict future performance             │  │ │      │
│  │ │    • Identify at-risk students              │  │ │      │
│  │ │    • Recommend interventions                │  │ │      │
│  │ │                                             │  │ │      │
│  │ │    Tools:                                   │  │ │      │
│  │ │    • get_performance_data()                 │  │ │      │
│  │ │    • predict_cgpa()                         │  │ │      │
│  │ │    • predict_backlog_risk()                 │  │ │      │
│  │ │    • generate_analytics_report()            │  │ │      │
│  │ │                                             │  │ │      │
│  │ │    Memory:                                  │  │ │      │
│  │ │    • Historical performance data             │  │ │      │
│  │ │    • ML model predictions                   │  │ │      │
│  │ │    • Threshold configurations               │  │ │      │
│  │ └─────────────────────────────────────────────┘  │ │      │
│  │                                                    │ │      │
│  │ ┌─────────────────────────────────────────────┐  │ │      │
│  │ │ 4. Career Agent                            │  │ │      │
│  │ │    Responsibilities:                        │  │ │      │
│  │ │    • Build career roadmaps                  │  │ │      │
│  │ │    • Match internships to interests         │  │ │      │
│  │ │    • Predict placement chances              │  │ │      │
│  │ │    • Guide resume building                  │  │ │      │
│  │ │                                             │  │ │      │
│  │ │    Tools:                                   │  │ │      │
│  │ │    • get_career_paths()                     │  │ │      │
│  │ │    • match_internships()                    │  │ │      │
│  │ │    • predict_placement()                    │  │ │      │
│  │ │    • generate_resume()                      │  │ │      │
│  │ │                                             │  │ │      │
│  │ │    Memory:                                  │  │ │      │
│  │ │    • Career paths database                  │  │ │      │
│  │ │    • Placement statistics                   │  │ │      │
│  │ │    • Resume templates                       │  │ │      │
│  │ └─────────────────────────────────────────────┘  │ │      │
│  │                                                    │ │      │
│  │ ┌─────────────────────────────────────────────┐  │ │      │
│  │ │ 5. Resource Agent                          │  │ │      │
│  │ │    Responsibilities:                        │  │ │      │
│  │ │    • Find learning resources                │  │ │      │
│  │ │    • Recommend courses & certifications     │  │ │      │
│  │ │    • Suggest peer study groups              │  │ │      │
│  │ │    • Index external knowledge               │  │ │      │
│  │ │                                             │  │ │      │
│  │ │    Tools:                                   │  │ │      │
│  │ │    • search_resources()                     │  │ │      │
│  │ │    • get_certifications()                   │  │ │      │
│  │ │    • find_study_groups()                    │  │ │      │
│  │ │    • rank_resources()                       │  │ │      │
│  │ │                                             │  │ │      │
│  │ │    Memory:                                  │  │ │      │
│  │ │    • Resource database                      │  │ │      │
│  │ │    • User ratings & feedback                │  │ │      │
│  │ │    • Resource relevance scores              │  │ │      │
│  │ └─────────────────────────────────────────────┘  │ │      │
│  │                                                    │ │      │
│  │ ┌─────────────────────────────────────────────┐  │ │      │
│  │ │ 6. Notification Agent                      │  │ │      │
│  │ │    Responsibilities:                        │  │ │      │
│  │ │    • Send alerts & reminders                │  │ │      │
│  │ │    • Manage notification preferences        │  │ │      │
│  │ │    • Track notification history             │  │ │      │
│  │ │    • Handle retry & failures                │  │ │      │
│  │ │                                             │  │ │      │
│  │ │    Tools:                                   │  │ │      │
│  │ │    • send_email()                           │  │ │      │
│  │ │    • send_sms()                             │  │ │      │
│  │ │    • send_push_notification()               │  │ │      │
│  │ │    • schedule_notification()                │  │ │      │
│  │ │                                             │  │ │      │
│  │ │    Memory:                                  │  │ │      │
│  │ │    • Notification templates                 │  │ │      │
│  │ │    • User preferences                       │  │ │      │
│  │ │    • Delivery history                       │  │ │      │
│  │ └─────────────────────────────────────────────┘  │ │      │
│  │                                                    │ │      │
│  │ ┌─────────────────────────────────────────────┐  │ │      │
│  │ │ 7. Resume Agent                            │  │ │      │
│  │ │    Responsibilities:                        │  │ │      │
│  │ │    • Build & improve resumes                │  │ │      │
│  │ │    • Suggest content & formatting           │  │ │      │
│  │ │    • Generate ATS-optimized versions        │  │ │      │
│  │ │    • Track resume versions                  │  │ │      │
│  │ │                                             │  │ │      │
│  │ │    Tools:                                   │  │ │      │
│  │ │    • generate_resume_content()              │  │ │      │
│  │ │    • validate_resume_ats()                  │  │ │      │
│  │ │    • export_resume()                        │  │ │      │
│  │ │    • get_resume_templates()                 │  │ │      │
│  │ │                                             │  │ │      │
│  │ │    Memory:                                  │  │ │      │
│  │ │    • Resume templates                       │  │ │      │
│  │ │    • Student achievements                   │  │ │      │
│  │ │    • Best practices                         │  │ │      │
│  │ └─────────────────────────────────────────────┘  │ │      │
│  │                                                    │ │      │
│  └────────────────────────────────────────────────────┘ │      │
│                                                         │      │
│  ┌─────────────────────────────────────────────────┐   │      │
│  │ SHARED MEMORY & KNOWLEDGE BASE                 │   │      │
│  │ ┌──────────────────────────────────────────┐   │   │      │
│  │ │ Vector Store (ChromaDB/Pinecone)        │   │   │      │
│  │ │ • Student profiles                       │   │   │      │
│  │ │ • Curriculum & syllabus                  │   │   │      │
│  │ │ • Academic policies                      │   │   │      │
│  │ │ • Career information                     │   │   │      │
│  │ │ • Course reviews & feedback              │   │   │      │
│  │ └──────────────────────────────────────────┘   │   │      │
│  │                                                 │   │      │
│  │ ┌──────────────────────────────────────────┐   │   │      │
│  │ │ Long-term Memory (PostgreSQL)            │   │   │      │
│  │ │ • Agent decisions & reasoning             │   │   │      │
│  │ │ • Conversation history                    │   │   │      │
│  │ │ • Student goals & progress                │   │   │      │
│  │ │ • Recommendations & feedback              │   │   │      │
│  │ └──────────────────────────────────────────┘   │   │      │
│  │                                                 │   │      │
│  │ ┌──────────────────────────────────────────┐   │   │      │
│  │ │ Short-term Memory (Redis Cache)          │   │   │      │
│  │ │ • Current conversation context            │   │   │      │
│  │ │ • Recent queries & responses              │   │   │      │
│  │ │ • Session state                           │   │   │      │
│  │ │ • Agent states                            │   │   │      │
│  │ └──────────────────────────────────────────┘   │   │      │
│  └─────────────────────────────────────────────────┘   │      │
│                                                         │      │
└─────────────────────────────────────────────────────────┴──────┘
```

### Agent Execution Cycle

Each agent follows the OPAL cycle:

```
┌─────────────────────────────────┐
│      OPAL EXECUTION CYCLE       │
├─────────────────────────────────┤
│                                 │
│ 1. OBSERVE                      │
│    • Parse user query           │
│    • Extract entities & intent  │
│    • Retrieve context from mem  │
│                                 │
│ 2. THINK                        │
│    • Analyze problem            │
│    • Identify constraints       │
│    • Consider options           │
│    • Update beliefs             │
│                                 │
│ 3. PLAN                         │
│    • Break into subtasks        │
│    • Order by dependency        │
│    • Assign weights & priority  │
│    • Prepare tools              │
│                                 │
│ 4. EXECUTE                      │
│    • Call external APIs         │
│    • Query databases            │
│    • Invoke LLM                 │
│    • Process results            │
│                                 │
│ 5. REFLECT                      │
│    • Evaluate outcomes          │
│    • Check for errors           │
│    • Validate results           │
│    • Store results              │
│                                 │
│ 6. LEARN                        │
│    • Update vector embeddings   │
│    • Store feedback             │
│    • Improve prompts            │
│    • Fine-tune decisions        │
│                                 │
└─────────────────────────────────┘
```

---

## Deployment Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                    AWS / Azure / GCP                             │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │ CDN (CloudFront / Cloudflare)                              │ │
│  │ • Static assets (JS, CSS, images)                          │ │
│  │ • Edge caching                                             │ │
│  └────────────────────┬───────────────────────────────────────┘ │
│                       │                                          │
│  ┌────────────────────▼───────────────────────────────────────┐ │
│  │ Load Balancer (ALB / NLB)                                  │ │
│  │ • SSL/TLS termination                                      │ │
│  │ • Traffic distribution                                     │ │
│  └────────┬─────────────────────────────────┬────────────────┘ │
│           │                                 │                  │
│  ┌────────▼──────────┐          ┌───────────▼──────────┐      │
│  │  API Tier 1       │          │   API Tier 2        │      │
│  │  (Kubernetes)     │          │   (Kubernetes)      │      │
│  │                   │          │                     │      │
│  │  ┌─────────────┐  │          │  ┌─────────────┐   │      │
│  │  │FastAPI Pod 1│  │          │  │FastAPI Pod 3│   │      │
│  │  └─────────────┘  │          │  └─────────────┘   │      │
│  │  ┌─────────────┐  │          │  ┌─────────────┐   │      │
│  │  │FastAPI Pod 2│  │          │  │FastAPI Pod 4│   │      │
│  │  └─────────────┘  │          │  └─────────────┘   │      │
│  │                   │          │                     │      │
│  │  ┌─────────────┐  │          │  ┌─────────────┐   │      │
│  │  │LLM Agent 1  │  │          │  │LLM Agent 2  │   │      │
│  │  └─────────────┘  │          │  └─────────────┘   │      │
│  └───────┬──────────┘          └────────┬────────────┘      │
│          │                              │                   │
│  ┌───────▼──────────────────────────────▼──────────┐        │
│  │        Kubernetes Service (Internal)            │        │
│  │        • Service Discovery                      │        │
│  │        • Load Balancing                         │        │
│  └───────┬──────────────────────────────┬──────────┘        │
│          │                              │                   │
│  ┌───────▼─────────────┐     ┌─────────▼──────────┐        │
│  │ PostgreSQL Primary  │     │ PostgreSQL Replica │        │
│  │ (Write)             │     │ (Read-only)        │        │
│  │ • Replication       │     │ • Backup           │        │
│  │ • WAL archiving     │     │ • Read scaling     │        │
│  └──────┬──────────────┘     └────────────────────┘        │
│         │                                                   │
│  ┌──────▼────────────────────────────────────────────┐     │
│  │ Redis Cluster (Cache + Session Store)            │     │
│  │ • Master-Slave replication                       │     │
│  │ • Persistence (RDB + AOF)                        │     │
│  │ • High availability                              │     │
│  └──────────────────────────────────────────────────┘     │
│                                                            │
│  ┌──────────────────────────────────────────────────┐     │
│  │ RabbitMQ / Kafka (Message Broker)               │     │
│  │ • Event streaming                                │     │
│  │ • Task queues                                    │     │
│  │ • Cluster mode                                   │     │
│  └──────────────────────────────────────────────────┘     │
│                                                            │
│  ┌──────────────────────────────────────────────────┐     │
│  │ Celery Workers (Background Jobs)                │     │
│  │ • ML predictions                                 │     │
│  │ • Bulk uploads                                   │     │
│  │ • Scheduled tasks                                │     │
│  │ • Notifications                                  │     │
│  └──────────────────────────────────────────────────┘     │
│                                                            │
│  ┌──────────────────────────────────────────────────┐     │
│  │ Storage                                           │     │
│  │ ┌────────────────┐  ┌──────────────────────┐    │     │
│  │ │AWS S3          │  │Elasticsearch / Chroma│    │     │
│  │ │• Documents     │  │• Document search     │    │     │
│  │ │• Backups       │  │• Vector indexing     │    │     │
│  │ │• File uploads  │  │• Analytics           │    │     │
│  │ └────────────────┘  └──────────────────────┘    │     │
│  └──────────────────────────────────────────────────┘     │
│                                                            │
│  ┌──────────────────────────────────────────────────┐     │
│  │ Monitoring & Logging                             │     │
│  │ ┌────────────┐  ┌──────────┐  ┌────────────┐   │     │
│  │ │Prometheus  │  │Grafana   │  │Loki/ELK    │   │     │
│  │ │• Metrics   │  │• Dashboards  │• Logs    │   │     │
│  │ │• Alerts    │  │• Alerts      │• Tracing │   │     │
│  │ └────────────┘  └──────────┘  └────────────┘   │     │
│  └──────────────────────────────────────────────────┘     │
│                                                            │
│  ┌──────────────────────────────────────────────────┐     │
│  │ Disaster Recovery                                │     │
│  │ • Cross-region failover                          │     │
│  │ • Automated backups (6-hourly)                   │     │
│  │ • Point-in-time recovery                         │     │
│  │ • Regular DR drills                              │     │
│  └──────────────────────────────────────────────────┘     │
│                                                            │
└──────────────────────────────────────────────────────────────────┘
```

---

## Summary

This architecture provides:

✅ **Scalability**: Horizontal scaling via Kubernetes  
✅ **Reliability**: Multi-region failover, automated backups  
✅ **Security**: End-to-end encryption, RBAC, audit logs  
✅ **Performance**: Caching, CDN, async processing  
✅ **AI/ML**: Multi-agent orchestration, RAG, predictions  
✅ **Maintainability**: Infrastructure-as-code, monitoring, logging  
✅ **Extensibility**: Modular services, open APIs  

