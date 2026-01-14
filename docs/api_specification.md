# CBNU Chatbot API Specification

## Base URL
```
http://localhost:8000/api/v1
```

## Authentication
All requests must include an API key in the header:
```
Authorization: Bearer YOUR_API_KEY
```

## Endpoints

### Chat Endpoints

#### POST /chat/message
Send a message to the chatbot.

**Request:**
```json
{
  "message": "What are the visa requirements for international students?",
  "conversation_id": "conv_12345",
  "language": "en"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "message_id": "msg_67890",
    "response": "International students typically need...",
    "sources": [
      {
        "title": "Visa Requirements",
        "url": "docs/visa.pdf",
        "relevance": 0.95
      }
    ],
    "conversation_id": "conv_12345"
  }
}
```

#### GET /chat/conversation/{conversation_id}
Retrieve conversation history.

**Response:**
```json
{
  "success": true,
  "data": {
    "conversation_id": "conv_12345",
    "messages": [
      {
        "id": "msg_1",
        "role": "user",
        "content": "Hello",
        "timestamp": "2024-01-14T10:30:00Z"
      },
      {
        "id": "msg_2",
        "role": "assistant",
        "content": "Hello! How can I help?",
        "timestamp": "2024-01-14T10:30:05Z"
      }
    ]
  }
}
```

### Knowledge Base Endpoints

#### GET /knowledge/search
Search the knowledge base.

**Query Parameters:**
- `q`: Search query (required)
- `limit`: Number of results (default: 10)
- `language`: Language code (default: en)

**Response:**
```json
{
  "success": true,
  "data": {
    "results": [
      {
        "id": "doc_1",
        "title": "Tuition Fees",
        "content": "...",
        "relevance": 0.92
      }
    ],
    "total": 5
  }
}
```

#### POST /knowledge/ingest
Upload new documents to knowledge base.

**Request:**
```
Content-Type: multipart/form-data

file: <PDF/Document>
category: "tuition"
language: "en"
```

**Response:**
```json
{
  "success": true,
  "data": {
    "document_id": "doc_123",
    "status": "processing",
    "message": "Document queued for processing"
  }
}
```

### User Endpoints

#### POST /users/register
Register a new user.

**Request:**
```json
{
  "email": "student@example.com",
  "password": "secure_password",
  "name": "John Doe",
  "student_id": "2024001",
  "country": "China"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "user_id": "user_123",
    "email": "student@example.com",
    "token": "jwt_token_here"
  }
}
```

#### POST /users/login
User login.

**Request:**
```json
{
  "email": "student@example.com",
  "password": "secure_password"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "user_id": "user_123",
    "token": "jwt_token_here"
  }
}
```

### Admin Endpoints

#### GET /admin/conversations
Get all conversations (admin only).

**Query Parameters:**
- `skip`: Pagination offset
- `limit`: Number of results
- `date_from`: Filter by start date
- `date_to`: Filter by end date

**Response:**
```json
{
  "success": true,
  "data": {
    "conversations": [
      {
        "id": "conv_123",
        "user_id": "user_456",
        "message_count": 5,
        "created_at": "2024-01-14T10:00:00Z"
      }
    ],
    "total": 42,
    "skip": 0,
    "limit": 20
  }
}
```

#### PUT /admin/knowledge/{document_id}
Update knowledge base document.

**Request:**
```json
{
  "title": "Updated Title",
  "content": "Updated content...",
  "category": "academic"
}
```

## Error Responses

### 400 Bad Request
```json
{
  "success": false,
  "error": {
    "code": "INVALID_INPUT",
    "message": "Invalid message format"
  }
}
```

### 401 Unauthorized
```json
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid or missing API key"
  }
}
```

### 429 Too Many Requests
```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Rate limit exceeded. Try again in 60 seconds"
  }
}
```

### 500 Internal Server Error
```json
{
  "success": false,
  "error": {
    "code": "INTERNAL_ERROR",
    "message": "An unexpected error occurred"
  }
}
```

## Rate Limiting

- Standard: 100 requests/minute per user
- Premium: 1000 requests/minute per user
- Admin: Unlimited

Rate limit headers:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1642273800
```

## Versioning

API version specified in URL path: `/api/v1/`

Future versions will maintain backward compatibility with deprecation notices.
