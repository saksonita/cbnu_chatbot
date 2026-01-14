# CBNU Chatbot Architecture

## System Overview

The CBNU Chatbot is a modern, scalable AI-powered assistant built with a microservices-like architecture. It follows a layered approach with clear separation of concerns.

```
┌─────────────────────────────────────────────────────────────┐
│                     Frontend (React/Vue)                     │
├─────────────────────────────────────────────────────────────┤
│                     REST API Gateway                         │
├─────────────────────────────────────────────────────────────┤
│  API Layer │ Core Logic │ Services │ Database │ Tools        │
├─────────────────────────────────────────────────────────────┤
│  PostgreSQL │ Vector DB │ Cache │ File Storage               │
└─────────────────────────────────────────────────────────────┘
```

## Components

### 1. Frontend Layer (`frontend/`)
- User interface built with React/Vue
- Real-time chat interface
- Student information dashboard
- Admin panel for knowledge base management

### 2. API Layer (`backend/app/api/`)
- FastAPI REST endpoints
- Request validation and error handling
- CORS middleware
- Authentication/Authorization

### 3. Core Logic Layer (`backend/app/core/`)
- **Agent**: Main orchestrator for conversation flow
- **Intent**: Intent classification and routing
- **Synthesis**: Response generation and formatting

### 4. Services Layer (`backend/app/services/`)
- **LLM Service**: OpenAI integration
- **Embedding Service**: Text-to-vector conversion
- **Translation Service**: Multi-language support

### 5. Tools Layer (`backend/app/tools/`)
- **Database Tool**: Query CBNU knowledge base
- **RAG Tool**: Retrieve relevant documents
- **Web Tool**: Fetch current information

### 6. Data Layer
- PostgreSQL: Structured data (users, conversations, metadata)
- Vector Database: Embeddings for semantic search
- File Storage: Documents, uploads, cache

## Data Flow

### Query Processing Pipeline

```
User Input
    ↓
API Endpoint (FastAPI)
    ↓
Input Validation (Schemas)
    ↓
Intent Classification (Intent Service)
    ↓
Context Retrieval (RAG, Database Tools)
    ↓
LLM Processing (OpenAI Service)
    ↓
Response Synthesis (Synthesis Layer)
    ↓
Format & Return (JSON Response)
    ↓
Frontend Display
```

## Key Architectural Decisions

### 1. Separation of Concerns
Each layer has a specific responsibility, making the system maintainable and testable.

### 2. Modular Services
Services are decoupled, allowing easy replacement or upgrade of components (e.g., switching LLM providers).

### 3. Data Pipelines
- ETL scripts in `scripts/` handle data ingestion and processing
- RAG system for context-aware responses
- Vector embeddings for semantic understanding

### 4. Containerization
Docker containers ensure consistent deployment across environments.

## Technology Stack

### Backend
- **Framework**: FastAPI
- **Language**: Python 3.10+
- **Database**: PostgreSQL
- **Vector DB**: ChromaDB
- **Embedding Model**: BGE-M3
- **Web Search**: DuckDuckGo API
- **LLM**: OpenAI GPT-4
- **Async**: AsyncIO, Uvicorn

### Frontend
- **Framework**: React 18+
- **State Management**: Redux/Zustand
- **Styling**: Tailwind CSS
- **HTTP**: Axios/React Query

### Infrastructure
- **Containerization**: Docker & Docker Compose
- **Orchestration**: Kubernetes (optional for production)
- **CI/CD**: GitHub Actions

## Technology Stack

| Layer | Component | Technology |
|-------|-----------|-------------|
| **Front End** | Web UI | To be specified |
| **Back End** | API Framework | FastAPI |
| | Tool Routing | Function Calling |
| **Data Layer** | Structured Database | PostgreSQL |
| | Vector Database | Chroma DB |
| **RAG Pipeline** | Embedding Model | bge-m3 |
| | Retrieval | Vector similarity search |
| **Web Search Tool** | Search Engine | DuckDuckGo (duckduckgo_search) |
| | Content Fetching | HTTP fetch + text extraction |
| **Infrastructure** | Containerization | Docker |
| | Configuration | Environment variables (.env) |
| **Foundation Model** | LLM Answer Generation | OpenAI |

## Scalability Considerations

### Horizontal Scaling
- Stateless API servers behind load balancer
- Database replication
- Message queue for long-running tasks

### Caching Strategy
- Redis for session and query caching
- Vector DB caching for embeddings
- HTTP caching headers

### Monitoring
- Application logging (Sentry/CloudWatch)
- Performance monitoring (Datadog/New Relic)
- Database query optimization

## Security

### Authentication
- JWT tokens for API authentication
- Session management for frontend
- Rate limiting on endpoints

### Data Protection
- HTTPS/TLS encryption
- Database encryption at rest
- API key rotation
- Input sanitization and validation

## Future Enhancements

1. **Multi-language Support**: Extend RAG for Korean, English, Chinese, etc.
2. **Advanced Analytics**: Track user interactions and improve response quality
3. **Integration APIs**: Connect with CBNU's existing information systems
4. **Mobile App**: Native mobile applications
5. **Voice Interface**: Speech-to-text and text-to-speech capabilities
