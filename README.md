# AI Agent API

A production-ready AI Agent API with advanced RAG (Retrieval-Augmented Generation) capabilities, built with Node.js, TypeScript, MongoDB Atlas, Redis, and Google Gemini integration. This API provides intelligent question-answering based on trained knowledge from various sources including documents, websites, YouTube videos, audio, and video files.

## 🚀 Features

### Core Functionality
- **Multi-Source Training**: Support for documents (PDF, DOCX, TXT, CSV, MD), websites, YouTube videos, audio files (MP3, WAV, M4A, AAC, OGG, FLAC), and video files (MP4, WebM, MOV, AVI, MKV)
- **Advanced RAG System**: Hybrid vector and keyword search with cosine similarity scoring
- **Content Deduplication**: SHA256 hash-based deduplication to prevent duplicate training data
- **Agent Metadata Integration**: Query external agents database for context-aware responses
- **Intelligent Chunking**: Smart text chunking with overlap preservation and metadata tracking
- **Real-time Processing**: Asynchronous job processing with progress tracking
- **Comprehensive Analytics**: Detailed usage analytics, source performance tracking, and business insights
- **Response Auditing**: AI-powered hallucination detection and response quality assessment
- **User Feedback System**: Collect and analyze user feedback for continuous improvement
- **Caching Layer**: Redis-based caching for improved performance
- **Watchdog Service**: Automated response monitoring and quality assurance

### Advanced RAG Features
- **Hybrid Search Strategy**: Combines vector similarity (semantic) with keyword matching (lexical)
- **Dynamic Retrieval Configuration**: Configurable K-values, similarity thresholds, and confidence scores
- **Context-Aware Responses**: Agent metadata integration for personalized responses
- **Source Attribution**: Detailed source tracking with confidence scores and similarity metrics
- **Fallback Mechanisms**: Graceful degradation when no relevant content is found
- **Content Versioning**: Track content updates and maintain historical versions

### Security Features
- **Input Sanitization**: Comprehensive input validation and sanitization
- **File Upload Security**: Whitelist-based file type validation with MIME type checking
- **Rate Limiting**: Configurable rate limits per endpoint
- **CORS Protection**: Configurable CORS policies for production environments
- **Helmet Security**: Comprehensive security headers
- **Session Management**: Secure session handling with MongoDB store
- **API Token Authentication**: Bearer token-based authentication
- **Request Size Limits**: Configurable request size limits to prevent abuse

### Performance & Monitoring
- **Sentry Integration**: Error monitoring and performance tracking
- **Compression**: Response compression for improved bandwidth usage
- **Database Optimization**: MongoDB indexes for optimal query performance
- **Vector Search**: MongoDB Atlas vector search for semantic similarity
- **Graceful Shutdown**: Proper cleanup on server termination
- **Health Checks**: Comprehensive health monitoring endpoints

## 📋 Table of Contents

- [Installation](#installation)
- [Configuration](#configuration)
- [API Endpoints](#api-endpoints)
- [Technical Implementation](#technical-implementation)
- [Data Models](#data-models)
- [Security Features](#security-features)
- [Performance Optimization](#performance-optimization)
- [Deployment](#deployment)
- [Testing](#testing)
- [Contributing](#contributing)

## 🛠️ Installation

### Prerequisites
- Node.js >= 18.0.0
- npm >= 8.0.0
- MongoDB Atlas account with vector search enabled
- Redis server
- Google Gemini API keys

### Setup

1. **Clone the repository**
```bash
git clone <repository-url>
cd ai-agent-api
```

2. **Install dependencies**
```bash
npm install
```

3. **Environment Configuration**
```bash
cp .env.example .env
```

Edit `.env` with your configuration:
```env
# Server Configuration
PORT=3000
NODE_ENV=development

# MongoDB Configuration
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/ai-agent-api

# Redis Configuration
REDIS_URL=redis://localhost:6379
REDIS_PASSWORD=your_redis_password

# Google Gemini API Keys (comma-separated for rotation)
GEMINI_API_KEYS=key1,key2,key3,key4,key5,key6,key7,key8,key9,key10

# Security
AGENT_API_TOKEN=your_secure_api_token
SESSION_SECRET=your_session_secret

# CORS Configuration
ALLOWED_ORIGINS=http://localhost:3000,http://localhost:3001

# Sentry Configuration
SENTRY_DSN=your_sentry_dsn
```

4. **Start the server**
```bash
# Development mode
npm run dev

# Production mode
npm run build
npm start
```

## ⚙️ Configuration

### Security Configuration
The API includes comprehensive security measures:

```typescript
// Security limits and validation
MAX_FILE_SIZE: 20MB
MAX_FILES_PER_REQUEST: 10
MAX_TEXT_LENGTH: 1MB
ALLOWED_DOCUMENT_EXTENSIONS: ['.pdf', '.docx', '.doc', '.txt', '.csv', '.md']
ALLOWED_AUDIO_EXTENSIONS: ['.mp3', '.wav', '.m4a', '.aac', '.ogg', '.flac']
ALLOWED_VIDEO_EXTENSIONS: ['.mp4', '.webm', '.mov', '.avi', '.mkv']
```

### Rate Limiting
```typescript
// Production rate limits
Train: 100 requests per 15 minutes
Ask: 500 requests per 15 minutes
Analytics: 200 requests per 15 minutes
Feedback: 100 requests per 15 minutes
Watchdog: 50 requests per 15 minutes
```

### Retrieval Configuration
```typescript
// Default retrieval settings
VECTOR_K: 8,                    // Top-k for vector search
KEYWORD_K: 3,                   // Top-k for keyword search
MIN_SIMILARITY_SCORE: 0.3,      // Minimum similarity threshold
MAX_CONTEXT_LENGTH: 4000,       // Maximum context length
CONFIDENCE_THRESHOLD: 0.4,      // Minimum confidence score
MAX_CHUNKS: 5                   // Maximum chunks to include
```

## 📡 API Endpoints

### Authentication
All API endpoints require Bearer token authentication:
```
Authorization: Bearer your_api_token
```

### 1. Health Check
**GET** `/health`

Check server health and dependencies status.

**Sample Response:**
```json
{
  "status": "OK",
  "timestamp": "2024-01-15T10:30:00.000Z",
  "uptime": 3600,
  "environment": "production",
  "version": "1.0.0",
  "memory": {
    "rss": 52428800,
    "heapTotal": 20971520,
    "heapUsed": 10485760
  },
  "database": "connected",
  "gemini": {
    "status": "operational",
    "activeKeys": 10,
    "totalRequests": 1500
  }
}
```

### 2. API Status
**GET** `/api/status`

Get API status information.

**Sample Response:**
```json
{
  "status": "operational",
  "service": "AI Agent API",
  "version": "1.0.0",
  "environment": "production",
  "timestamp": "2024-01-15T10:30:00.000Z"
}
```

### 3. Train Agent
**POST** `/api/train`

Train an AI agent with various content sources.

**Request Body:**
```json
{
  "agentId": "sales-agent-001",
  "source": "document",
  "fileType": "pdf",
  "files": [File],
  "sourceUrl": "https://example.com/document.pdf"
}
```

**Supported Sources:**
- `document`: PDF, DOCX, DOC, TXT, CSV, MD files
- `website`: Web scraping with content extraction
- `youtube`: YouTube video transcript extraction
- `audio`: MP3, WAV, M4A, AAC, OGG, FLAC files
- `video`: MP4, WebM, MOV, AVI, MKV files

**Sample Response:**
```json
{
  "jobId": "train_1705312200000_abc123",
  "status": "queued",
  "message": "Training job started successfully",
  "agentId": "sales-agent-001",
  "source": "document",
  "estimatedTime": "30-60 seconds"
}
```

### 4. Training Job Status
**GET** `/api/train/status/{jobId}`

Check training job progress.

**Sample Response:**
```json
{
  "jobId": "train_1705312200000_abc123",
  "status": "processing",
  "progress": 75,
  "chunksProcessed": 150,
  "totalChunks": 200,
  "successCount": 145,
  "errorCount": 2,
  "skippedCount": 3,
  "estimatedTimeRemaining": "15 seconds"
}
```

### 5. Ask Question
**POST** `/api/ask`

Ask a question to a trained AI agent.

**Request Body:**
```json
{
  "agentId": "sales-agent-001",
  "question": "What are the benefits of the premium plan?"
}
```

**Sample Response:**
```json
{
  "agent_id": "sales-agent-001",
  "query": "What are the benefits of the premium plan?",
  "reply": "Based on our training data, the premium plan offers the following benefits:\n\n1. Unlimited access to all features\n2. Priority customer support\n3. Advanced analytics dashboard\n4. Custom integrations\n5. 24/7 technical support\n\nThe premium plan costs $99/month and includes all standard features plus these additional benefits.",
  "confidence": 0.87,
  "fallback_used": false,
  "question_id": "q_1705312200000_xyz789",
  "agent_metadata": {
    "name": "Sales Assistant",
    "role": "Fashion store assistant",
    "tone": "friendly",
    "do_not_answer_from_general_knowledge": true
  },
  "feedback_prompt": "How was this answer? (optional feedback)",
  "retraining_suggested": false,
  "audit": {
    "hallucination_risk_score": 0.12,
    "hallucination_risk_level": "low",
    "factual_accuracy": 0.89,
    "source_alignment": 0.92,
    "completeness": 0.85,
    "relevance": 0.94,
    "compliance_flags": [],
    "requires_human_review": false
  },
  "meta": {
    "tokens_used": 1250,
    "retrieval_time_ms": 245,
    "model": "gemini-1.5",
    "retrieval_strategy": "hybrid",
    "chunks_used": 3,
    "chunks_searched": 25,
    "chunks_filtered": 22,
    "context_length": 2800,
    "sources_count": 2,
    "average_similarity": 0.78,
    "retrieval_config": {
      "vector_k": 8,
      "keyword_k": 3,
      "similarity_threshold": 0.3,
      "confidence_threshold": 0.4,
      "max_chunks": 5
    },
    "sources": [
      {
        "source": "product_catalog.pdf",
        "source_url": "https://example.com/catalog.pdf",
        "chunk_index": 15,
        "confidence": 0.89,
        "similarity": 0.82
      },
      {
        "source": "pricing_guide.pdf",
        "source_url": "https://example.com/pricing.pdf",
        "chunk_index": 8,
        "confidence": 0.85,
        "similarity": 0.76
      }
    ]
  }
}
```

### 6. Analytics Endpoints

#### Get Question Analytics
**GET** `/api/analytics/questions/{agentId}`

**Sample Response:**
```json
{
  "agentId": "sales-agent-001",
  "period": "last_30_days",
  "summary": {
    "totalQuestions": 1250,
    "answeredQuestions": 1180,
    "unansweredQuestions": 70,
    "averageConfidence": 0.78,
    "averageResponseTime": 320,
    "totalTokensUsed": 156000
  },
  "questions": [
    {
      "question": "What are the benefits of the premium plan?",
      "count": 45,
      "averageConfidence": 0.87,
      "lastAsked": "2024-01-15T10:30:00.000Z"
    }
  ]
}
```

#### Get Source Analytics
**GET** `/api/analytics/sources/{agentId}`

**Sample Response:**
```json
{
  "agentId": "sales-agent-001",
  "sources": [
    {
      "sourceUrl": "https://example.com/catalog.pdf",
      "sourceType": "document",
      "fileName": "product_catalog.pdf",
      "usageCount": 156,
      "lastUsed": "2024-01-15T10:30:00.000Z",
      "averageConfidence": 0.82,
      "totalTokensUsed": 45000,
      "totalQuestionsAnswered": 89
    }
  ]
}
```

#### Get Business Insights
**GET** `/api/analytics/insights/{agentId}`

**Sample Response:**
```json
{
  "agentId": "sales-agent-001",
  "insights": [
    {
      "date": "2024-01-15",
      "totalQuestions": 45,
      "answeredQuestions": 42,
      "unansweredQuestions": 3,
      "averageConfidence": 0.79,
      "averageResponseTime": 315,
      "totalTokensUsed": 5600,
      "topSources": [
        {
          "sourceUrl": "https://example.com/catalog.pdf",
          "usageCount": 12,
          "averageConfidence": 0.85
        }
      ],
      "topQuestions": [
        {
          "question": "What are the benefits of the premium plan?",
          "count": 8,
          "averageConfidence": 0.87
        }
      ],
      "userSessions": 23,
      "uniqueUsers": 18
    }
  ]
}
```

### 7. Feedback System
**POST** `/api/feedback`

Submit user feedback for responses.

**Request Body:**
```json
{
  "agentId": "sales-agent-001",
  "questionId": "q_1705312200000_xyz789",
  "question": "What are the benefits of the premium plan?",
  "originalReply": "Based on our training data...",
  "confidence": 0.87,
  "userSatisfaction": "positive",
  "feedbackText": "Very helpful and accurate information!"
}
```

**Sample Response:**
```json
{
  "success": true,
  "feedbackId": "feedback_1705312200000_def456",
  "message": "Feedback submitted successfully",
  "retrainingSuggestion": {
    "suggested": false,
    "reason": "High confidence and positive feedback"
  }
}
```

### 8. Cache Management
**GET** `/api/cache/status`

**Sample Response:**
```json
{
  "status": "operational",
  "cacheSize": 1024,
  "hitRate": 0.78,
  "missRate": 0.22,
  "totalRequests": 15000,
  "memoryUsage": "45MB"
}
```

**POST** `/api/cache/clear`

**Sample Response:**
```json
{
  "success": true,
  "message": "Cache cleared successfully",
  "clearedEntries": 1024
}
```

### 9. Watchdog Service
**POST** `/api/watchdog/audit`

**Request Body:**
```json
{
  "agentId": "sales-agent-001",
  "questionId": "q_1705312200000_xyz789",
  "question": "What are the benefits of the premium plan?",
  "response": "Based on our training data...",
  "confidence": 0.87,
  "chunksUsed": 3,
  "sources": ["catalog.pdf", "pricing.pdf"]
}
```

**Sample Response:**
```json
{
  "auditId": "audit_1705312200000_ghi789",
  "hallucinationRiskScore": 0.12,
  "hallucinationRiskLevel": "low",
  "factualAccuracy": 0.89,
  "sourceAlignment": 0.92,
  "completeness": 0.85,
  "relevance": 0.94,
  "complianceFlags": [],
  "requiresHumanReview": false,
  "auditReasoning": "Response aligns well with provided sources and shows no signs of hallucination."
}
```

## 🔧 Technical Implementation

### RAG System Architecture

#### 1. Content Processing Pipeline
```
Input Source → Text Extraction → Chunking → Embedding Generation → Storage
     ↓              ↓              ↓              ↓              ↓
Documents      PDF.js, Mammoth   Smart Chunking  Gemini API    MongoDB
Websites       Puppeteer         with Overlap    768-dim vectors  Vector Index
YouTube        Transcript API    Metadata Track
Audio/Video    Whisper API       Content Hash
```

#### 2. Retrieval Strategy
The system uses a hybrid approach combining:

**Vector Search (Semantic)**
- MongoDB Atlas vector search with HNSW index
- 768-dimensional embeddings from Google Gemini
- Cosine similarity scoring
- Configurable K-value (default: 8)

**Keyword Search (Lexical)**
- MongoDB text search with regex patterns
- Keyword extraction from questions
- Stop word filtering
- Configurable K-value (default: 3)

**Result Fusion & Ranking**
- Combine vector and keyword results
- Calculate confidence scores
- Apply similarity and confidence thresholds
- Rank by confidence score
- Limit to maximum chunks (default: 5)

#### 3. Content Deduplication
```typescript
// SHA256 hash generation for deduplication
function generateContentHash(text: string): string {
  return crypto.createHash('sha256').update(text.trim()).digest('hex');
}

// Version tracking for content updates
async function getContentVersion(agentId: string, contentHash: string): Promise<number> {
  const existing = await Memory.findOne({ agentId, contentHash });
  if (existing) return existing.contentVersion;
  
  const highestVersion = await Memory.findOne({ agentId }).sort({ contentVersion: -1 });
  return (highestVersion?.contentVersion || 0) + 1;
}
```

#### 4. Intelligent Chunking
```typescript
// Smart chunking with overlap preservation
function chunkText(text: string, maxLength: number = 1000, overlap: number = 200): ChunkWithMetadata[] {
  // Split by paragraphs first
  const paragraphs = text.split(/\n\s*\n/);
  
  // Process each paragraph
  for (const paragraph of paragraphs) {
    if (paragraph.length <= maxLength) {
      // Keep short paragraphs as single chunks
      chunks.push({ text: paragraph.trim(), metadata: {...} });
    } else {
      // Split long paragraphs by sentences
      const sentences = paragraph.split(/(?<=[.?!])\s+/);
      // Create overlapping chunks
    }
  }
  
  return chunks;
}
```

#### 5. Agent Metadata Integration
```typescript
// Query external agents database
const getAgentMetadata = async (agentId: string): Promise<AgentMetadata | null> => {
  const agentsDb = mongoose.connection.useDb('agents');
  const Agent = agentsDb.model('Agent', new mongoose.Schema({
    name: String,
    tone: String,
    role: String,
    do_not_answer_from_general_knowledge: Boolean
  }));
  
  return await Agent.findOne({ _id: agentId }).lean();
};
```

#### 6. Response Generation
```typescript
// Context-aware prompt construction
const prompt = `
You are ${agentMetadata?.name || 'an expert assistant'} with the role of ${agentMetadata?.role || 'an AI assistant'}.

${agentContext}${agentMetadata?.do_not_answer_from_general_knowledge ? 
  'IMPORTANT: Use ONLY the context below to answer the question. Do NOT use any general knowledge outside of the provided context.' : 
  'Use the context below to answer the question. You may supplement with general knowledge if needed.'}

${agentMetadata?.tone ? `Respond in a ${agentMetadata.tone} tone.` : ''}

Context:
${context}

Question: ${question}

Answer:`;
```

### Performance Optimizations

#### 1. Database Indexing Strategy
```typescript
// Compound indexes for optimal query performance
MemorySchema.index({ agentId: 1, source: 1 });
MemorySchema.index({ agentId: 1, sourceUrl: 1 });
MemorySchema.index({ agentId: 1, chunkIndex: 1 });
MemorySchema.index({ agentId: 1, contentHash: 1 }); // For deduplication
MemorySchema.index({ agentId: 1, contentVersion: 1 }); // For versioning

// Vector index created in MongoDB Atlas
// db.memories.createIndex({ embedding: "vector" }, { 
//   vectorSize: 768, 
//   vectorSearchOptions: { numLists: 100 } 
// });
```

#### 2. Caching Strategy
```typescript
// Redis-based caching with TTL
const cacheKey = `response:${agentId}:${questionHash}`;
const cachedResponse = await redis.get(cacheKey);

if (cachedResponse) {
  return JSON.parse(cachedResponse);
}

// Cache invalidation on training updates
await redis.del(`cache:${agentId}:*`);
```

#### 3. Request Optimization
```typescript
// Request size validation
app.use((req: Request, res: Response, next: NextFunction) => {
  const contentLength = parseInt(req.headers['content-length'] || '0');
  
  if (contentLength > SECURITY_CONFIG.MAX_TEXT_LENGTH) {
    return res.status(413).json({
      error: 'Request too large',
      maxSize: `${SECURITY_CONFIG.MAX_TEXT_LENGTH / 1024}kb`
    });
  }
  
  next();
});
```

## 🗄️ Data Models

### Memory Model
Stores trained knowledge chunks with vector embeddings:

```typescript
interface IMemory {
  agentId: string;
  text: string;
  embedding: number[]; // 768-dimensional vector
  source: 'audio' | 'video' | 'document' | 'website' | 'youtube';
  sourceUrl?: string;
  chunkIndex: number;
  contentHash: string; // SHA256 for deduplication
  contentVersion: number;
  chunkMetadata: {
    totalChunks: number;
    chunkSize: number;
    startPosition?: number;
    endPosition?: number;
    fileName?: string;
    pageNumber?: number;
    timestamp?: string;
    section?: string;
  };
  sourceMetadata?: {
    title?: string;
    author?: string;
    duration?: number;
    timestamp?: string;
  };
}
```

### Analytics Models
Comprehensive analytics tracking:

```typescript
// Question Analytics
interface IQuestionAnalytics {
  agentId: string;
  question: string;
  questionHash: string;
  timestamp: Date;
  responseTime: number;
  confidence: number;
  fallbackUsed: boolean;
  tokensUsed: number;
  chunksUsed: number;
  chunksSearched: number;
  averageSimilarity: number;
  sources: string[];
}

// Business Insights
interface IBusinessInsights {
  agentId: string;
  date: Date;
  totalQuestions: number;
  answeredQuestions: number;
  unansweredQuestions: number;
  averageConfidence: number;
  averageResponseTime: number;
  totalTokensUsed: number;
  topSources: Array<{
    sourceUrl?: string;
    fileName?: string;
    usageCount: number;
    averageConfidence: number;
  }>;
  topQuestions: Array<{
    question: string;
    count: number;
    averageConfidence: number;
  }>;
  userSessions: number;
  uniqueUsers: number;
}
```

### TrainJob Model
Tracks training job progress:

```typescript
interface ITrainJob {
  jobId: string;
  status: 'queued' | 'processing' | 'completed' | 'failed';
  progress: number;
  error: any;
  result: any;
  agentId: string;
  fileNames: string[];
  chunksProcessed?: number;
  totalChunks?: number;
  successCount?: number;
  errorCount?: number;
  skippedCount?: number;
}
```

## 🔒 Security Features

### Input Validation & Sanitization
- **Agent ID Validation**: Alphanumeric with hyphens/underscores only
- **Question Sanitization**: HTML/script tag removal, length limits
- **URL Validation**: Protocol and domain whitelisting
- **File Upload Security**: Extension and MIME type validation

### Rate Limiting
- Configurable limits per endpoint
- IP-based tracking
- Graceful degradation

### CORS Protection
- Configurable allowed origins
- Credential support
- Method and header restrictions

### Session Security
- MongoDB-based session store
- Secure cookie configuration
- Automatic session cleanup

## ⚡ Performance Optimization

### Vector Search
- MongoDB Atlas vector search with HNSW index
- 768-dimensional embeddings from Google Gemini
- Cosine similarity scoring

### Caching Strategy
- Redis-based response caching
- Configurable TTL and cache size
- Cache invalidation on training updates

### Database Optimization
- Compound indexes for common queries
- Content hash indexing for deduplication
- Vector index for similarity search

### Response Optimization
- Gzip compression
- Configurable context length limits
- Smart chunk filtering and ranking

## 🚀 Deployment

### Docker Deployment
```bash
# Build image
docker build -t ai-agent-api .

# Run container
docker run -p 3000:3000 --env-file .env ai-agent-api
```

### Production Checklist
- [ ] Set `NODE_ENV=production`
- [ ] Configure secure `SESSION_SECRET`
- [ ] Set up MongoDB Atlas with vector search
- [ ] Configure Redis with password
- [ ] Set up Sentry for monitoring
- [ ] Configure CORS for production domains
- [ ] Set up SSL/TLS certificates
- [ ] Configure rate limiting for production loads
- [ ] Set up monitoring and alerting

### Environment Variables
```env
# Required
MONGO_URI=mongodb+srv://...
GEMINI_API_KEYS=key1,key2,key3...
AGENT_API_TOKEN=your_secure_token

# Optional
PORT=3000
NODE_ENV=production
REDIS_URL=redis://localhost:6379
REDIS_PASSWORD=your_redis_password
SESSION_SECRET=your_session_secret
ALLOWED_ORIGINS=https://yourdomain.com
SENTRY_DSN=your_sentry_dsn
```

## 🧪 Testing

### Run Tests
```bash
# Run all tests
npm test

# Run specific test
node test/test-api.js
node test/test-analytics.js
node test/test-security.js
```

### Test Coverage
- API endpoint testing
- Security validation testing
- File upload testing
- Analytics testing
- Cache functionality testing
- Watchdog service testing

### Sample Test Data
The `test/sample/` directory contains test files:
- `sample.txt` - Text document for testing
- `sample.mp3` - Audio file for testing
- `sample.mp4` - Video file for testing

## 📊 Monitoring & Analytics

### Sentry Integration
- Error tracking and performance monitoring
- Release tracking
- User context and breadcrumbs

### Health Monitoring
- Database connection status
- Redis connection status
- Gemini API status
- Memory usage tracking

### Business Analytics
- Question volume and success rates
- Source performance tracking
- User satisfaction metrics
- Response quality analysis

## 🔧 Development

### Scripts
```bash
npm run dev          # Development mode with hot reload
npm run build        # Build for production
npm run start        # Start production server
npm run lint         # Run ESLint
npm run lint:fix     # Fix ESLint issues
npm run clean        # Clean build directory
```

### Code Structure
```
src/
├── models/          # MongoDB schemas and models
├── routes/          # Express route handlers
├── services/        # Business logic services
├── utils/           # Utility functions
└── server.ts        # Main application file
```

### Key Technologies
- **Node.js** - Runtime environment
- **TypeScript** - Type-safe JavaScript
- **Express** - Web framework
- **MongoDB** - Document database with vector search
- **Redis** - Caching and session storage
- **Google Gemini** - AI model for embeddings and generation
- **Sentry** - Error monitoring
- **Swagger** - API documentation

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests for new functionality
5. Ensure all tests pass
6. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🆘 Support

For support and questions:
- Create an issue in the repository
- Check the API documentation at `/docs`
- Review the test files for usage examples

## 🔄 Changelog

### Version 1.0.0
- Initial release with core RAG functionality
- Multi-source training support
- Comprehensive analytics system
- Security features and monitoring
- Production-ready deployment configuration
