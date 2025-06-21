# 4SalesAI Agent API

A production-ready Node.js/TypeScript API for async, multi-source document, audio, video, website, and YouTube training, with advanced RAG, Gemini integration, MongoDB Atlas vector search, comprehensive analytics, and intelligent caching.

## 🚀 Quick Start

```bash
# Install dependencies
npm install

# Copy environment file and configure
cp .env.example .env

# Start development server
npm run dev

# Access API documentation
# Open http://localhost:3000/docs in your browser
```

## 🔑 API Token Authentication

**All `/api/*` endpoints require authentication** using a Bearer token in the Authorization header.

### Setup
```env
# In your .env file
AGENT_API_TOKEN=your_secret_token_here
```

### Usage
```bash
# Example with curl
curl -H "Authorization: Bearer your_token" http://localhost:3000/api/ask

# Example with JavaScript
fetch('/api/ask', {
  headers: { 'Authorization': 'Bearer your_token' },
  method: 'POST',
  body: JSON.stringify({ agentId: 'my-agent', question: 'Hello' })
})
```

## 🎯 Core Features

- **🔄 Async Multi-Source Training**: Documents (PDF, DOCX, DOC, CSV, TXT), Audio (mp3, wav), Video (mp4), Websites, YouTube
- **🧠 Advanced RAG**: Hybrid vector+keyword search, context deduplication, robust prompt engineering
- **⚡ Intelligent Caching**: Redis-based caching with per-agent isolation (70% performance improvement)
- **📊 Comprehensive Analytics**: Real-time dashboard, performance metrics, business insights
- **🔐 Production Security**: CORS, rate limiting, security headers, compression, logging
- **🏥 Health Monitoring**: Built-in health checks and status endpoints
- **🛡️ Watchdog System**: Hallucination detection and response auditing
- **💬 Feedback System**: User feedback collection and retraining suggestions

## 📚 API Documentation

### Interactive Documentation
Visit `http://localhost:3000/docs` for complete interactive API documentation with Swagger UI.

### Core Endpoints

#### Health & Status
```http
GET /health                    # Health check
GET /api/status               # Service status
```

#### Training
```http
POST /api/train               # Start async training job
GET /api/train/status/:jobId  # Poll job status
```

#### Question Answering
```http
POST /api/ask                 # Ask a question (RAG)
```

#### Analytics
```http
GET /api/analytics/dashboard/:agentId     # Dashboard overview
GET /api/analytics/top-questions/:agentId # Most asked questions
GET /api/analytics/sources/:agentId       # Source analytics
GET /api/analytics/unanswered/:agentId    # Unanswered queries
GET /api/analytics/similarity-heatmap/:agentId # Similarity visualization
GET /api/analytics/business-insights/:agentId  # Business intelligence
POST /api/analytics/generate-insights/:agentId # Generate daily insights
GET /api/analytics/export/:agentId        # Export data (JSON/CSV)
```

#### Feedback System
```http
POST /api/feedback                           # Submit user feedback
GET /api/feedback/suggestions/:agentId       # Get retraining suggestions
GET /api/feedback/summary/:agentId           # Get feedback summary
POST /api/feedback/suggestions/:id/implement # Mark suggestion as implemented
```

#### Cache Management
```http
GET /api/cache/health              # Check cache health
GET /api/cache/stats/:agentId      # Get agent cache statistics
DELETE /api/cache/clear/:agentId   # Clear agent cache
GET /api/cache/stats               # Get overall cache statistics
```

#### Watchdog System
```http
POST /api/watchdog/audit           # Audit response for hallucination risk
GET /api/watchdog/stats/:agentId   # Get audit statistics
GET /api/watchdog/high-risk/:agentId # Get high-risk responses
POST /api/watchdog/review/:id      # Mark audit as reviewed
GET /api/watchdog/config           # Get watchdog configuration
POST /api/watchdog/config          # Update watchdog configuration
```

## 🏗️ Architecture

### Technology Stack
- **Backend**: Node.js 18+, Express.js, TypeScript, ESM
- **Database**: MongoDB Atlas with vector search (768-dim Gemini embeddings)
- **AI**: Google Gemini for embeddings, generation, file/video/audio processing
- **Cache**: Redis for agent isolation and context caching
- **Queue**: Async job processing with MongoDB persistence
- **Security**: CORS, Helmet, rate limiting, compression, logging

### Cache Architecture

The API implements a sophisticated caching system with **agent isolation**:

#### Cache Layers
1. **Embedding Cache**: Stores generated embeddings for text chunks
2. **Answer Cache**: Stores complete Q&A pairs with confidence scores
3. **Context Cache**: Stores retrieved contexts for similar questions

#### Agent Isolation
Each agent has its own isolated cache namespace:
```
embedding:{agentId}:{hash} → [0.1, 0.2, 0.3, ...]
answers:{agentId} → [{"question": "...", "answer": "...", ...}]
contexts:{agentId} → [{"question": "...", "context": "...", ...}]
```

#### Performance Benefits
- **70% faster** response times for repeated questions
- **Reduced API costs** with fewer Gemini embedding calls
- **Intelligent similarity matching** for similar questions
- **Automatic TTL** to prevent stale data

## 📊 Analytics Dashboard

### Features
- **Real-time Data**: Live analytics for any agent
- **Interactive Charts**: Visualize trends and patterns
- **Export Capabilities**: Download data in JSON or CSV format
- **Business Intelligence**: Daily insights and performance metrics

### Key Metrics
- **Question Analytics**: Total questions, answered vs unanswered, confidence scores
- **Source Analytics**: Most used sources, source types, usage patterns
- **Performance Metrics**: Response times, token usage, similarity scores
- **Business Insights**: Daily trends, user sessions, unique users
- **Similarity Heatmaps**: Visual representation of question-chunk relationships

### Dashboard Access
Open `doc/analytics-dashboard.html` in your browser for the interactive dashboard.

## 🛠️ Installation & Setup

### Prerequisites
- Node.js 18+
- MongoDB Atlas account with vector search enabled
- Google Gemini API key(s)
- Redis (for caching)

### 1. Clone & Install
```bash
git clone <repository-url>
cd ai-agent-api
npm install
```

### 2. Environment Configuration
```bash
cp .env.example .env
```

Configure your `.env` file:
```env
# MongoDB
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/database

# Google Gemini API Keys (supports multiple keys for load balancing)
GEMINI_API_KEY=your_gemini_api_key
GEMINI_API_KEY_1=your_second_key
GEMINI_API_KEY_2=your_third_key
# ... up to GEMINI_API_KEY_9

# API Security
AGENT_API_TOKEN=your_secret_api_token
SESSION_SECRET=your_session_secret

# Redis Configuration
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=your_redis_password
REDIS_DB=0

# Production Settings
NODE_ENV=production
ALLOWED_ORIGINS=https://www.4salesai.com
```

### 3. MongoDB Atlas Setup
1. Create a MongoDB Atlas cluster
2. Enable vector search
3. Create a vector index on the `embedding` field with dimension 768

### 4. Redis Setup

#### Option A: Local Installation
```bash
# Ubuntu/Debian
sudo apt-get install redis-server

# macOS
brew install redis

# Windows
# Download from https://redis.io/download
```

#### Option B: Docker
```bash
docker run -d -p 6379:6379 redis:alpine
```

### 5. Build & Start
```bash
# Development
npm run dev

# Production
npm run build
npm start
```

## 📖 Usage Examples

### Training with Documents
```bash
curl -F agentId=my-agent \
     -F source=document \
     -F fileType=txt \
     -F files=@sample.txt \
     -H "Authorization: Bearer your_token" \
     http://localhost:3000/api/train
```

### Training with Audio
```bash
curl -F agentId=my-agent \
     -F source=audio \
     -F fileType=mp3 \
     -F files=@sample.mp3 \
     -H "Authorization: Bearer your_token" \
     http://localhost:3000/api/train
```

### Training with Video
```bash
curl -F agentId=my-agent \
     -F source=video \
     -F fileType=mp4 \
     -F files=@sample.mp4 \
     -H "Authorization: Bearer your_token" \
     http://localhost:3000/api/train
```

### Training with Website
```bash
curl -X POST \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer your_token" \
     -d '{"agentId":"my-agent","source":"website","sourceUrl":"https://example.com"}' \
     http://localhost:3000/api/train
```

### Training with YouTube
```bash
curl -X POST \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer your_token" \
     -d '{"agentId":"my-agent","source":"youtube","sourceUrl":"https://youtube.com/watch?v=..."}' \
     http://localhost:3000/api/train
```

### Asking Questions
```bash
curl -X POST \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer your_token" \
     -d '{"agentId":"my-agent","question":"What is the flagship product?"}' \
     http://localhost:3000/api/ask
```

### Getting Analytics
```bash
# Dashboard overview
curl -H "Authorization: Bearer your_token" \
     http://localhost:3000/api/analytics/dashboard/my-agent

# Top questions
curl -H "Authorization: Bearer your_token" \
     http://localhost:3000/api/analytics/top-questions/my-agent?limit=10

# Export data
curl -H "Authorization: Bearer your_token" \
     http://localhost:3000/api/analytics/export/my-agent?format=json
```

## 🔧 Configuration

### Cache Settings
Configure cache behavior in `src/services/cache.ts`:
```typescript
const CACHE_CONFIG = {
  EMBEDDING_TTL: 24 * 60 * 60, // 24 hours
  ANSWER_TTL: 60 * 60, // 1 hour
  CONTEXT_TTL: 30 * 60, // 30 minutes
  MAX_CACHE_SIZE: 1000, // Max items per agent
  SIMILARITY_THRESHOLD: 0.85 // Threshold for considering embeddings similar
};
```

### Rate Limiting
Production rate limits (configurable in `src/server.ts`):
- Training: 100 requests per 15 minutes
- Questions: 500 requests per 15 minutes
- Analytics: 200 requests per 15 minutes
- Feedback: 100 requests per 15 minutes
- Watchdog: 50 requests per 15 minutes

### Security Settings
- CORS restricted to production domain
- Helmet security headers
- Request size limits
- Input sanitization and validation

## 🧪 Testing

### Run Test Suites
```bash
# API functionality
node test-api.js

# Analytics system
node test-analytics.js

# Cache system
node test-cache.js

# Feedback system
node test-feedback.js

# Watchdog system
node test-watchdog.js

# Security features
node test-security.js
```

### Test File Processing
```bash
# Test document processing
node test/test-document.js

# Test audio processing
node test/test-audio.js

# Test video processing
node test/test-video.js

# Test website scraping
node test/test-website.js

# Test YouTube processing
node test/test-youtube.js
```

## 🚀 Production Deployment

### Environment Variables
Ensure all production environment variables are set:
- `NODE_ENV=production`
- `ALLOWED_ORIGINS=https://www.4salesai.com`
- Strong `AGENT_API_TOKEN` and `SESSION_SECRET`
- Production MongoDB and Redis credentials

### Security Checklist
- [ ] Change default API token
- [ ] Set strong session secret
- [ ] Configure CORS for production domain
- [ ] Enable Redis password authentication
- [ ] Set up MongoDB Atlas with proper access controls
- [ ] Configure rate limiting for production load
- [ ] Set up monitoring and logging

### Monitoring
- Health check: `GET /health`
- API status: `GET /api/status`
- Cache health: `GET /api/cache/health`
- Sentry error tracking (configured)

## 🔍 Troubleshooting

### Common Issues

#### MongoDB Connection
```bash
# Check connection
curl http://localhost:3000/health

# Verify environment variables
echo $MONGO_URI
```

#### Redis Connection
```bash
# Test Redis connection
redis-cli ping

# Check cache health
curl -H "Authorization: Bearer your_token" \
     http://localhost:3000/api/cache/health
```

#### File Upload Issues
- Check file size limits
- Verify supported file types
- Ensure proper multipart form data

#### Training Job Failures
```bash
# Check job status
curl -H "Authorization: Bearer your_token" \
     http://localhost:3000/api/train/status/job_id_here
```

### Logs
- Development: Console logs with timestamps
- Production: Structured logging with Sentry integration
- Request logging with security information

## 📈 Performance Optimization

### Cache Optimization
- Monitor cache hit rates via `/api/cache/stats/:agentId`
- Adjust TTL settings based on usage patterns
- Clear agent caches when retraining

### Database Optimization
- Monitor MongoDB Atlas performance
- Optimize vector search queries
- Use appropriate indexes

### API Optimization
- Use connection pooling
- Implement request compression
- Monitor rate limiting effectiveness

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests for new functionality
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License.

## 🆘 Support

For support and questions:
- Check the interactive API documentation at `/docs`
- Review the troubleshooting section
- Check the test files for usage examples
- Monitor the health endpoints for system status

---

**Ready to build intelligent AI agents? Start with `npm run dev` and visit `http://localhost:3000/docs` for the complete API documentation!**
