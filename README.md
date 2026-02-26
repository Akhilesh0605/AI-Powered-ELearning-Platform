# AI-Powered Adaptive E-Learning Platform

A production-grade, AI-driven adaptive learning system that dynamically generates structured course modules, quizzes, and reinforcement content. The platform leverages Large Language Models (LLMs) with Retrieval-Augmented Generation (RAG) to deliver personalized learning experiences that adapt to individual student performance.

---

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Folder Structure](#folder-structure)
- [Tech Stack](#tech-stack)
- [Installation](#installation)
- [Environment Variables](#environment-variables)
- [API Documentation](#api-documentation)
- [Adaptive Learning Engine](#adaptive-learning-engine)
- [Database Schema](#database-schema)
- [AI Pipeline](#ai-pipeline)
- [Future Improvements](#future-improvements)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

This platform addresses the limitations of traditional one-size-fits-all e-learning by implementing an intelligent adaptive learning system. The core intelligence resides in a dedicated AI microservice that handles:

- **Module Generation**: Automatically structures course content from standard textbooks using RAG
- **Quiz Generation**: Creates assessments with concept-level tagging
- **Adaptive Reinforcement**: Generates practice problems and visual explanations based on learner performance
- **Mastery Scoring**: Tracks concept-level understanding to personalize the learning path

### Core Features

| Feature | Description |
|---------|-------------|
| Structured Course Generation | Extracts and organizes content from textbooks via RAG pipeline |
| Concept-Level Tagging | Tags modules and quiz questions with specific concepts for granular tracking |
| Adaptive Quiz Engine | Adjusts difficulty and focus based on learner performance |
| Concept Mastery Tracking | Maintains per-concept mastery scores for each learner |
| Dynamic Reinforcement | Generates practice problems and simplified explanations for weak areas |
| Performance Analytics | Visual dashboard for tracking progress and identifying gaps |
| Multi-Subject Architecture | Scalable design supporting multiple subjects and courses |

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              SYSTEM ARCHITECTURE                            │
└─────────────────────────────────────────────────────────────────────────────┘

┌──────────────┐       ┌──────────────────────┐       ┌────────────────────────┐
│              │       │                      │       │                        │
│   Frontend   │◄─────►│   Backend (FastAPI)  │◄─────►│   AI Microservice      │
│   (React)    │       │                      │       │   (FastAPI)            │
│              │       │   - Auth (OAuth)     │       │                        │
│ - Tailwind   │       │   - REST API         │       │   - Groq LLM API       │
│ - Chart.js   │       │   - Business Logic   │       │   - RAG Pipeline       │
│ - Recharts   │       │   - Data Validation  │       │   - Adaptive Engine    │
│              │       │                      │       │   - Embeddings         │
└──────────────┘       └──────────┬───────────┘       └───────────┬────────────┘
                                  │                               │
                                  │                               │
                       ┌──────────▼───────────┐       ┌───────────▼────────────┐
                       │                      │       │                        │
                       │   PostgreSQL         │       │   Vector Database      │
                       │                      │       │   (FAISS / Chroma)     │
                       │   - Users            │       │                        │
                       │   - Courses          │       │   - Document Chunks    │
                       │   - Progress         │       │   - Embeddings         │
                       │   - Quiz Results     │       │   - Semantic Index     │
                       │                      │       │                        │
                       └──────────────────────┘       └────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                              REQUEST FLOW                                   │
└─────────────────────────────────────────────────────────────────────────────┘

  User Request                                                    Response
       │                                                              ▲
       ▼                                                              │
  ┌─────────┐    HTTP/JSON    ┌─────────┐    HTTP/JSON    ┌─────────┐│
  │Frontend │ ───────────────►│ Backend │ ───────────────►│   AI    ││
  │         │                 │         │                 │ Service ││
  │         │◄────────────────│         │◄────────────────│         ││
  └─────────┘                 └─────────┘                 └─────────┘│
                                                                     │
                                                              ───────┘
```

---

## Folder Structure

```
ai-adaptive-elearning/
│
├── frontend/                    # React frontend application
│   ├── public/
│   ├── src/
│   │   ├── components/          # Reusable UI components
│   │   │   ├── common/          # Buttons, inputs, modals
│   │   │   ├── course/          # Course-related components
│   │   │   ├── quiz/            # Quiz interface components
│   │   │   └── analytics/       # Dashboard and charts
│   │   ├── pages/               # Route-level page components
│   │   ├── hooks/               # Custom React hooks
│   │   ├── services/            # API client services
│   │   ├── context/             # React context providers
│   │   ├── utils/               # Utility functions
│   │   └── styles/              # Global styles and Tailwind config
│   ├── package.json
│   └── tailwind.config.js
│
├── backend/                     # FastAPI backend service
│   ├── app/
│   │   ├── api/
│   │   │   ├── v1/
│   │   │   │   ├── endpoints/   # API route handlers
│   │   │   │   └── router.py    # API router configuration
│   │   ├── core/
│   │   │   ├── config.py        # Application configuration
│   │   │   ├── security.py      # OAuth and auth utilities
│   │   │   └── database.py      # Database connection
│   │   ├── models/              # SQLAlchemy ORM models
│   │   ├── schemas/             # Pydantic schemas
│   │   ├── services/            # Business logic layer
│   │   └── main.py              # Application entry point
│   ├── alembic/                 # Database migrations
│   ├── tests/
│   └── requirements.txt
│
├── ai-microservice/             # AI/ML microservice
│   ├── app/
│   │   ├── api/
│   │   │   └── endpoints/       # AI service endpoints
│   │   ├── core/
│   │   │   ├── config.py        # AI service configuration
│   │   │   └── llm_client.py    # LLM API wrapper
│   │   ├── services/
│   │   │   ├── rag/             # RAG pipeline components
│   │   │   │   ├── embeddings.py
│   │   │   │   ├── retriever.py
│   │   │   │   └── generator.py
│   │   │   ├── adaptive/        # Adaptive learning engine
│   │   │   │   ├── mastery.py
│   │   │   │   └── reinforcement.py
│   │   │   ├── quiz_generator.py
│   │   │   └── module_generator.py
│   │   ├── models/              # Data models
│   │   └── main.py
│   ├── data/
│   │   ├── textbooks/           # Source textbook PDFs
│   │   └── vector_store/        # Persisted vector database
│   ├── tests/
│   └── requirements.txt
│
├── docker-compose.yml           # Container orchestration
├── .env.example                 # Environment variables template
├── pyproject.toml
├── main.py
└── README.md
```

---

## Tech Stack

### Frontend
| Technology | Purpose |
|------------|---------|
| React 18+ | UI framework |
| Tailwind CSS | Utility-first styling |
| Chart.js / Recharts | Performance analytics visualization |
| Axios | HTTP client |
| React Router | Client-side routing |

### Backend
| Technology | Purpose |
|------------|---------|
| Python 3.12+ | Runtime |
| FastAPI | Web framework |
| SQLAlchemy | ORM |
| Alembic | Database migrations |
| OAuth 2.0 | Authentication |
| Pydantic | Data validation |

### AI Microservice
| Technology | Purpose |
|------------|---------|
| Python 3.12+ | Runtime |
| FastAPI | Web framework |
| Groq API | LLM inference (prototype) |
| LangChain | LLM orchestration |
| FAISS / Chroma | Vector database |
| Sentence Transformers | Embedding generation |

### Database
| Technology | Purpose |
|------------|---------|
| PostgreSQL | Structured data storage |
| FAISS / Chroma | Vector similarity search |

---

## Installation

### Prerequisites

- Python 3.12+
- Node.js 18+
- PostgreSQL 14+
- Git

### 1. Clone the Repository

```bash
git clone https://github.com/your-org/ai-adaptive-elearning.git
cd ai-adaptive-elearning
```

### 2. Frontend Setup

```bash
cd frontend

# Install dependencies
npm install

# Start development server
npm run dev
```

The frontend will be available at `http://localhost:5173`

### 3. Backend Setup

```bash
cd backend

# Create virtual environment
python -m venv venv

# Activate virtual environment
# Windows
.\venv\Scripts\activate
# macOS/Linux
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Run database migrations
alembic upgrade head

# Start the server
uvicorn app.main:app --reload --port 8000
```

The backend API will be available at `http://localhost:8000`

### 4. AI Microservice Setup

```bash
cd ai-microservice

# Create virtual environment
python -m venv venv

# Activate virtual environment
# Windows
.\venv\Scripts\activate
# macOS/Linux
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Initialize vector store (first time only)
python -m app.scripts.init_vector_store

# Start the server
uvicorn app.main:app --reload --port 8001
```

The AI microservice will be available at `http://localhost:8001`

### 5. Docker Setup (Alternative)

```bash
# Build and start all services
docker-compose up --build

# Run in background
docker-compose up -d
```

---

## Environment Variables

Create a `.env` file in each service directory based on the provided `.env.example`:

### Backend `.env`

```env
# Application
APP_NAME=AI-Adaptive-ELearning
APP_ENV=development
DEBUG=true

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/elearning_db

# Authentication
SECRET_KEY=your-secret-key-here
OAUTH_CLIENT_ID=your-oauth-client-id
OAUTH_CLIENT_SECRET=your-oauth-client-secret

# AI Microservice
AI_SERVICE_URL=http://localhost:8001

# CORS
ALLOWED_ORIGINS=http://localhost:5173,http://localhost:3000
```

### AI Microservice `.env`

```env
# Application
APP_NAME=AI-Microservice
APP_ENV=development
DEBUG=true

# LLM Configuration
GROQ_API_KEY=your-groq-api-key
LLM_MODEL=llama-3.1-70b-versatile
LLM_TEMPERATURE=0.7
LLM_MAX_TOKENS=4096

# Embedding Model
EMBEDDING_MODEL=all-MiniLM-L6-v2

# Vector Database
VECTOR_DB_TYPE=faiss
VECTOR_DB_PATH=./data/vector_store

# RAG Configuration
RAG_CHUNK_SIZE=512
RAG_CHUNK_OVERLAP=50
RAG_TOP_K=5
```

### Frontend `.env`

```env
VITE_API_BASE_URL=http://localhost:8000/api/v1
VITE_APP_NAME=AI Adaptive Learning
```

---

## API Documentation

### Backend API Endpoints

#### Authentication

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/auth/register` | Register new user |
| POST | `/api/v1/auth/login` | User login |
| POST | `/api/v1/auth/refresh` | Refresh access token |
| POST | `/api/v1/auth/logout` | User logout |

#### Courses

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/courses` | List all courses |
| GET | `/api/v1/courses/{id}` | Get course details |
| POST | `/api/v1/courses` | Create new course |
| GET | `/api/v1/courses/{id}/modules` | Get course modules |

#### Modules

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/modules/{id}` | Get module content |
| POST | `/api/v1/modules/{id}/complete` | Mark module complete |
| GET | `/api/v1/modules/{id}/quiz` | Get module quiz |

#### Quiz

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/quiz/{id}/submit` | Submit quiz answers |
| GET | `/api/v1/quiz/{id}/results` | Get quiz results |

#### Progress & Analytics

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/users/me/progress` | Get user progress |
| GET | `/api/v1/users/me/mastery` | Get concept mastery scores |
| GET | `/api/v1/users/me/analytics` | Get performance analytics |

#### Reinforcement

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/reinforcement/{concept_id}` | Get reinforcement content |
| GET | `/api/v1/reinforcement/{concept_id}/practice` | Get practice problems |
| GET | `/api/v1/reinforcement/{concept_id}/visual` | Get visual explanations |

### AI Microservice Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/generate/module` | Generate module content |
| POST | `/api/v1/generate/quiz` | Generate quiz questions |
| POST | `/api/v1/generate/practice` | Generate practice problems |
| POST | `/api/v1/generate/explanation` | Generate visual explanation |
| POST | `/api/v1/analyze/mastery` | Calculate mastery score |
| GET | `/api/v1/health` | Service health check |

### Example Request/Response

**Generate Quiz Questions**

```bash
POST /api/v1/generate/quiz
Content-Type: application/json

{
  "module_id": "mod_123",
  "concepts": ["derivatives", "chain_rule", "product_rule"],
  "difficulty": "intermediate",
  "num_questions": 5
}
```

```json
{
  "quiz_id": "quiz_456",
  "questions": [
    {
      "id": "q_1",
      "type": "multiple_choice",
      "concept": "chain_rule",
      "difficulty": "intermediate",
      "question": "Find the derivative of f(x) = sin(x²)",
      "options": [
        "2x·cos(x²)",
        "cos(x²)",
        "2x·sin(x²)",
        "-2x·cos(x²)"
      ],
      "correct_answer": 0,
      "explanation": "Using the chain rule: f'(x) = cos(x²) · 2x = 2x·cos(x²)"
    }
  ]
}
```

---

## Adaptive Learning Engine

The adaptive learning engine is the core intelligence that personalizes the learning experience based on individual student performance.

### Workflow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ADAPTIVE LEARNING FLOW                              │
└─────────────────────────────────────────────────────────────────────────────┘

    ┌──────────┐
    │  Start   │
    │  Module  │
    └────┬─────┘
         │
         ▼
    ┌──────────┐
    │ Complete │
    │  Module  │
    └────┬─────┘
         │
         ▼
    ┌──────────┐
    │   Take   │
    │   Quiz   │
    └────┬─────┘
         │
         ▼
    ┌──────────────────┐
    │ Calculate        │
    │ Concept Mastery  │
    │ Scores           │
    └────────┬─────────┘
             │
             ▼
    ┌────────────────────┐
    │ Mastery ≥ 70%?     │
    └────────┬───────────┘
             │
      ┌──────┴──────┐
      │             │
      ▼             ▼
    [YES]         [NO]
      │             │
      ▼             ▼
┌──────────┐  ┌──────────────────┐
│  Unlock  │  │ Generate         │
│   Next   │  │ Reinforcement    │
│  Module  │  │ Content          │
└──────────┘  └────────┬─────────┘
                       │
              ┌────────┴────────┐
              │                 │
              ▼                 ▼
        ┌──────────┐     ┌──────────────┐
        │ Practice │     │   Visual     │
        │ Problems │     │ Explanations │
        └────┬─────┘     └──────┬───────┘
             │                  │
             └────────┬─────────┘
                      │
                      ▼
              ┌──────────────┐
              │   Retake     │
              │   Quiz       │
              └──────────────┘
```

### Mastery Calculation

The mastery score for each concept is calculated using a weighted formula:

```python
mastery_score = (
    correct_answers / total_questions * 0.6 +    # Accuracy weight
    response_confidence * 0.2 +                   # Confidence factor
    improvement_rate * 0.2                        # Learning trajectory
)
```

### Thresholds

| Mastery Level | Score Range | Action |
|---------------|-------------|--------|
| Expert | 90-100% | Unlock advanced content |
| Proficient | 70-89% | Proceed to next module |
| Developing | 50-69% | Generate targeted practice |
| Needs Support | 0-49% | Generate comprehensive reinforcement |

---

## Database Schema

### Entity Relationship Diagram

```
┌──────────────────┐       ┌──────────────────┐       ┌──────────────────┐
│      users       │       │     courses      │       │     modules      │
├──────────────────┤       ├──────────────────┤       ├──────────────────┤
│ id (PK)          │       │ id (PK)          │       │ id (PK)          │
│ email            │       │ title            │       │ course_id (FK)   │
│ password_hash    │       │ description      │       │ title            │
│ full_name        │       │ subject          │       │ content          │
│ created_at       │       │ difficulty       │       │ order_index      │
│ updated_at       │       │ created_at       │       │ concepts[]       │
└────────┬─────────┘       └────────┬─────────┘       └────────┬─────────┘
         │                          │                          │
         │    ┌─────────────────────┘                          │
         │    │                                                │
         ▼    ▼                                                ▼
┌──────────────────┐                                 ┌──────────────────┐
│  enrollments     │                                 │     quizzes      │
├──────────────────┤                                 ├──────────────────┤
│ id (PK)          │                                 │ id (PK)          │
│ user_id (FK)     │                                 │ module_id (FK)   │
│ course_id (FK)   │                                 │ questions (JSON) │
│ enrolled_at      │                                 │ created_at       │
│ status           │                                 └────────┬─────────┘
└────────┬─────────┘                                          │
         │                                                    │
         │                                                    ▼
         │                                          ┌──────────────────┐
         │                                          │  quiz_attempts   │
         │                                          ├──────────────────┤
         │                                          │ id (PK)          │
         │                                          │ user_id (FK)     │
         │                                          │ quiz_id (FK)     │
         │                                          │ answers (JSON)   │
         │                                          │ score            │
         │                                          │ completed_at     │
         │                                          └────────┬─────────┘
         │                                                   │
         ▼                                                   ▼
┌────────────────────────────────────────────────────────────────────────┐
│                          concept_mastery                               │
├────────────────────────────────────────────────────────────────────────┤
│ id (PK)                                                                │
│ user_id (FK)                                                           │
│ concept_name                                                           │
│ mastery_score                                                          │
│ attempts_count                                                         │
│ last_assessed_at                                                       │
│ updated_at                                                             │
└────────────────────────────────────────────────────────────────────────┘
```

### Key Tables

| Table | Description |
|-------|-------------|
| `users` | User accounts and authentication data |
| `courses` | Course metadata and configuration |
| `modules` | Individual learning modules within courses |
| `quizzes` | Quiz questions and metadata |
| `quiz_attempts` | User quiz submissions and scores |
| `concept_mastery` | Per-user, per-concept mastery tracking |
| `reinforcement_content` | Cached generated reinforcement materials |

---

## AI Pipeline

### RAG Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         RAG PIPELINE ARCHITECTURE                           │
└─────────────────────────────────────────────────────────────────────────────┘

  INGESTION PHASE                           RETRIEVAL PHASE
  ───────────────                           ────────────────

  ┌──────────────┐                          ┌──────────────┐
  │   Textbook   │                          │  User Query  │
  │     PDFs     │                          │              │
  └──────┬───────┘                          └──────┬───────┘
         │                                         │
         ▼                                         ▼
  ┌──────────────┐                          ┌──────────────┐
  │    Parse     │                          │   Embed      │
  │   Document   │                          │   Query      │
  └──────┬───────┘                          └──────┬───────┘
         │                                         │
         ▼                                         ▼
  ┌──────────────┐                          ┌──────────────┐
  │    Chunk     │                          │   Vector     │
  │    Text      │                          │   Search     │
  │ (512 tokens) │                          │              │
  └──────┬───────┘                          └──────┬───────┘
         │                                         │
         ▼                                         ▼
  ┌──────────────┐                          ┌──────────────┐
  │   Generate   │                          │  Retrieve    │
  │  Embeddings  │                          │  Top-K Docs  │
  └──────┬───────┘                          └──────┬───────┘
         │                                         │
         ▼                                         ▼
  ┌──────────────┐                          ┌──────────────┐
  │    Store     │─────────────────────────►│   Context    │
  │   Vectors    │                          │  Augmented   │
  │   (FAISS)    │                          │   Prompt     │
  └──────────────┘                          └──────┬───────┘
                                                   │
                                                   ▼
                                            ┌──────────────┐
                                            │   LLM        │
                                            │  Generation  │
                                            │   (Groq)     │
                                            └──────┬───────┘
                                                   │
                                                   ▼
                                            ┌──────────────┐
                                            │   Response   │
                                            └──────────────┘
```

### Pipeline Components

#### 1. Document Ingestion
- PDF parsing using PyMuPDF or PDFPlumber
- Text chunking with configurable overlap (512 tokens, 50 token overlap)
- Metadata extraction (chapter, section, page numbers)

#### 2. Embedding Generation
- Model: `all-MiniLM-L6-v2` (384 dimensions)
- Batch processing for efficiency
- Normalized embeddings for cosine similarity

#### 3. Vector Storage
- FAISS index for production (IVF-PQ for large datasets)
- Chroma for development and prototyping
- Metadata filtering support

#### 4. Retrieval
- Semantic similarity search
- Top-K retrieval (default K=5)
- Reranking for improved relevance

#### 5. Generation
- Groq API with Llama 3.1 70B
- Structured output parsing
- Temperature tuning per task type

### Content Generation Prompts

The system uses specialized prompts for different generation tasks:

| Task | Temperature | Max Tokens |
|------|-------------|------------|
| Module Generation | 0.7 | 4096 |
| Quiz Generation | 0.5 | 2048 |
| Practice Problems | 0.6 | 1024 |
| Visual Explanations | 0.8 | 2048 |

---

## Future Improvements

### Short-term (v1.x)
- [ ] Add support for multiple LLM providers (OpenAI, Anthropic, local models)
- [ ] Implement collaborative learning features
- [ ] Add gamification elements (badges, streaks, leaderboards)
- [ ] Mobile-responsive design improvements
- [ ] Offline mode with service workers

### Medium-term (v2.x)
- [ ] Multi-modal content support (video, interactive simulations)
- [ ] Voice-based learning interface
- [ ] Peer tutoring matching system
- [ ] Advanced analytics with learning predictions
- [ ] A/B testing framework for content optimization

### Long-term (v3.x)
- [ ] Federated learning for privacy-preserving personalization
- [ ] AR/VR learning experiences
- [ ] Integration with institutional LMS platforms
- [ ] Real-time collaborative problem solving
- [ ] AI teaching assistant chatbot

---

## Contributing

We welcome contributions from the community. Please follow these guidelines:

### Getting Started

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Make your changes
4. Run tests: `pytest`
5. Commit with conventional commits: `git commit -m "feat: add new feature"`
6. Push to your fork: `git push origin feature/your-feature-name`
7. Open a Pull Request

### Code Standards

- **Python**: Follow PEP 8, use type hints, docstrings for public functions
- **TypeScript/React**: Follow ESLint configuration, use functional components
- **Commits**: Use [Conventional Commits](https://www.conventionalcommits.org/)
- **Documentation**: Update README and docstrings for new features

### Pull Request Process

1. Update documentation for any changed functionality
2. Add tests for new features
3. Ensure all CI checks pass
4. Request review from maintainers
5. Address review feedback promptly

### Development Setup

```bash
# Install development dependencies
pip install -r requirements-dev.txt

# Run linting
ruff check .

# Run type checking
mypy .

# Run tests with coverage
pytest --cov=app tests/
```

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

```
MIT License

Copyright (c) 2026 AI Adaptive E-Learning Platform

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

<p align="center">
  <strong>Built with AI for the future of education</strong>
</p>
