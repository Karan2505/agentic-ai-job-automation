# Setup Guide: Agentic AI Job Application Automation Platform

## Quick Start

### Prerequisites
- Python 3.10+
- Git
- Docker (optional, for containerization)

### Installation

```bash
# Clone the repository
git clone https://github.com/Karan2505/agentic-ai-job-automation.git
cd agentic-ai-job-automation

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Download spacy model
python -m spacy download en_core_web_md
```

## Configuration

### Environment Variables

Create `.env` file in root directory:

```bash
# API Keys
OPENAI_API_KEY=your_key_here
ANTHROPIC_API_KEY=your_key_here
GITHUB_TOKEN=your_token_here
LINKEDIN_TOKEN=your_token_here

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/job_automation
REDIS_URL=redis://localhost:6379

# Logging
LOG_LEVEL=INFO
```

## Project Structure

```
agentic-ai-job-automation/
├── core/                          # Core orchestrator
│   ├── __init__.py
│   └── orchestrator.py            # Main LangGraph orchestrator (220+ lines)
├── agents/                        # Specialized agents
│   ├── __init__.py
│   ├── resume_intelligence.py    # Resume parsing & profile extraction
│   ├── skill_scorer.py           # Skill credibility assessment
│   ├── job_matcher.py            # Job relevance matching
│   ├── portfolio_crawler.py      # GitHub & portfolio analysis
│   ├── application_executor.py   # Playwright web automation
│   ├── cold_outreach.py          # Email generation & sending
│   └── compliance_checker.py     # Risk & compliance validation
├── utils/                         # Utilities
│   ├── security.py               # Encryption & OAuth handling
│   └── logger.py                 # Audit logging
├── models/                        # Data models
│   └── schemas.py                # Pydantic schemas
├── tests/                         # Test suite
│   └── test_all.py              # Unit & integration tests
├── requirements.txt               # Python dependencies (62 packages)
├── .gitignore                     # Git ignore rules
├── SETUP.md                       # This file
└── README.md                      # Project overview
```

## Running the Platform

### Basic Usage

```python
from core.orchestrator import OrchestratorAgent, OrchestratorConfig

# Initialize orchestrator
config = OrchestratorConfig(
    max_retries=3,
    timeout_seconds=300,
    rate_limit_enabled=True,
    human_in_the_loop=True
)

orchestrator = OrchestratorAgent(config)

# Execute workflow
result = orchestrator.run_workflow(
    user_id="user_123",
    resume_path="/path/to/resume.pdf",
    preferences={
        "job_titles": ["Senior Software Engineer"],
        "locations": ["Remote"],
        "min_salary": 150000
    }
)

print(f"Workflow Result: {result}")
```

## Key Features

### 1. Resume Intelligence (Agents)
- PDF/DOCX parsing with text extraction
- Intelligent section parsing (contact, experience, skills, education)
- URL extraction (GitHub, portfolio, LinkedIn)

### 2. Skill Scoring
- NLP-based skill tagging (spaCy)
- Confidence scoring (junior/mid/senior)
- Cross-validation with projects & experience

### 3. Job Matching
- Semantic similarity matching with embeddings
- Multi-factor scoring (skills 40%, experience 30%, location 15%, company 10%, salary 5%)
- Explainable decision-making

### 4. Web Automation
- Playwright-based form filling
- Multi-platform support (LinkedIn, Indeed, Naukri, Uplers)
- CAPTCHA detection and manual review flagging

### 5. Cold Outreach
- Hiring manager identification
- LLM-powered email personalization
- Rate-limiting compliance

### 6. Compliance & Security
- Platform ToS verification
- Rate limit enforcement
- AES-256 encryption for credentials
- OAuth token handling
- Comprehensive audit logging

## Testing

```bash
# Run all tests
pytest tests/ -v --cov

# Run specific test
pytest tests/test_all.py::test_orchestrator -v

# Run with coverage report
pytest tests/ --cov=core --cov=agents --cov-report=html
```

## Docker Deployment

```bash
# Build Docker image
docker build -t agentic-ai-job-automation .

# Run container
docker run -e DATABASE_URL=postgresql://... -e OPENAI_API_KEY=... agentic-ai-job-automation

# Using docker-compose
docker-compose up -d
```

## Monitoring & Logging

- Logs are stored in `logs/` directory
- Audit logs track all application submissions
- Sentry integration for error tracking (optional)
- JSON logging for ELK stack integration

## API Rate Limits

- LinkedIn: 200 actions/day
- Indeed: 300 applications/day
- GitHub API: 5000 requests/hour (authenticated)
- OpenAI: Based on plan

## Security Best Practices

1. **Credentials**: Always use environment variables, never hardcode
2. **Secrets**: Rotate API keys and tokens regularly
3. **PII**: Audit logs never expose sensitive user data
4. **OAuth**: Use official OAuth flows for platform authentication
5. **HTTPS**: Always use TLS/SSL in production

## Troubleshooting

### Issue: Resume parsing fails
**Solution**: Ensure pdfplumber and python-docx are installed. Check file format.

### Issue: GitHub API rate limit exceeded
**Solution**: Add GitHub token to .env. Rate limits increase from 60 to 5000 requests/hour.

### Issue: CAPTCHA detected during application
**Solution**: Application is flagged for human review. Manually complete on the platform.

### Issue: Job matching scores too low
**Solution**: Update skill embeddings. Run skill scorer with updated job descriptions.

## Contributing

See CONTRIBUTING.md for guidelines on submitting improvements and bug reports.

## License

MIT License - See LICENSE file for details

## Support

For issues, questions, or suggestions, please open an issue on GitHub.

---

**Last Updated**: January 2026  
**Status**: Production Ready ✅
