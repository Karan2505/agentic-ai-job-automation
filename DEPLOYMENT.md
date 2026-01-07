# Agentic AI Job Automation System - Deployment Guide

## Overview

This is a production-ready, fully automated job application system powered by AI agents. The system autonomously applies to jobs across LinkedIn, Indeed, Naukri, and Uplers while tracking applications in real-time.

## System Architecture

```
┌─────────────────────────────────────────┐
│         Frontend (React/TypeScript)     │
│  - Dashboard with Analytics             │
│  - Resume Upload Interface              │
│  - Real-time Application Tracking       │
│  - User Authentication                  │
└────────────┬────────────────────────────┘
             │
             │ HTTPS
             │
┌────────────▼────────────────────────────┐
│  Backend API (FastAPI - Python 3.11)    │
│  - Resume Parsing & NLP                 │
│  - Job Matching Engine                  │
│  - Application Automation               │
│  - WebSocket Real-time Updates          │
│  - JWT Authentication & Rate Limiting   │
└────────────┬────────────────────────────┘
             │
    ┌────────┴────────┬─────────────┐
    │                 │             │
┌───▼────┐      ┌────▼──┐    ┌─────▼────┐
│PostgreSQL   │Redis │  │Celery Queue│
│  DB     │      Cache   │Async Jobs │
└─────────┘      └───────┘    └─────────┘
```

## Prerequisites

- Docker and Docker Compose
- Python 3.11+ (for local development)
- PostgreSQL 13+
- Redis 6+
- Node.js 16+ (for frontend development)

## Quick Start with Docker

### 1. Clone the Repository

```bash
git clone https://github.com/Karan2505/agentic-ai-job-automation.git
cd agentic-ai-job-automation
```

### 2. Set Environment Variables

```bash
cp .env.example .env
```

Edit `.env` with your configuration:

```bash
# API Configuration
API_HOST=0.0.0.0
API_PORT=8000
API_TITLE="Job Automation System"
API_VERSION="1.0.0"

# Database
DATABASE_URL=postgresql+asyncpg://postgres:postgres@postgres:5432/job_automation

# Redis
REDIS_URL=redis://redis:6379/0

# Environment
ENVIRONMENT=production
DEBUG=false

# Security
SECRET_KEY=your-super-secret-key-change-in-production
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

# Sentry
SENTRY_DSN=https://your-sentry-dsn@sentry.io/123456
```

### 3. Start with Docker Compose

```bash
docker-compose up -d
```

This starts:
- Backend API on `http://localhost:8000`
- Frontend on `http://localhost:3000`
- PostgreSQL Database
- Redis Cache
- Celery Workers

### 4. Health Check

```bash
curl http://localhost:8000/health
```

Expected response:
```json
{"status": "healthy", "service": "job-automation-api"}
```

## API Documentation

### Interactive Docs

- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc

### Key Endpoints

```
GET    /health              - Health check
POST   /api/v1/auth/login   - User authentication
POST   /api/v1/resumes      - Upload and analyze resume
GET    /api/v1/jobs         - Get matching jobs
POST   /api/v1/applications - Submit job application
GET    /api/v1/analytics    - Application analytics
```

## Local Development

### Backend Setup

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run migrations
alembic upgrade head

# Start development server
python -m uvicorn backend.app.main:app --reload --host 0.0.0.0 --port 8000
```

### Frontend Setup

```bash
cd frontend
npm install
npm run dev
```

## Database Migrations

```bash
# Create new migration
alembic revision --autogenerate -m "Add new column"

# Apply migrations
alembic upgrade head

# Rollback last migration
alembic downgrade -1
```

## Deployment to Cloud

### AWS Elastic Container Service (ECS)

```bash
# Build and push Docker image
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com

docker tag agentic-ai-job-automation:latest ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/agentic-ai-job-automation:latest

docker push ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/agentic-ai-job-automation:latest
```

### Heroku

```bash
heroku create agentic-ai-job-automation
heroku container:push web
heroku container:release web
heroku config:set ENVIRONMENT=production DEBUG=false
```

### DigitalOcean App Platform

1. Connect GitHub repository
2. Set environment variables in App Platform console
3. Deploy

## Monitoring & Logging

### Sentry Error Tracking

Errors are automatically tracked in Sentry for monitoring.

### Prometheus Metrics

```
GET /metrics - Prometheus metrics endpoint
```

### Log Files

```bash
# Backend logs
docker logs agentic-ai-job-automation-backend-1

# Database logs
docker logs agentic-ai-job-automation-postgres-1
```

## Troubleshooting

### Database Connection Issues

```bash
# Check database status
docker exec postgres psql -U postgres -c "SELECT version();"

# Reset database
docker exec agentic-ai-job-automation-postgres-1 psql -U postgres -d job_automation -c "DROP SCHEMA public CASCADE; CREATE SCHEMA public;"
```

### Redis Connection Issues

```bash
# Check Redis
docker exec redis redis-cli ping

# Flush Redis cache
docker exec redis redis-cli FLUSHALL
```

## Performance Tuning

### Database Optimization

```sql
CREATE INDEX idx_applications_user_id ON applications(user_id);
CREATE INDEX idx_jobs_category ON jobs(category);
CREATE INDEX idx_resumes_user_id ON resumes(user_id);
```

### Caching Strategy

- Job listings cached for 1 hour
- User profiles cached for 30 minutes
- Application status cached for 5 minutes

## Security Considerations

✅ JWT Authentication  
✅ Rate Limiting (100 requests/minute)  
✅ CORS Protection  
✅ SQL Injection Prevention  
✅ XSS Protection  
✅ CSRF Tokens  
✅ Environment Variable Encryption  
✅ Audit Logging  

## Support & Contributing

- Report issues: GitHub Issues
- Documentation: /docs endpoint
- Contributing: See CONTRIBUTING.md

## License

MIT License - See LICENSE file
