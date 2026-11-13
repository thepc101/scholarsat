# 🎓 ScholarSAT

> **The 100% Free, Community-Driven, Open-Source Digital SAT Preparation Platform.**  
> *An open alternative to CookSAT and proprietary prep apps. Built by students, educators, and engineers for the world.*

---

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Next.js](https://img.shields.io/badge/Next.js-15-000000?logo=next.js&logoColor=white)](https://nextjs.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Docker](https://img.shields.io/badge/Docker-Ready-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![Deployment](https://img.shields.io/badge/Hosted_on-Contabo_VPS-0052CC?logo=server&logoColor=white)](https://contabo.com/)

---

## 📖 Table of Contents

- [Vision & Manifesto](#-vision--manifesto)
- [Key Features](#-key-features)
- [System Architecture](#-system-architecture)
- [Tech Stack](#-tech-stack)
- [Getting Started (Local Development)](#-getting-started-local-development)
  - [Prerequisites](#prerequisites)
  - [5-Minute Quickstart](#5-minute-quickstart)
  - [Environment Variables](#environment-variables)
  - [Seeding the Question Bank](#seeding-the-question-bank)
- [BYOK (Bring Your Own Key) AI Architecture](#-byok-bring-your-own-key-ai-architecture)
- [Digital SAT (DSAT) Engine Specifications](#-digital-sat-dsat-engine-specifications)
- [Contabo VPS Production Deployment Guide](#-contabo-vps-production-deployment-guide)
  - [Server Specifications](#server-specifications)
  - [VPS Initial Setup & Hardening](#vps-initial-setup--hardening)
  - [Docker Production Deployment](#docker-production-deployment)
  - [Domain & Automated SSL via Caddy](#domain--automated-ssl-via-caddy)
  - [Automated CI/CD with GitHub Actions](#automated-cicd-with-github-actions)
  - [Database Backups](#database-backups)
- [Question Bank Data Schema & Contribution](#-question-bank-data-schema--contribution)
- [Current Roadmap & Priority Tasks (Where to Contribute Now)](#-current-roadmap--priority-tasks)
- [Contributing & Code Standards](#-contributing--code-standards)
- [License & Community](#-license--community)

---

## 🌟 Vision & Manifesto

Standardized tests like the SAT gatekeep higher education, scholarships, and career opportunities. Yet platforms like **CookSAT**, **ScholarSAT**, UWorld, and Khan Academy alternatives often gate realistic practice exams, question explanations, and smart tools behind expensive subscriptions.

**ScholarSAT** exists to break down that paywall:
1. **Always 100% Free**: No paywalls, no tiers, no locked features.
2. **Community-Owned**: Maintained transparently on GitHub under the MIT License.
3. **Zero-Cost Scalability with BYOK**: AI tutoring features run via **Bring Your Own Key** (OpenAI, Claude, Gemini, DeepSeek, or local Ollama). This ensures the platform incurs **$0 in recurring LLM API inference costs**, keeping hosting lightweight, independent, and indefinitely sustainable.
4. **True-to-Life Bluebook Experience**: Exact UI match to the College Board Digital SAT (adaptive testing, Desmos graphing calculator, highlighter, line references, annotation tool, and timer).
5. **Robust Self-Hosting**: Built to run seamlessly on cost-effective VPS infrastructure like **Contabo**.

---

## ⚡ Key Features

| Feature | Description | Status |
| :--- | :--- | :--- |
| **Realistic DSAT Mock Exams** | Full 2-stage adaptive testing engine (Reading & Writing + Math) with exact College Board scoring models. | 🟡 In Active Dev |
| **Massive Question Bank** | 5,000+ categorized questions filtered by Domain, Skill, Difficulty (Easy, Medium, Hard), and Passage Type. | 🟡 In Active Dev |
| **Desmos Calculator** | Official Desmos Graphing Calculator API embedded directly inside the Math sections. | 🟢 Ready |
| **BYOK AI Tutor** | Step-by-step Socratic explanations, similar question generation, and instant doubt resolution via user-supplied API keys. | 🟡 In Active Dev |
| **Vocab Builder & SRS** | High-frequency SAT vocabulary drill powered by the SuperMemo (SM-2) Spaced Repetition System. | 🟡 In Active Dev |
| **Analytics Dashboard** | Domain mastery heatmaps, pacing breakdowns (seconds spent per question), and score prediction curve. | 🔵 Planned |
| **Offline / PWA Support** | Local offline caching for practice sessions on low-bandwidth connections. | 🔵 Planned |

---

## 🏗️ System Architecture

```mermaid
graph TD
    subgraph Client ["Client Layer (Browser / PWA)"]
        UI[Next.js 15 UI / React 19]
        Desmos[Embedded Desmos API]
        Katex[KaTeX Math Engine]
        BYOK_Store[Local Encrypted Key Storage]
    end

    subgraph AppServer ["Contabo VPS / Docker Stack"]
        Caddy[Caddy Reverse Proxy & Auto-SSL]
        App[Next.js Server / API Routes]
        Postgres[(PostgreSQL 16 DB)]
        Redis[(Redis Cache & Rate Limiter)]
    end

    subgraph AI_Providers ["External LLM Providers (BYOK)"]
        OpenAI[OpenAI - GPT-4o / mini]
        Anthropic[Anthropic - Claude 3.5]
        Gemini[Google Gemini 1.5/2.0]
        DeepSeek[DeepSeek V3 / R1]
        Ollama[Local Ollama / LocalAI]
    end

    UI --> Caddy
    Caddy --> App
    App --> Postgres
    App --> Redis
    UI --> Desmos
    UI --> Katex

    %% BYOK AI Flow: Client initiates requests using user's stored key
    BYOK_Store -.->|User API Key| App
    App -.->|Ephemeral Proxy / Zero Logging| AI_Providers
    UI -.->|Direct Browser Fetch Option| AI_Providers
```

---

## 💻 Tech Stack

- **Framework**: [Next.js 15](https://nextjs.org/) (App Router, React 19, Server Actions)
- **Language**: [TypeScript 5.x](https://www.typescriptlang.org/) (Strict mode enabled)
- **Styling & UI Components**: [Tailwind CSS v4](https://tailwindcss.com/) + [shadcn/ui](https://ui.shadcn.com/) + [Lucide Icons](https://lucide.dev/)
- **Database & ORM**: [PostgreSQL 16](https://www.postgresql.org/) + [Prisma ORM](https://www.prisma.io/) (with connection pooling)
- **Caching & Sessions**: [Redis](https://redis.io/)
- **Math & Chemistry Rendering**: [KaTeX](https://katex.org/)
- **Calculator**: [Desmos API](https://www.desmos.com/api/v1.9/docs/index.html)
- **Authentication**: [Auth.js / NextAuth v5](https://authjs.dev/) (GitHub, Google, Email Magic Link)
- **Deployment & Hosting**: [Contabo Cloud VPS](https://contabo.com/) running [Docker Compose](https://docs.docker.com/compose/) and [Caddy](https://caddyserver.com/)

---

## 🚀 Getting Started (Local Development)

Follow these steps to get your local environment running in under 5 minutes.

### Prerequisites

Ensure you have the following installed on your machine:
- **Node.js**: `v20.x` or `v22.x LTS` ([Download](https://nodejs.org/))
- **pnpm**: `v9.x` (Recommended: `npm install -g pnpm`) or `npm`
- **Docker & Docker Compose**: ([Download Docker Desktop](https://www.docker.com/products/docker-desktop/))
- **Git**: ([Download](https://git-scm.com/))

### 5-Minute Quickstart

#### 1. Clone the Repository
```bash
git clone https://github.com/your-org/scholarsat.git
cd scholarsat
```

#### 2. Install Dependencies
```bash
pnpm install
```

#### 3. Setup Environment Variables
Copy the example environment file:
```bash
cp .env.example .env
```
*(The default `.env.example` comes pre-configured to work out of the box with the local Docker Compose services).*

#### 4. Spin Up Local Services (PostgreSQL & Redis)
```bash
docker compose up -d
```
Verify that the containers are healthy:
```bash
docker compose ps
```

#### 5. Push Database Schema & Seed Initial Data
```bash
# Push Prisma schema to local Postgres
pnpm db:push

# Run database seeds (imports sample SAT questions, vocab decks, and demo user)
pnpm db:seed
```

#### 6. Start the Development Server
```bash
pnpm dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser. You should see the ScholarSAT welcome screen!

---

### Environment Variables

Here is a summary of variables defined in [`.env.example`](.env.example):

| Variable | Description | Default (Local) |
| :--- | :--- | :--- |
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://scholarsat:scholarsat_dev@localhost:5432/scholarsat_db?schema=public` |
| `REDIS_URL` | Redis cache connection string | `redis://localhost:6379` |
| `NEXTAUTH_URL` | NextAuth canonical base URL | `http://localhost:3000` |
| `NEXTAUTH_SECRET` | Secret key for JWT signing | Generate via `openssl rand -base64 32` |
| `DESMOS_API_KEY` | Public Desmos API Key | `dcb31709b452b1cf9dc26972add0fda6` (Demo key) |
| `NEXT_PUBLIC_APP_URL` | Base frontend URL | `http://localhost:3000` |

---

### Seeding the Question Bank

To seed questions or import bulk question JSON dumps:
```bash
# Seed default test dataset (100 questions across Reading, Writing, Math)
pnpm db:seed

# Import from a custom question bank directory
pnpm run import:questions --path=./data/questions/reading-writing.json
```

---

## 🤖 BYOK (Bring Your Own Key) AI Architecture

To keep ScholarSAT 100% free with **zero server inference cost**, we adopt the **BYOK (Bring Your Own Key)** architecture:

### 1. Privacy-First Key Management
- The user provides their own API key (OpenAI, Anthropic, Gemini, DeepSeek, or custom base URL for Ollama).
- Keys are saved **exclusively in the browser's `localStorage`** (with optional local AES encryption using a user passphrase).
- **The ScholarSAT server NEVER stores, logs, or persists user API keys to the database.**

### 2. Supported Providers & Models
- **OpenAI**: `gpt-4o`, `gpt-4o-mini`, `gpt-4-turbo`
- **Anthropic**: `claude-3-5-sonnet`, `claude-3-5-haiku`
- **Google Gemini**: `gemini-1.5-pro`, `gemini-1.5-flash`, `gemini-2.0`
- **DeepSeek**: `deepseek-chat` (V3), `deepseek-reasoner` (R1)
- **Local / Self-Hosted**: `ollama`, `localai`, or any OpenAI-compatible endpoint

### 3. Execution Pipeline
When a student requests an explanation or Socratic hint:
```
[User clicks "Explain with AI"]
       │
       ▼
[Client retrieves user key from localStorage]
       │
       ▼
[Client sends prompt + sanitized question + key via Ephemeral Stream Proxy]
       │
       ▼
[Provider streams response back via Server-Sent Events (SSE)]
       │
       ▼
[Key discarded from memory on stream completion. Zero logging.]
```

To contribute a new AI provider, see [`src/lib/ai/providers/`](src/lib/ai/providers/).

---

## 🎯 Digital SAT (DSAT) Engine Specifications

ScholarSAT accurately replicates the College Board **Bluebook** digital testing experience:

### Section Breakdown
1. **Reading and Writing**:
   - 2 Modules (27 questions each, 32 minutes per module)
   - Short stimulus passages (25–150 words)
   - Domains: Information & Ideas, Craft & Structure, Expression of Ideas, Standard English Conventions
2. **Math**:
   - 2 Modules (22 questions each, 35 minutes per module)
   - Multiple Choice & Student-Produced Responses (Grid-in)
   - Embedded Desmos Graphing Calculator accessible throughout both modules
   - Domains: Algebra, Advanced Math, Problem-Solving & Data Analysis, Geometry & Trigonometry

### Adaptive Testing Logic
- **Stage 1 (Routing Module)**: Balanced mix of easy, medium, and hard questions.
- **Stage 2 (Adaptive Module)**:
  - Higher performance in Module 1 routes student to the **Upper/Hard Module 2** (unlocks score potential 200–800).
  - Lower performance routes student to the **Lower/Easy Module 2** (caps score at ~600).
- Scoring calculations implement standard Item Response Theory (IRT) estimation curves.

### Testing Tools & UI
- **Desmos Graphing Calculator** modal
- **Reference Sheet** popup (formulas for circle, triangle, cone, cylinder, etc.)
- **Strikethrough tool** (eliminate choices A, B, C, D)
- **Highlighter & Notes** on passage text
- **Question Flag for Review**
- **Countdown Timer** with 5-minute warning alert and "Hide Timer" toggle

---

## 🖥️ Contabo VPS Production Deployment Guide

ScholarSAT is optimized to run smoothly on **Contabo Cloud VPS**, providing immense compute and memory at minimal cost.

### Server Specifications
Recommended Contabo Plan:
- **Contabo Cloud VPS M** (or S for lightweight dev):
  - 6 vCPU Cores
  - 16 GB RAM
  - 400 GB NVMe / SSD
  - Ubuntu 24.04 LTS

---

### VPS Initial Setup & Hardening

SSH into your Contabo VPS as `root`:
```bash
ssh root@<YOUR_CONTABO_IP>
```

Run the following initialization commands:

```bash
# 1. Update system packages
apt update && apt upgrade -y

# 2. Create a dedicated deployer user
adduser deployer
usermod -aG sudo deployer

# 3. Configure basic UFW firewall
ufw default deny incoming
ufw default allow outgoing
ufw allow 22/tcp   # SSH
ufw allow 80/tcp   # HTTP
ufw allow 443/tcp  # HTTPS
ufw enable

# 4. Install Docker and Docker Compose
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
usermod -aG docker deployer

# 5. Switch to deployer user
su - deployer
```

---

### Docker Production Deployment

Clone the repo and configure environment on the VPS:

```bash
mkdir -p /home/deployer/apps
cd /home/deployer/apps
git clone https://github.com/your-org/scholarsat.git
cd scholarsat

# Create production environment file
cp .env.example .env.production
nano .env.production
```

Configure your production secrets:
```ini
NODE_ENV=production
NEXT_PUBLIC_APP_URL=https://scholarsat.org
DATABASE_URL=postgresql://scholarsat:STRONG_PASSWORD_HERE@postgres:5432/scholarsat_prod
REDIS_URL=redis://redis:6379
NEXTAUTH_SECRET=GENERATE_WITH_OPENSSL_RAND_BASE64_32
DOMAIN_NAME=scholarsat.org
```

Launch the production stack:
```bash
docker compose -f docker-compose.prod.yml up -d --build
```

Run database migrations inside the running container:
```bash
docker compose -f docker-compose.prod.yml exec web pnpm db:push
docker compose -f docker-compose.prod.yml exec web pnpm db:seed
```

---

### Domain & Automated SSL via Caddy

ScholarSAT includes a pre-configured `Caddyfile`. **Caddy automatically provisions and renews Let's Encrypt SSL certificates.**

Point your domain's DNS `A` record (`scholarsat.org` and `www.scholarsat.org`) to your Contabo VPS IP. Caddy handles the rest automatically!

---

### Automated CI/CD with GitHub Actions

A sample GitHub Actions workflow (`.github/workflows/deploy.yml`) allows zero-downtime automated deployments whenever code is merged to `main`:

```yaml
name: Deploy to Contabo VPS

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy via SSH
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.CONTABO_HOST }}
          username: deployer
          key: ${{ secrets.CONTABO_SSH_PRIVATE_KEY }}
          script: |
            cd /home/deployer/apps/scholarsat
            git pull origin main
            docker compose -f docker-compose.prod.yml build web
            docker compose -f docker-compose.prod.yml up -d --no-deps web
            docker compose -f docker-compose.prod.yml exec -T web pnpm db:push
            docker system prune -f
```

---

### Database Backups

Set up an automated daily backup cron job on the Contabo server:
```bash
crontab -e
```
Add the following line (backs up Postgres daily at 03:00 UTC and keeps 14 days of backups):
```cron
0 3 * * * docker exec scholarsat-postgres pg_dump -U scholarsat scholarsat_prod | gzip > /home/deployer/backups/db_$(date +\%Y\%m\%d_\%H\%M\%S).sql.gz && find /home/deployer/backups -type f -mtime +14 -delete
```

---

## 📊 Question Bank Data Schema & Contribution

Questions are stored in a standard JSON format supporting LaTeX math (`$...$` and `$$...$$`) and Markdown.

### Question JSON Schema Example
```json
{
  "id": "dsat-math-alg-00042",
  "section": "MATH",
  "domain": "Algebra",
  "skill": "Linear equations in two variables",
  "difficulty": "MEDIUM",
  "passage": null,
  "questionText": "A line in the $xy$-plane passes through the point $(0, 4)$ and has a slope of $-\\frac{3}{2}$. What is the equation of the line?",
  "questionType": "MULTIPLE_CHOICE",
  "options": [
    { "id": "A", "text": "$y = -\\frac{3}{2}x + 4$" },
    { "id": "B", "text": "$y = \\frac{3}{2}x + 4$" },
    { "id": "C", "text": "$y = -\\frac{3}{2}x - 4$" },
    { "id": "D", "text": "$y = -\\frac{2}{3}x + 4$" }
  ],
  "correctAnswer": "A",
  "explanation": "The slope-intercept form of a linear equation is $y = mx + b$, where $m$ is the slope and $b$ is the $y$-intercept. Here $m = -\\frac{3}{2}$ and the point $(0, 4)$ indicates $b = 4$. Substituting gives $y = -\\frac{3}{2}x + 4$.",
  "tags": ["slope-intercept", "coordinate-geometry", "sat-prep"]
}
```

### Adding Questions
1. Place question JSON files into `data/questions/<domain>/`.
2. Run validation test: `pnpm test:questions`
3. Ingest into database: `pnpm run import:questions`
4. Submit a Pull Request!

---

## 🗺️ Current Roadmap & Priority Tasks

Here is where contributors can jump in right now:

### 🔥 Immediate Sprints (Phase 1: MVP Core)
- [ ] **Track 1: Exam Interface & Desmos Integration**
  - Implement full-screen DSAT exam layout (`/exam/[examId]`).
  - Integrate Desmos Graphing Calculator API floating window.
  - Implement College Board-style question navigation bar & review screen.
- [ ] **Track 2: Question Ingestion & Bank Viewer**
  - Build question search & filter UI by Domain, Skill, and Difficulty.
  - Write parser/importer script for bulk JSON & Markdown datasets.
- [ ] **Track 3: BYOK AI Tutor Component**
  - Build client-side API key modal with secure local storage.
  - Implement streaming response component with KaTeX rendering for math.
  - Write system prompts for Socratic tutoring and error diagnosis.
- [ ] **Track 4: Vocab SRS System**
  - Implement SM-2 spaced repetition algorithm for vocabulary flashcards.
  - Build interactive flashcard UI with flip animation, audio pronunciation, and example sentences.
- [ ] **Track 5: Adaptive Scoring & Analytics**
  - Code the 2-stage adaptive routing logic (Module 1 -> Easy/Hard Module 2).
  - Implement scaled scoring algorithm (200-800 per section).

---

## 🤝 Contributing & Code Standards

We welcome all contributions! Whether you are writing code, authoring questions, designing UI, or improving documentation, your help is invaluable.

### Branching Convention
- `feat/feature-name`: New features
- `fix/bug-fix-name`: Bug fixes
- `content/question-batch`: Adding questions or vocab decks
- `docs/topic`: Documentation updates

### Commit Message Format (Conventional Commits)
```
feat(exam): embed Desmos calculator modal in math section
fix(scoring): adjust adaptive module 2 routing threshold
content(vocab): add 50 high-frequency words for module 1
docs(readme): add Contabo deployment instructions
```

### Code Style Guidelines
- **TypeScript**: No `any` types. Strict mode must pass (`pnpm type-check`).
- **Formatting**: ESLint and Prettier (`pnpm lint` and `pnpm format`).
- **Components**: Use Tailwind CSS and shadcn/ui primitives. Keep components modular and accessible (ARIA compliant).

Read our full guide in [CONTRIBUTING.md](CONTRIBUTING.md).

---

## 📄 License & Community

- **Code License**: [MIT License](LICENSE) — free for personal, educational, and non-commercial use.
- **Questions & Content**: Released under [Creative Commons Attribution-ShareAlike 4.0 (CC BY-SA 4.0)](https://creativecommons.org/licenses/by-sa/4.0/).
- **Community**: Join our discussion on GitHub Discussions and join the Discord server (link coming soon!).

---

<p align="center">
  <b>Built with ❤️ by the ScholarSAT Open Source Community.</b><br>
  <i>"Education is the most powerful weapon which you can use to change the world."</i>
</p>
