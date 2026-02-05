# AI Vibe Coding — Elite Production Handbook
## First Prompts for Any Project

> **Code is poetry written for machines to execute and humans to admire.**

A comprehensive, battle-tested engineering doctrine for building production-grade software with AI assistance. This document prevents catastrophic failures: hardcoded secrets, missing validation, untested assumptions, code overwrites, junk logic, performance regressions, and hidden security vulnerabilities.

**Use this as a copy-paste prompt bank, checklist system, and release gate enforcement.**

---

## TABLE OF CONTENTS
1. [Why AI Projects Fail](#why-ai-projects-fail)
2. [Master Prompt: Project Auditor](#master-prompt)
3. [Prompt Bank](#prompt-bank)
4. [Project Setup Checklists](#checklists)
5. [Security Patterns & Snippets](#security-patterns)
6. [Performance Optimization](#performance)
7. [DevOps & CI/CD](#devops)
8. [SBOM & Supply Chain Security](#sbom-supply-chain)
9. [Kubernetes Security & Pod Policies](#kubernetes-security)
10. [Cloud Chapters (AWS/GCP/Azure)](#cloud-chapters)
11. [Language-Specific Chapters](#language-chapters)
12. [Breach Postmortems & Lessons](#breach-postmortems)
13. [AI Failure Modes & Defenses](#ai-failures)
14. [Web Scraping Risks & Advanced Defense](#web-scraping)
15. [SRE: SLOs, Error Budgets & Incident Response](#sre)
16. [Database Migrations & Zero-Downtime Deployments](#db-migrations)
17. [Chaos Engineering & Resilience Testing](#chaos-engineering)
18. [Developer Experience & Onboarding](#developer-experience)
19. [Legal, Compliance & Privacy](#legal-compliance)
20. [Release Gates & Non-Negotiables](#release-gates)
21. [Recommended Tools & Commands](#tools)

---

## <a name="why-ai-projects-fail"></a>Why AI-Assisted Projects Fail in Production

AI dramatically accelerates development but amplifies mistakes when guardrails are missing.

**Common failure patterns:**
- **Code overwrites**: AI rewrites working logic with broken code; no diffs enforced
- **Junk logic lines**: AI generates unused variables, dead code, or hallucinated functions
- **Hardcoded secrets**: API keys, database passwords embedded in code pushed to GitHub
- **Missing validation**: User input accepted without sanitization leading to SQL injection
- **Untested assumptions**: Generated code never verified with real tests
- **Performance regressions**: N+1 queries, missing indexes, unbounded loops
- **Exposed APIs**: Public endpoints with no authentication or rate limits
- **No rollback plan**: Breaking changes shipped without safe rollback strategy

**Mitigation mindset**: Humans define constraints, AI generates drafts, automation verifies correctness. Assume compromise, assume scale, assume AI will be wrong.

---

## <a name="master-prompt"></a>MASTER PROMPT: Full Project Auditor

Copy-paste this into your AI tool when you need a complete security, performance, and architecture audit.

```
You are a principal engineer, security auditor, SRE, and compliance officer. 
I will paste code, diffs, or configuration snippets. 

Analyze for:
- Security vulnerabilities (SQL injection, exposed secrets, XSS, CSRF, SSRF, privilege escalation)
- Performance bottlenecks (N+1 queries, unbounded loops, missing indexes, cache misses)
- Architectural flaws (tight coupling, monolithic growth, missing abstraction layers)
- Testing gaps (low coverage, untested edge cases, missing integration tests)
- AI-induced risks (hallucinated APIs, inverted logic, missing error handling)
- Production readiness (no rollback plan, missing observability, unhandled failure modes)

Output MUST be JSON with this exact structure:
{
  "summary": "2-3 sentence overview",
  "risks": [
    {
      "area": "security|performance|cost|maintainability|privacy|legal|accessibility",
      "issue": "short title",
      "severity": "critical|high|medium|low",
      "explanation": "one sentence why this matters",
      "exploit_path": "how attacker could exploit this (if security)",
      "business_impact": "what breaks if this happens"
    }
  ],
  "actionable_fixes": [
    {
      "title": "fix title",
      "priority": 1-5,
      "steps": ["step 1", "step 2"],
      "files_to_change": ["path/file.ts"],
      "code_examples": ["before code", "after code"],
      "test_example": "unit test to verify fix"
    }
  ],
  "tests_to_add": [
    {
      "type": "unit|integration|e2e|fuzz|load|security",
      "description": "what this test verifies",
      "example_command": "shell command to run this test"
    }
  ],
  "ci_changes": ["github action yaml or script"],
  "final_score": {
    "security": 0-10,
    "performance": 0-10,
    "maintainability": 0-10,
    "privacy": 0-10,
    "operational_readiness": 0-10
  },
  "ship_no_ship_verdict": "SHIP|NO_SHIP_FIX_FIRST|CONDITIONAL",
  "remediation_time_estimate": "hours/days to fix critical items",
  "notes": "freeform observations"
}

Be conservative. Flag anything that could be dangerous. 
Provide minimal reproducible fixes and exact shell commands.
```

---

## <a name="prompt-bank"></a>PROMPT BANK (Copy-Paste Ready)

### Prompt 1: Code Review + Refactor
```
You are a senior engineer. Here is a code diff/PR:
[PASTE CODE OR DIFF]

Provide:
1) Short summary (2 sentences)
2) 5 highest priority issues with severity levels
3) 3 refactor suggestions with before/after code snippets
4) List of unit tests to add (title + code skeleton)
5) One-line commit messages for each fix
```

### Prompt 2: Security Audit (Quick)
```
You are an application security engineer. Review these files/configs:
[PASTE FILES]

Provide:
1) Prioritized vulnerabilities (most critical first)
2) CVSS-like severity and exploitability notes
3) Exact shell commands to run local security tests
   - Examples: `sqlmap`, `gobuster`, `nmap`, `snyk test`, `bandit -r .`, `gosec ./...`
4) Commit-ready patch snippets for each fix
5) Automated tools to prevent this class of bug
```

### Prompt 3: Generate Comprehensive Tests
```
Act as a test engineer. For this module:
[PASTE MODULE CODE]

Generate unit tests covering:
1) Happy path (normal operation)
2) Edge cases (boundary conditions, empty inputs, max values)
3) Failure cases (exceptions, network timeouts, permission denied)
4) Mock setup and data fixtures
5) Integration test scenarios if external APIs are involved
```

### Prompt 4: Performance Profiling & Optimization
```
You are a performance engineer. Analyze this service:
[PASTE CODE OR ARCHITECTURE DESCRIPTION]

Provide:
1) Top 7 performance bottlenecks to profile
2) Tools and exact commands to use:
   - Node.js: clinic.js, 0x, autocannon
   - Python: py-spy, scalene, locust
   - Go: pprof, benchstat
   - Database: EXPLAIN ANALYZE, pgBadger
3) Code-level optimization patches (query indexing, caching, concurrency)
4) Load test plan:
   - Tool recommendation (k6, locust, ghz)
   - Target metrics (latency p99, error rate, throughput)
   - Expected baseline before/after optimization
```

### Prompt 5: Web Scraping Ethics, Legal & Defense
```
You are an expert on web scraping legality and technical defense. 
I want to scrape [DOMAIN] for [DATA TYPE].

Provide:
1) Legal considerations:
   - Copyright and terms of service analysis
   - GDPR/CCPA data scraping implications
   - Cease-and-desist risk assessment
2) robots.txt analysis and compliance
3) Ethical scraping configuration:
   - Rate limiting (requests per second)
   - User-Agent headers (identify yourself)
   - Retry strategy with exponential backoff
4) Anti-blocking best practices:
   - Proxy rotation (reputation vs anonymity tradeoff)
   - Residential vs datacenter IPs
   - Session handling and cookie management
5) Defensive robustness:
   - Canary URLs to detect shadow bans
   - Checksum validation for structural changes
   - Data poisoning detection
6) Robust scraping skeleton (Python requests + async):
   - Connection pooling
   - Timeout handling
   - Exponential backoff with jitter
   - Storage (file, database, S3)
7) Alternatives if scraping is disallowed:
   - Official APIs (with pricing)
   - Data partnerships
   - Paid data aggregators
```

### Prompt 6: Secrets & Key Exposure Scanner
```
Scan this repository/code for leaked secrets:
[PASTE CODE OR FILES]

If secrets are found, provide:
1) List of files and line numbers to rotate immediately
2) Step-by-step key rotation for each secret type:
   - AWS access keys (revoke old, create new, update services)
   - Database passwords (rotate in DB, update connection strings)
   - JWT signing keys (re-sign active tokens, update clients)
   - API tokens (revoke on provider, generate new, update configs)
3) Commands to remove secrets from git history:
   - Using git filter-repo: [exact command]
   - Using BFG: [exact command]
4) .gitignore rules to prevent future leaks
5) Pre-commit hook to block obvious patterns:
   - AWS keys (AKIA prefix)
   - RSA private keys (-----BEGIN RSA PRIVATE KEY-----)
   - Generic password patterns (password\s*=)
   - Slack tokens
   - GitHub tokens
```

### Prompt 7: Dependency & Supply-Chain Risk Analysis
```
Analyze these package files for supply-chain risk:
[PASTE package.json / pyproject.toml / go.mod / Gemfile]

Provide:
1) Risky packages (unmaintained, known CVEs, recent security issues):
   - Package name, version, severity
   - Last commit date (if stale)
   - Known vulnerabilities + CVE links
2) Suggested version pinning strategy:
   - Major.minor pinning vs patch-only
   - Dependency ranges that balance updates vs stability
3) Lockfile enforcement:
   - CI step to fail if lockfile is out of sync
   - Commands: npm ci, pip install --require-hashes, go mod verify
4) Automation setup:
   - Dependabot or Renovate configuration
   - Auto-merge criteria (patch only, tests passing)
5) Vulnerability scanning commands:
   - npm audit, npm audit fix
   - pip-audit, pip-audit --fix
   - snyk test, snyk monitor
   - OWASP Dependency-Check
```

---

## <a name="checklists"></a>PROJECT SETUP CHECKLISTS

### ✅ Project Initialization
- [ ] README with setup instructions
- [ ] LICENSE file (MIT, Apache 2.0, or GPL)
- [ ] CONTRIBUTING.md for developer onboarding
- [ ] .gitignore (language-specific + secrets)
- [ ] Branching strategy documented (main, staging, feature branches)
- [ ] Issue & PR templates in `.github/`
- [ ] CI/CD skeleton (GitHub Actions, GitLab CI, or equivalent)
- [ ] Secret management plan (env vars, Vault, KMS)
- [ ] Monitoring & alerting placeholders

### ✅ Code Quality & Maintainability
- [ ] Linter configured (ESLint, Black, golangci-lint)
- [ ] Code formatter enforced (Prettier, autopep8, gofmt)
- [ ] Type checking enabled (TypeScript, mypy, Go strict)
- [ ] Security linting rules active (no hardcoded secrets, obvious vulns)
- [ ] CODEOWNERS file for code review accountability
- [ ] Lockfile checked into git (package-lock.json, Pipfile.lock, go.sum)
- [ ] Pre-commit hooks installed locally
- [ ] Husky / pre-commit framework setup (prevent bad commits)

### ✅ Security (High-Level)
- [ ] HTTPS/TLS enforced (no HTTP in production)
- [ ] HSTS header configured (Strict-Transport-Security)
- [ ] CORS configured (only trusted origins)
- [ ] CSP (Content-Security-Policy) header set
- [ ] Secrets in KMS/Vault, never in code
- [ ] Input validation on all user-facing endpoints
- [ ] SQL queries use parameterized statements
- [ ] Rate limiting configured (auth endpoints, public APIs)
- [ ] Session security (HttpOnly, Secure, SameSite cookies)
- [ ] Password hashing (Argon2 or bcrypt, never plain text)
- [ ] Admin 2FA enabled
- [ ] Weekly dependency scanning (`npm audit`, `pip-audit`, `snyk test`)
- [ ] Pre-commit hook blocks common secrets (AWS keys, PEM files)
- [ ] Git history scanned for old secrets (git filter-repo if found)

### ✅ Privacy & Compliance
- [ ] Data minimization principle (collect only what's needed)
- [ ] PII handling documented (encryption, retention, deletion)
- [ ] Encryption at rest (database, backups, file storage)
- [ ] Privacy policy published and updated
- [ ] User data opt-out & deletion flows implemented
- [ ] GDPR/CCPA compliance checklist completed (if applicable)
- [ ] PCI compliance (if handling payment cards)

### ✅ Testing
- [ ] Unit tests target 80% coverage for core business logic
- [ ] Integration tests for database and external API interactions
- [ ] E2E tests for critical user flows (login, checkout, etc.)
- [ ] SAST/DAST tools in CI (semgrep, snyk, OWASP ZAP)
- [ ] Load tests written (k6, Locust, ghz) with baseline metrics
- [ ] Fuzz testing for input parsers
- [ ] Security testing for auth flows and permission checks

### ✅ Observability & Monitoring
- [ ] Structured logging with correlation IDs
- [ ] Metrics collection (Prometheus, CloudWatch, DataDog)
- [ ] Dashboards for health, performance, errors
- [ ] Error tracking (Sentry, Rollbar, or Cloud Error Reporting)
- [ ] Alerting configured (PagerDuty, OpsGenie, or Slack)
- [ ] Runbooks for common alerts
- [ ] Database backup automated and tested
- [ ] Backup restore procedure documented and rehearsed monthly

### ✅ Deployment & Infrastructure
- [ ] Infrastructure as Code (Terraform, CloudFormation, or equivalent)
- [ ] Immutable container images (Docker)
- [ ] Secrets managed in Vault/Secrets Manager (not in image)
- [ ] Health checks (Kubernetes liveness/readiness probes)
- [ ] Blue/Green or Canary deployment strategy
- [ ] Auto-scaling configured with cost caps
- [ ] Monitoring alerts configured for infrastructure
- [ ] Disaster recovery plan documented
- [ ] Rollback procedure defined and tested

---

## <a name="security-patterns"></a>SECURITY PATTERNS & SNIPPETS

### SQL Injection Prevention
```sql
-- ❌ WRONG - Never concatenate user input
SELECT * FROM users WHERE id = '` + userId + `';

-- ✅ CORRECT - Use parameterized queries
-- Node.js (pg library)
const res = await client.query('SELECT * FROM users WHERE id = $1', [userId]);

-- Python (psycopg2)
cursor.execute('SELECT * FROM users WHERE id = %s', (user_id,))

-- Go (database/sql)
row := db.QueryRow('SELECT * FROM users WHERE id = $1', userID)
```

### Secrets Management
```
❌ WRONG - Storing secrets in code
const API_KEY = "sk_live_1234567890abcdef";

✅ CORRECT - Use environment variables or KMS
const apiKey = process.env.API_KEY;
// OR use AWS Secrets Manager / HashiCorp Vault

If secret is leaked:
1. Rotate the secret immediately
2. Revoke old tokens/keys on provider
3. Remove from git history: git filter-repo --replace-text secrets.txt
4. Run gitleaks to verify removal
5. Notify affected users
```

### Pre-Commit Hook (Block Secrets)
```bash
#!/bin/sh
# .git/hooks/pre-commit

if git grep -n "AKIA\|BEGIN RSA PRIVATE KEY\|password\s*=\|sk_live_\|sk_test_" --cached; then
    echo "🚨 Potential secret found in staged files!"
    echo "Do not commit. Rotate the secret immediately."
    exit 1
fi

# Also prevent large files
for file in $(git diff --cached --name-only); do
    size=$(git cat-file -s :$file 2>/dev/null || echo 0)
    if [ $size -gt 10485760 ]; then  # 10MB limit
        echo "🚨 File too large: $file ($size bytes)"
        exit 1
    fi
done

exit 0
```

### Input Validation (Pydantic + FastAPI)
```python
from pydantic import BaseModel, EmailStr, validator
from fastapi import FastAPI, HTTPException

app = FastAPI()

class User(BaseModel):
    email: EmailStr
    age: int
    
    @validator('age')
    def age_must_be_positive(cls, v):
        if v < 0 or v > 150:
            raise ValueError('Age must be between 0 and 150')
        return v

@app.post("/users")
async def create_user(user: User):
    # FastAPI automatically validates the input against the schema
    return {"status": "created", "user": user}
```

### XSS Prevention (React)
```jsx
// ❌ WRONG - Dangerous HTML injection
function UserBio({ bio }) {
  return <div dangerouslySetInnerHTML={{ __html: bio }} />;
}

// ✅ CORRECT - Escaped by default
function UserBio({ bio }) {
  return <div>{bio}</div>;  // React escapes HTML
}

// For trusted content, use DOMPurify
import DOMPurify from 'dompurify';
function UserBio({ bio }) {
  const cleanHtml = DOMPurify.sanitize(bio);
  return <div dangerouslySetInnerHTML={{ __html: cleanHtml }} />;
}
```

### CSRF Protection
```python
from fastapi import FastAPI, Form
from fastapi_csrf_protect import CsrfProtect

app = FastAPI()

@app.post("/transfer")
async def transfer(csrf_protect: CsrfProtect = Depends()):
    # CSRF token automatically verified from request headers
    # Process transfer
    return {"status": "success"}
```

---

## <a name="performance"></a>PERFORMANCE OPTIMIZATION

### Database Optimization
```sql
-- ❌ SLOW - No index
SELECT * FROM orders WHERE customer_id = 123;

-- ✅ FAST - Indexed column
CREATE INDEX idx_orders_customer_id ON orders(customer_id);
SELECT * FROM orders WHERE customer_id = 123;

-- Always use EXPLAIN ANALYZE to verify
EXPLAIN ANALYZE SELECT * FROM orders WHERE customer_id = 123;

-- Avoid N+1 queries
-- ❌ SLOW
for user in users:
    posts = db.query('SELECT * FROM posts WHERE user_id = ?', user.id)

-- ✅ FAST - Eager load with JOIN or batch query
posts = db.query('''
  SELECT p.* FROM posts p 
  JOIN users u ON p.user_id = u.id 
  WHERE u.id IN (?, ?, ?)
''', user_ids)
```

### Caching Strategy
```python
import redis

redis_client = redis.Redis(host='localhost', port=6379)

def get_user_profile(user_id):
    # Check cache first
    cache_key = f'user_profile:{user_id}'
    cached = redis_client.get(cache_key)
    if cached:
        return json.loads(cached)
    
    # Query database if not cached
    user = db.query(User).filter(User.id == user_id).first()
    
    # Store in cache with 1-hour TTL
    redis_client.setex(cache_key, 3600, json.dumps(user.to_dict()))
    return user.to_dict()

def invalidate_user_cache(user_id):
    redis_client.delete(f'user_profile:{user_id}')
```

### Pagination (Cursor-Based)
```python
@app.get("/posts")
async def list_posts(cursor: Optional[str] = None, limit: int = 20):
    # Cursor-based pagination avoids offset performance issues
    query = db.query(Post).order_by(Post.id.desc())
    
    if cursor:
        query = query.filter(Post.id < int(cursor))
    
    posts = query.limit(limit + 1).all()
    has_more = len(posts) > limit
    posts = posts[:limit]
    
    next_cursor = posts[-1].id if posts else None
    return {
        "posts": posts,
        "next_cursor": next_cursor,
        "has_more": has_more
    }
```

### Background Jobs (Celery)
```python
from celery import Celery

celery_app = Celery('tasks')

# ❌ SLOW - Blocking call in request handler
@app.post("/send-email")
async def send_email(email: str):
    # This blocks the HTTP response!
    send_bulk_email(email, template='welcome')
    return {"status": "sent"}

# ✅ FAST - Async job
@celery_app.task
def send_email_async(email: str):
    send_bulk_email(email, template='welcome')

@app.post("/send-email")
async def send_email(email: str):
    # Fire and forget - returns immediately
    send_email_async.delay(email)
    return {"status": "queued"}
```

### Connection Pooling
```python
from sqlalchemy import create_engine

# Configure connection pool
engine = create_engine(
    'postgresql://user:password@localhost/db',
    pool_size=20,           # max connections in pool
    max_overflow=10,        # max additional connections
    pool_recycle=3600,      # recycle connections after 1 hour
    pool_pre_ping=True      # test connections before use
)
```

---

## <a name="devops"></a>DEVOPS & CI/CD

### GitHub Actions CI Skeleton
```yaml
name: CI

on: [push, pull_request]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
      security-events: write

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Full history for commit analysis

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Type check
        run: npm run type-check

      - name: Test
        run: npm test -- --coverage

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info

      - name: Security scan (semgrep)
        run: npx semgrep --config=auto --json --output=semgrep-report.json .

      - name: Dependency audit
        run: npm audit --audit-level=moderate

      - name: SAST (Snyk)
        run: npx snyk test --json --output=snyk-report.json
        continue-on-error: true

      - name: Build
        run: npm run build

      - name: Build Docker image
        run: |
          docker build -t ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:latest .

      - name: Scan image with Trivy
        uses: aquasecurity/trivy-action@v1
        with:
          image-ref: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:latest
          format: json
          output: trivy-report.json
          exit-code: 1  # Fail if vulnerabilities found

      - name: Upload security reports
        if: always()
        uses: actions/upload-artifact@v3
        with:
          name: security-reports
          path: |
            semgrep-report.json
            snyk-report.json
            trivy-report.json
```

### Dockerfile (Multi-Stage, Best Practices)
```dockerfile
# === Build stage ===
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./

RUN npm ci --only=production

COPY . .

RUN npm run build && \
    npm run test

# === Runtime stage ===
FROM node:20-alpine

WORKDIR /app

# Create non-root user
RUN addgroup -S app && adduser -S app -G app

ENV NODE_ENV=production

COPY --from=builder --chown=app:app /app/dist ./dist
COPY --from=builder --chown=app:app /app/node_modules ./node_modules

USER app

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3000/health', (r) => {if (r.statusCode !== 200) throw new Error(r.statusCode)})"

CMD ["node", "dist/index.js"]
```

### Terraform Infrastructure Template
```hcl
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

# VPC
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name = "main-vpc"
  }
}

# Security Group
resource "aws_security_group" "app" {
  name   = "app-sg"
  vpc_id = aws_vpc.main.id

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "app-sg"
  }
}

# ECS Cluster
resource "aws_ecs_cluster" "main" {
  name = "main-cluster"

  setting {
    name  = "containerInsights"
    value = "enabled"
  }
}

# ECS Service
resource "aws_ecs_service" "app" {
  name            = "app-service"
  cluster         = aws_ecs_cluster.main.id
  task_definition = aws_ecs_task_definition.app.arn
  desired_count   = var.desired_count
  launch_type     = "FARGATE"

  network_configuration {
    subnets          = [aws_subnet.public.id]
    security_groups  = [aws_security_group.app.id]
    assign_public_ip = true
  }
}

# CloudWatch Logs
resource "aws_cloudwatch_log_group" "app" {
  name              = "/ecs/app"
  retention_in_days = 7
}
```

---

## <a name="sbom-supply-chain"></a>SBOM & SUPPLY CHAIN SECURITY

### What is SBOM?
Software Bill of Materials (SBOM) is a complete inventory of all components, libraries, and dependencies in your software. Required for supply chain security and compliance.

### Generate SBOM with Syft
```bash
# Install syft
curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sh -s -- -b /usr/local/bin

# Generate SBOM for current directory
syft packages dir:. -o json > sbom.json

# Generate SBOM for Docker image
syft packages docker:myapp:latest -o spdx-json > sbom-spdx.json

# Generate SBOM in CycloneDX format (industry standard)
syft packages dir:. -o cyclonedx-json > sbom-cyclonedx.json
```

### Sign Artifacts with Cosign (Sigstore)
```bash
# Install cosign
curl -sSfL https://github.com/sigstore/cosign/releases/latest/download/cosign-linux-amd64 -o /usr/local/bin/cosign
chmod +x /usr/local/bin/cosign

# Generate key pair (do once)
cosign generate-key-pair

# Sign container image
cosign sign --key cosign.key ghcr.io/myorg/myapp:v1.0.0

# Verify signature before deployment
cosign verify --key cosign.pub ghcr.io/myorg/myapp:v1.0.0

# Keyless signing (using OIDC - no private key management)
COSIGN_EXPERIMENTAL=1 cosign sign ghcr.io/myorg/myapp:v1.0.0

# Attach SBOM to container image
cosign attach sbom --sbom sbom-cyclonedx.json ghcr.io/myorg/myapp:v1.0.0

# Sign the attached SBOM
cosign sign --attachment sbom ghcr.io/myorg/myapp:v1.0.0
```

### CI Pipeline with SBOM + Signing
```yaml
name: Build, Sign, and Publish

on:
  push:
    tags: ['v*']

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
      id-token: write  # Required for keyless signing

    steps:
      - uses: actions/checkout@v4

      - name: Build Docker image
        run: docker build -t ghcr.io/${{ github.repository }}:${{ github.ref_name }} .

      - name: Generate SBOM
        uses: anchore/sbom-action@v0
        with:
          image: ghcr.io/${{ github.repository }}:${{ github.ref_name }}
          format: cyclonedx-json
          output-file: sbom.json

      - name: Scan SBOM for vulnerabilities
        uses: anchore/scan-action@v3
        with:
          sbom: sbom.json
          fail-build: true
          severity-cutoff: high

      - name: Push image
        run: docker push ghcr.io/${{ github.repository }}:${{ github.ref_name }}

      - name: Sign image (keyless)
        uses: sigstore/cosign-installer@v3
      - run: cosign sign --yes ghcr.io/${{ github.repository }}:${{ github.ref_name }}
        env:
          COSIGN_EXPERIMENTAL: 1

      - name: Attach and sign SBOM
        run: |
          cosign attach sbom --sbom sbom.json ghcr.io/${{ github.repository }}:${{ github.ref_name }}
          cosign sign --yes --attachment sbom ghcr.io/${{ github.repository }}:${{ github.ref_name }}
```

### Hardened CI Runner Security
```yaml
# ❌ WRONG - Public runners with secrets
jobs:
  build:
    runs-on: ubuntu-latest  # Shared infrastructure
    steps:
      - run: echo ${{ secrets.AWS_SECRET_KEY }}  # Exposed in logs!

# ✅ CORRECT - Self-hosted runners for sensitive workloads
jobs:
  build:
    runs-on: self-hosted  # Your controlled infrastructure
    environment: production  # Requires approval
    steps:
      - name: Assume role (no long-lived credentials)
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::123456789:role/GitHubActions
          aws-region: us-east-1

# Runner hardening checklist:
# ✅ Ephemeral runners (destroyed after each job)
# ✅ No persistent storage of secrets
# ✅ Network isolation (no direct internet access)
# ✅ Minimal tooling installed
# ✅ Regular security patching
# ✅ Audit logging enabled
```

### Pre-Commit Configuration with Gitleaks
```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json
      - id: check-added-large-files
        args: ['--maxkb=1000']

  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.0
    hooks:
      - id: gitleaks

  - repo: https://github.com/psf/black
    rev: 23.12.0
    hooks:
      - id: black

  - repo: https://github.com/pycqa/isort
    rev: 5.13.0
    hooks:
      - id: isort

  - repo: https://github.com/pycqa/bandit
    rev: 1.7.6
    hooks:
      - id: bandit
        args: ['-r', '.', '-ll']

# Install: pip install pre-commit && pre-commit install
# Run manually: pre-commit run --all-files
```

---

## <a name="kubernetes-security"></a>KUBERNETES SECURITY & POD POLICIES

### Readiness & Liveness Probes
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: app
          image: ghcr.io/myorg/myapp:v1.0.0
          ports:
            - containerPort: 3000
          
          # Liveness probe - restart container if unhealthy
          livenessProbe:
            httpGet:
              path: /health
              port: 3000
            initialDelaySeconds: 30
            periodSeconds: 10
            timeoutSeconds: 5
            failureThreshold: 3
          
          # Readiness probe - remove from service if not ready
          readinessProbe:
            httpGet:
              path: /ready
              port: 3000
            initialDelaySeconds: 5
            periodSeconds: 5
            timeoutSeconds: 3
            failureThreshold: 3
          
          # Startup probe - for slow-starting containers
          startupProbe:
            httpGet:
              path: /health
              port: 3000
            failureThreshold: 30
            periodSeconds: 10
          
          # Resource limits (REQUIRED for production)
          resources:
            requests:
              memory: "128Mi"
              cpu: "100m"
            limits:
              memory: "512Mi"
              cpu: "500m"
          
          # Security context (REQUIRED)
          securityContext:
            runAsNonRoot: true
            runAsUser: 1000
            readOnlyRootFilesystem: true
            allowPrivilegeEscalation: false
            capabilities:
              drop:
                - ALL
```

### Pod Security Standards (PSS)
```yaml
# Namespace with restricted security (recommended for production)
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    pod-security.kubernetes.io/enforce: restricted
    pod-security.kubernetes.io/enforce-version: latest
    pod-security.kubernetes.io/audit: restricted
    pod-security.kubernetes.io/warn: restricted

---
# Network Policy - deny all by default, allow only required
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-all
  namespace: production
spec:
  podSelector: {}
  policyTypes:
    - Ingress
    - Egress

---
# Allow specific traffic
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-app-traffic
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: myapp
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              name: ingress-nginx
      ports:
        - protocol: TCP
          port: 3000
  egress:
    - to:
        - namespaceSelector:
            matchLabels:
              name: database
      ports:
        - protocol: TCP
          port: 5432
```

### Admission Controllers (OPA Gatekeeper)
```yaml
# Require all containers to have resource limits
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sRequiredResources
metadata:
  name: require-resource-limits
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]
  parameters:
    limits:
      - cpu
      - memory
    requests:
      - cpu
      - memory

---
# Block privileged containers
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sPSPPrivilegedContainer
metadata:
  name: block-privileged
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]

---
# Require approved container registries only
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sAllowedRepos
metadata:
  name: allowed-repos
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]
  parameters:
    repos:
      - "ghcr.io/myorg/"
      - "gcr.io/myproject/"
```

---

## <a name="cloud-chapters"></a>CLOUD CHAPTERS

### AWS — Security & Best Practices
```
🔴 CRITICAL RISKS:

1. IAM is your primary attack surface
   ❌ Root account with access keys exposed
   ✅ Use IAM roles for EC2, Lambda, RDS
   ✅ Enable MFA on all human users
   ✅ Use temporary security credentials

2. S3 exposure (public bucket misconfiguration)
   ❌ Bucket policy allows anonymous access
   ❌ ACLs not set correctly
   ✅ Block all public access at account level
   ✅ Enable versioning and MFA delete
   ✅ Scan with aws s3api list-buckets && aws s3api get-bucket-acl --bucket <name>

3. Instance metadata SSRF (Server-Side Request Forgery)
   ❌ EC2 instance metadata accessible at http://169.254.169.254/latest/meta-data/
   ✅ Use IMDSv2 (token-based, not available from SSRF)
   ✅ Set HttpPutResponseHopLimit to 1

4. Secrets Manager rotation
   ❌ Secrets never rotated
   ✅ Enable automatic rotation (Lambda-based)
   ✅ Audit access with CloudTrail

5. CloudTrail logging
   ❌ Disabled or logs not protected
   ✅ Enable for all regions
   ✅ Log to S3 with MFA delete
   ✅ Monitor suspicious API calls with CloudWatch

6. GuardDuty for threat detection
   ✅ Enable GuardDuty (detects unusual API activity)
   ✅ Configure SNS alerts to security team

7. Service Control Policies (SCPs)
   ✅ Prevent disabling GuardDuty
   ✅ Prevent S3 public access
   ✅ Restrict IAM policy changes
```

### GCP — Security & Best Practices
```
🔴 CRITICAL RISKS:

1. Service account keys exposed
   ❌ Long-lived service account keys with admin permissions
   ✅ Use Workload Identity (pod-to-service-account mapping)
   ✅ Rotate keys every 90 days
   ✅ Use Application Default Credentials (ADC)

2. Default service accounts
   ❌ Using project's default Compute Engine service account
   ✅ Create minimal-privilege custom service accounts
   ✅ Disable default service accounts

3. VPC Service Controls
   ✅ Create security perimeters for sensitive resources
   ✅ Restrict data exfiltration to specific networks

4. Cloud Function ingress
   ❌ Functions publicly accessible without auth
   ✅ Set ingress to "internal" or "require authentication"
   ✅ Use Identity-Aware Proxy (IAP)

5. Audit logging
   ✅ Enable Cloud Audit Logs for all services
   ✅ Export to Cloud Storage with versioning
   ✅ Monitor for suspicious activity

6. Secret Manager rotation
   ✅ Configure automatic rotation with Cloud Functions
   ✅ Audit secret access logs
```

### Azure — Security & Best Practices
```
🔴 CRITICAL RISKS:

1. Azure AD (Entra ID) credential exposure
   ❌ User credentials or service principals exposed
   ✅ Enable Conditional Access policies
   ✅ Require MFA for all privileged accounts
   ✅ Monitor sign-in activity with Sentinel

2. Managed identities (not used)
   ❌ Hardcoded credentials in Key Vault references
   ✅ Use system-assigned or user-assigned managed identities
   ✅ Eliminates credential management

3. Key Vault misconfiguration
   ❌ Publicly accessible Key Vault
   ❌ No Private Endpoint configured
   ✅ Enable Private Endpoint (block public access)
   ✅ Enable purge protection
   ✅ Audit access with Activity Log

4. Storage Account exposure
   ❌ Storage account keys shared or exposed
   ❌ Public blob access enabled
   ✅ Use SAS tokens with expiration
   ✅ Use managed identities for access
   ✅ Block public access

5. Role-Based Access Control (RBAC)
   ✅ Use built-in roles or custom roles with least privilege
   ✅ Regularly review role assignments
   ✅ Remove unused roles monthly
```

---

## <a name="language-chapters"></a>LANGUAGE-SPECIFIC CHAPTERS

### Python + FastAPI Production Patterns

```python
from fastapi import FastAPI, Depends, HTTPException, RateLimiter
from fastapi.security import HTTPBearer, HTTPAuthCredential
from pydantic import BaseModel, validator, EmailStr
from sqlalchemy.orm import Session
import logging
from slowapi import Limiter
from slowapi.util import get_remote_address

app = FastAPI(title="API", version="1.0.0")
limiter = Limiter(key_func=get_remote_address)
logger = logging.getLogger(__name__)

# ✅ Input validation with Pydantic
class UserCreate(BaseModel):
    email: EmailStr
    password: str
    
    @validator('password')
    def password_strong(cls, v):
        if len(v) < 12:
            raise ValueError('Password must be at least 12 characters')
        return v

# ✅ Rate limiting
@app.post("/login")
@limiter.limit("5/minute")
async def login(request: Request, credentials: UserCreate):
    # Maximum 5 logins per minute per IP
    pass

# ✅ Async safety (avoid blocking calls)
@app.get("/users/{user_id}")
async def get_user(user_id: int, db: Session = Depends(get_db)):
    # ❌ WRONG - This blocks!
    # user = db.query(User).filter(User.id == user_id).first()
    
    # ✅ CORRECT - Use async
    user = await db.get(User, user_id)
    if not user:
        raise HTTPException(status_code=404)
    return user

# ✅ Background tasks for heavy operations
from fastapi import BackgroundTasks

@app.post("/process")
async def process_data(background_tasks: BackgroundTasks):
    # Don't block response
    background_tasks.add_task(heavy_operation)
    return {"status": "processing"}

async def heavy_operation():
    logger.info("Heavy operation started")
    # Long-running work here

# ✅ Structured logging with correlation IDs
import uuid

@app.middleware("http")
async def add_correlation_id(request: Request, call_next):
    correlation_id = str(uuid.uuid4())
    request.state.correlation_id = correlation_id
    response = await call_next(request)
    response.headers["X-Correlation-ID"] = correlation_id
    return response

# Tools: pytest, mypy, bandit, pip-audit, locust
```

### Go Microservices Best Practices

```go
package main

import (
    "context"
    "fmt"
    "log"
    "time"
)

// ✅ Explicit error handling
func FetchUser(ctx context.Context, userID int) (User, error) {
    // Always return errors explicitly
    user, err := database.GetUser(ctx, userID)
    if err != nil {
        log.Printf("failed to fetch user: %w", err)
        return User{}, fmt.Errorf("user not found: %w", err)
    }
    return user, nil
}

// ✅ Context propagation (critical for timeouts)
func HandleRequest(w http.ResponseWriter, r *http.Request) {
    ctx, cancel := context.WithTimeout(r.Context(), 5*time.Second)
    defer cancel()
    
    user, err := FetchUser(ctx, 123)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    json.NewEncoder(w).Encode(user)
}

// ✅ Avoid goroutine leaks
func SafeGoroutine() {
    ctx, cancel := context.WithCancel(context.Background())
    defer cancel()  // IMPORTANT - prevents goroutine leak
    
    go func(c context.Context) {
        select {
        case <-c.Done():
            return  // Exit when context is cancelled
        case work := <-workQueue:
            processWork(work)
        }
    }(ctx)
}

// ✅ Circuit breaker pattern
import "github.com/grpc-ecosystem/go-grpc-middleware/retry"

conn, _ := grpc.Dial(
    "service:50051",
    grpc.WithUnaryInterceptor(
        grpc_retry.UnaryClientInterceptor(
            grpc_retry.WithMax(3),
            grpc_retry.WithBackoff(grpc_retry.BackoffLinear(100*time.Millisecond)),
        ),
    ),
)

// Tools: gosec, golangci-lint, pprof, ghz (gRPC load testing)
```

### Mobile (Flutter/Android) Security

```dart
// ✅ Never trust the client - all validation on server

// Store secrets securely
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

const storage = FlutterSecureStorage();
await storage.write(key: 'api_token', value: token);

// ❌ WRONG
const api_key = "sk_live_1234567890";  // Will be extracted

// ✅ CORRECT - Fetch from secure backend
final token = await fetchTokenFromSecureBackend();

// Certificate pinning
import 'package:http/http.dart' as http;

SecurityContext securityContext = SecurityContext.defaultContext;
securityContext.setTrustedCertificates('assets/certificates/cert.pem');

HttpClient httpClient = HttpClient(context: securityContext);

// Runtime integrity check
import 'package:flutter_app_badger/flutter_app_badger.dart';
import 'package:jailbreak_detection/jailbreak_detection.dart';

bool isDeviceCompromised = await JailbreakDetection.androidJailbreakDetected ??
    await JailbreakDetection.iosJailbreakDetected;

if (isDeviceCompromised) {
    // Disable sensitive features or warn user
}
```

---

## <a name="breach-postmortems"></a>BREACH POSTMORTEMS & LESSONS

### Case 1: Uber (2016) — GitHub Secrets Leaked

**What happened**: AWS credentials hardcoded in GitHub repository. Attacker accessed sensitive user data.

**Root cause**: No pre-commit hooks, no secret scanning in CI.

**Lesson**:
```
✅ Add gitleaks to pre-commit and CI
✅ Scan git history immediately after push: git filter-repo --replace-text secrets.txt
✅ Rotate all exposed credentials immediately
✅ Enable CloudTrail logging to detect unauthorized access
✅ Notify affected users
```

### Case 2: Equifax (2017) — Unpatched Dependency

**What happened**: Apache Struts vulnerability (CVE-2017-5638) unpatched for months. Attackers exploited it to exfiltrate 147 million credit files.

**Root cause**: No dependency scanning, slow patch cadence.

**Lesson**:
```
✅ Weekly vulnerability scanning (npm audit, snyk test, pip-audit)
✅ Emergency patch policy for critical CVEs (within 24 hours)
✅ Automated dependency updates (Dependabot)
✅ Fail CI if critical CVE detected
✅ Incident response playbook for security patches
```

### Case 3: Capital One (2019) — Cloud Misconfiguration

**What happened**: WAF misconfigured, SSRF vulnerability allowed attacker to query EC2 metadata service. Exposed credentials led to data exfiltration.

**Root cause**: No infrastructure review, missing IAM controls.

**Lesson**:
```
✅ IaC with strict resource defaults
✅ Minimize EC2 metadata access (IMDSv2)
✅ Restrict IAM roles to least privilege
✅ Regular cloud security audit (CloudTrail review)
✅ Network segmentation and zero-trust architecture
```

### Case 4: SolarWinds (2020) — Supply Chain Compromise

**What happened**: Attacker compromised build system, injected malware into software supply chain affecting 18,000 organizations.

**Root cause**: Weak CI/CD security, no build artifact verification.

**Lesson**:
```
✅ Hardened CI/CD runners (private, no public access)
✅ Reproducible builds and artifact signing (cosign)
✅ SBOM (Software Bill of Materials) generation
✅ Dependency lock files with hashes
✅ Vendor security assessment before integration
```

### Case 5: LastPass (2022) — Encrypted Vault Compromise

**What happened**: Attacker accessed encrypted vaults but couldn't decrypt (well-designed encryption). However, master password weakness would still lead to compromise.

**Root cause**: Client-side vulnerability exposed master password.

**Lesson**:
```
✅ Zero-knowledge architecture (server cannot decrypt)
✅ Key derivation function (KDF) with high iterations
✅ Client-side encryption/decryption only
✅ No plaintext secrets ever transmitted
✅ Encryption algorithm (AES-256 minimum)
```

---

## <a name="ai-failures"></a>AI FAILURE MODES & DEFENSES

### Problem 1: Hallucinated APIs
```
❌ AI invents non-existent methods
const user = await User.findByEmail(email);  // findByEmail doesn't exist

✅ DEFENSE: Require tests
// Test will fail immediately
test('should find user by email', () => {
    expect(() => User.findByEmail('test@example.com')).toThrow();
});

✅ DEFENSE: Verify against documentation
// Check TypeScript types or actual method list
Object.getOwnPropertyNames(User.prototype)
  .filter(name => name.includes('find'))
```

### Problem 2: Code Overwrites
```
❌ AI rewrites working logic with broken code
// Original (correct)
if (user && user.isAdmin) {
    return authorized;
}

// AI's "optimization" (broken)
return user?.isAdmin || false;  // Oops, not equivalent

✅ DEFENSE: Diff-only edits required
// Never accept full file rewrites without review
// Always run tests before merge
// Use code coverage to catch logic changes
```

### Problem 3: Insecure Defaults
```
❌ AI generates code with weak defaults
const server = http.createServer(app);  // No HTTPS!
const cors = { origin: "*" };           // All origins allowed!

✅ DEFENSE: Linting rules
// ESLint rule: no http.createServer without https wrapper
// Auto-fix: require('https').createServer(...)
```

### Problem 4: Junk Logic Lines
```
❌ AI leaves unused variables
function processUser(user) {
    const userName = user.name;  // Never used
    const timestamp = Date.now();  // Never used
    return user.id;
}

✅ DEFENSE: Linter (ESLint no-unused-vars)
// Fails if unused variables exist
// Cleaner code, reduced confusion
```

### Problem 5: Missing Error Handling
```
❌ AI forgets error cases
async function fetchData() {
    const response = await fetch(url);
    return response.json();  // What if network fails?
}

✅ DEFENSE: Type checking + tests
// TypeScript knows fetch can throw
// Test catches missing error handler
async function fetchData() {
    try {
        const response = await fetch(url);
        if (!response.ok) throw new Error(`HTTP ${response.status}`);
        return response.json();
    } catch (error) {
        logger.error('fetch failed', error);
        throw error;
    }
}
```

### Defense Checklist for AI Output
```
☑ Run linter (catches obvious errors)
☑ Run tests (catches logic errors)
☑ Run type checker (catches type mismatches)
☑ Check git diff (catches overwrites)
☑ Security scan (catches vulns)
☑ Manual review (catches subtle issues)
☑ Benchmark (catches performance regressions)
```

---

## <a name="web-scraping"></a>WEB SCRAPING — ADVANCED RISKS & DEFENSE

### Legal & Ethical Risks

```
Before scraping, check:
1. robots.txt - Does it allow scraping?
   curl https://example.com/robots.txt

2. Terms of Service - Explicitly prohibit scraping?

3. Copyright - Who owns the scraped data?

4. GDPR/CCPA - Does scraped data include PII?

5. API availability - Use it instead of scraping

6. Legal precedent - LinkedIn vs hiQ (scraping is legal if data is public)
```

### Technical Risks

```
🚨 RISK 1: Shadow Bans
   Detection: Sudden 403/429 responses
   Defense: Exponential backoff, proxy rotation, rate limiting

🚨 RISK 2: Honeypots
   Definition: Fake URLs crawlers shouldn't follow
   Detection: URLs don't appear in sitemap
   Defense: Respect robots.txt, verify URLs

🚨 RISK 3: Silent DOM Changes
   Detection: Checksum mismatch or parse errors
   Defense: Validate structure before processing

🚨 RISK 4: Data Poisoning
   Definition: False/malicious data injected
   Defense: Validate data against schema, cross-reference

🚨 RISK 5: IP Reputation Damage
   Detection: Slow degradation of response quality
   Defense: Residential proxies, rate limiting, user-agent rotation
```

### Robust Scraper Skeleton (Python)

```python
import requests
import time
from datetime import datetime
import json
import hashlib
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

class RobustScraper:
    def __init__(self, max_retries=5, backoff_factor=2):
        self.session = requests.Session()
        
        # Retry strategy with exponential backoff
        retry_strategy = Retry(
            total=max_retries,
            backoff_factor=backoff_factor,
            status_forcelist=[429, 500, 502, 503, 504],
            method_whitelist=["GET"]
        )
        
        adapter = HTTPAdapter(max_retries=retry_strategy)
        self.session.mount("http://", adapter)
        self.session.mount("https://", adapter)
        
        self.session.headers.update({
            "User-Agent": "Mozilla/5.0 (compatible; YourScraper/1.0 +https://yourdomain.com)"
        })
        
        self.data_cache = {}
        self.checksums = {}
    
    def fetch(self, url, timeout=10):
        """Fetch with retries and timeout"""
        try:
            response = self.session.get(url, timeout=timeout)
            response.raise_for_status()
            return response
        except requests.exceptions.RequestException as e:
            print(f"Failed to fetch {url}: {e}")
            return None
    
    def validate_structure(self, html, expected_elements):
        """Detect shadow bans or DOM changes"""
        checksum = hashlib.md5(html.encode()).hexdigest()
        
        if url in self.checksums:
            if checksum != self.checksums[url]:
                print(f"⚠ Structure changed for {url}")
                return False
        
        self.checksums[url] = checksum
        return True
    
    def scrape_with_backoff(self, urls, delay=1):
        """Scrape with rate limiting"""
        for url in urls:
            response = self.fetch(url)
            if response:
                if self.validate_structure(response.text, []):
                    print(f"✓ Scraped {url}")
                    yield response
            
            time.sleep(delay)  # Respect server

# Usage
scraper = RobustScraper()
for response in scraper.scrape_with_backoff(['https://example.com']):
    print(response.status_code)
```

---

## <a name="release-gates"></a>RELEASE GATES & NON-NEGOTIABLES

A release MUST fail if any of these are true:

```yaml
Release Gates:
  security_score: ❌ if < 8/10
  test_coverage: ❌ if < 80%
  secrets_detected: ❌ if any found
  critical_vulnerabilities: ❌ if any exist
  performance_regression: ❌ if > 10% slower
  no_rollback_plan: ❌ if undefined
  database_migration_untested: ❌ if migration not tested
  unreviewed_code: ❌ if CODEOWNERS not approved
  
Release Actions:
  - All secrets must be rotated immediately
  - All critical CVEs must be patched
  - All tests must pass locally and in CI
  - All code must be reviewed by senior engineer
  - Rollback plan must be written and rehearsed
  - Monitoring alerts must be configured
  - Incident response team must be on-call
```

---

## <a name="tools"></a>RECOMMENDED TOOLS & COMMANDS

### Linting & Code Quality
```bash
# JavaScript/TypeScript
npx eslint . --fix
npx prettier --write .

# Python
black . --line-length 100
isort . --profile black
pylint **/*.py
mypy . --strict

# Go
gofmt -w .
golangci-lint run ./...
```

### Security Scanning
```bash
# Dependency vulnerabilities
npm audit
pip-audit
snyk test

# Secrets scanning
gitleaks detect
truffleHog git https://github.com/org/repo.git

# SAST (Static Application Security Testing)
semgrep --config=auto .
bandit -r .
gosec ./...

# DAST (Dynamic Application Security Testing)
zaproxy start --start-options '-config api.key=1234'
burp pro

# Container scanning
trivy image myapp:latest
```

### Performance Testing
```bash
# Load testing
k6 run load-test.js
locust -f locustfile.py

# Benchmarking
npm run benchmark
go test -bench=. -benchmem
ab -c 100 -n 1000 https://example.com

# Profiling
node --prof app.js
py-spy record -o profile.svg python app.py
go tool pprof cpu.prof
```

### Infrastructure & Deployment
```bash
# Terraform
terraform validate
terraform plan
terraform apply

# Docker
docker build -t app:latest .
docker run --rm -it app:latest

# Kubernetes
kubectl apply -f manifests/
kubectl get pods -w
kubectl logs -f deployment/app
```

---

## FINAL ENGINEERING DOCTRINE

```
Speed without safety is chaos.
Automation is leverage.
Humans define policy, machines enforce it.

Move fast, but lock doors.
Assume compromise.
Assume scale.
Assume failure.

Design for humans, attackers, and tomorrow's scale.

Every line of code is a liability.
Every dependency is a risk.
Every permission is a threat.

Test ruthlessly.
Monitor continuously.
Rollback immediately.

This is production engineering.
```

---

## HOW TO USE THIS DOCUMENT

1. **During Development**: Copy relevant prompts and run against your PRs
2. **During Review**: Use checklists to verify completeness
3. **In CI/CD**: Inject Master Prompt and automated scanners
4. **Before Release**: Execute release gate checklist
5. **Post-Incident**: Review breach postmortem relevant to your failure
6. **Ongoing**: Update tool versions and add new attack patterns discovered

**Last Updated**: February 2026
**Status**: Production-Ready Handbook
**Version**: 1.0 Elite Edition
