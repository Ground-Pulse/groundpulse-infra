### 5. `groundpulse-infra`

```markdown
# GroundPulse Local Environment & Infrastructure (`groundpulse-infra`)

Container configurations, local datastore orchestration, deployment task definitions, and shared environment templates for GroundPulse.

---

## 📌 Work of This Repo
This repository configures and maintains the runtime infrastructure:
- Orchestrates local PostgreSQL 15 and Redis 7 containers via Docker Compose.
- Maintains `.env.example` templates for all backend and frontend microrepositories.
- Stores AWS ECS/Fargate task definitions, Amazon ECR Docker build specifications, and CloudFront/S3 configuration scripts.
- Contains root end-to-end integration and Playwright test orchestrations.

## ❓ Why We Created This Repo
With services and dashboards split across microrepositories, developers need a single location to spin up supporting backing services (databases, Redis queues, local mock stores) without duplicating container configurations inside every repository.

## 🛠 Tech Stack
- **Containerization:** Docker / Docker Compose
- **Datastores:** PostgreSQL 15, Redis 7
- **Cloud Infrastructure:** AWS ECS Fargate, AWS S3, CloudFront CDN
- **E2E Automation:** Playwright

## 📁 File Structure
```text
groundpulse-infra/
├── docker/
│   └── docker-compose.yml
├── env/
│   ├── .env.api.example
│   ├── .env.owner.example
│   └── .env.ops.example
├── e2e/
│   ├── owner-flow.spec.ts
│   ├── inspector-flow.spec.ts
│   └── provider-flow.spec.ts
├── playwright.config.ts
├── package.json
└── README.md
💻 Commands
Bash
# 1. Spin up local database and Redis queues
docker compose -f docker/docker-compose.local.yml up -d

# 2. Check running infrastructure containers
docker compose -f docker/docker-compose.local.yml ps

# 3. Tear down infrastructure containers (preserving volume data)
docker compose -f docker/docker-compose.local.yml down

# 4. Tear down containers and reset databases
docker compose -f docker/docker-compose.local.yml down -v

# 5. Run full end-to-end integration tests (requires apps running)
npx playwright test
🐳 Base Docker Services
YAML
services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgrespassword
      POSTGRES_DB: groundpulse
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  pgdata:
