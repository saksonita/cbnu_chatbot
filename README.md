# CBNU Chatbot - Foreign Students OIA

A comprehensive AI-powered chatbot system designed to assist foreign students at CBNU with university information and guidance.

## Project Structure

```
chatbot-oia/
├── frontend/                  # User Interface Layer
├── backend/                   # Application Logic Layer
├── data/                      # Data Pipeline
├── scripts/                   # Automation & Operations
├── docker/                    # Containerization
└── docs/                      # Documentation
```

## Quick Start

### Prerequisites
- Python 3.9+
- Docker & Docker Compose
- PostgreSQL
- OpenAI API Key

### Installation

1. Clone the repository
```bash
git clone <repository_url>
cd cbnu_chatbot
```

2. Create environment configuration
```bash
cp .env.example .env
# Update .env with your configuration
```

3. Install Python dependencies (Backend)
```bash
cd backend
pip install -r requirements.txt
```

4. Run with Docker
```bash
docker-compose up -d
```

## Architecture

### Backend
- **API Layer** (`backend/app/api/`) - HTTP endpoints and middleware
- **Core Logic** (`backend/app/core/`) - Agent, Intent processing, Response synthesis
- **Tools** (`backend/app/tools/`) - Database, RAG, Web data interfaces
- **Services** (`backend/app/services/`) - LLM, Embeddings, Translation
- **Database** (`backend/app/database/`) - Models and connections
- **Schemas** (`backend/app/schemas/`) - Data validation

### Data Pipeline
- **Raw** - Source documents (PDFs, HTML, CSV)
- **Processed** - Cleaned and chunked data
- **Embeddings** - Vector database storage

## Documentation

See [docs/](docs/) for detailed documentation:
- [Architecture](docs/architecture.md)
- [API Specification](docs/api_specification.md)
- [Deployment Guide](docs/deployment_guide.md)

## Development

### Running Tests
```bash
cd backend
pytest tests/
```

### Running the Backend Server
```bash
cd backend
uvicorn app.api.main:app --reload
```

## Deployment

See [docs/deployment_guide.md](docs/deployment_guide.md) for production deployment instructions.

## Contributing

Please read our contributing guidelines and submit pull requests to the main branch.

## License

This project is licensed under the MIT License - see LICENSE file for details.
