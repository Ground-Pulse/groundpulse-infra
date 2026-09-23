### 5. `groundpulse-infra`

```markdown
# groundpulse-infra

Infrastructure, Docker orchestration, and deployment automation for GroundPulse[cite: 1].

---

## 🎯 Purpose of This Repo
This repository manages all container orchestration, local infrastructure definitions, CI/CD pipeline configuration, and cloud deployment templates for the GroundPulse platform[cite: 1].

## ❓ Why We Created This Repo
With applications separated into micro-repositories, having a central infrastructure repository guarantees that:
- Developers can spin up all required dependencies (PostgreSQL 15 and Redis 7) locally with a single command[cite: 1].
- Production deployment templates (AWS ECS task definitions, ECR build scripts, and Vercel project configurations) remain version-controlled in one place[cite: 1].
- End-to-end multi-role test suites (Playwright) can test across the API and client applications together[cite: 1].

## 📂 File Structure
```text
groundpulse-infra/
├── docker/
│   ├── docker-compose.local.yml       # Local PostgreSQL 15 & Redis 7 containers
│   └── docker-compose.test.yml        # Ephemeral containers for integration suites
├── e2e/                               # Cross-repo Playwright E2E tests
│   ├── owner-flow.spec.ts             # Register -> Schedule -> Approve repair
│   ├── inspector-flow.spec.ts         # Complete checklist -> Submit report
│   └── admin-provider-flow.spec.ts    # Assign provider -> Complete job
├── terraform/                         # (Optional) AWS ECS, RDS, and S3 provisioning
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
├── scripts/
│   ├── dev-setup.sh                   # Clones and installs all 4 sister repos
│   └── run-all-tests.sh               # Runs test suites across all components
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
