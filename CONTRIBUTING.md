# Contributing to ScholarSAT 🎓

Thank you for your interest in contributing to **ScholarSAT**! ScholarSAT is a 100% free, community-owned, open-source alternative to CookSAT and proprietary SAT prep tools. Every pull request, question authoring, bug report, and architectural improvement directly helps students worldwide.

---

## 🧭 Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can You Help?](#how-can-you-help)
- [Development Setup](#development-setup)
- [Git Workflow & Branching Strategy](#git-workflow--branching-strategy)
- [Commit Message Guidelines](#commit-message-guidelines)
- [Pull Request Process](#pull-request-process)
- [Architecture & Folder Structure](#architecture--folder-structure)
- [Adding Questions & Vocab](#adding-questions--vocab)
- [BYOK AI Guidelines](#byok-ai-guidelines)
- [Testing & Quality Checks](#testing--quality-checks)

---

## 📜 Code of Conduct

We are committed to providing a welcoming, inclusive, and harassment-free environment for everyone. Please be respectful, constructive, and supportive to fellow contributors.

---

## 💡 How Can You Help?

1. **Frontend Engineers**: Replicate the exact College Board Bluebook testing experience, integrate Desmos, build responsive flashcards, and design analytics heatmaps.
2. **Backend & DB Engineers**: Optimize PostgreSQL schema, write question filtering/search queries, build adaptive score routing algorithms, and setup Redis caching.
3. **AI / Prompt Engineers**: Build the BYOK (Bring Your Own Key) prompt system for Socratic explanations, hints, and error diagnosis across OpenAI, Claude, Gemini, and DeepSeek.
4. **Content Authors (Tutors & Students)**: Author original Digital SAT-style questions, write detailed step-by-step explanations, and curate high-frequency vocabulary decks.
5. **DevOps & Infrastructure**: Maintain Contabo VPS deployment scripts, Docker containers, Caddy reverse proxy, and GitHub Actions CI/CD.

---

## 💻 Development Setup

### 1. Prerequisites
- Node.js 20.x or 22.x LTS
- pnpm (recommended) or npm
- Docker & Docker Compose
- Git

### 2. Step-by-Step
```bash
# Fork & clone your fork
git clone https://github.com/<your-username>/scholarsat.git
cd scholarsat

# Install dependencies
pnpm install

# Copy environment variables
cp .env.example .env

# Start local PostgreSQL and Redis containers
docker compose up -d

# Push Prisma schema and seed database
pnpm db:push
pnpm db:seed

# Start the dev server
pnpm dev
```

Visit `http://localhost:3000` to see your local app running.

---

## 🌿 Git Workflow & Branching Strategy

1. Always work from an up-to-date `main` branch.
2. Create a feature branch with a descriptive name:
   - `feat/<feature-name>` (e.g. `feat/desmos-embed`, `feat/byok-gemini-provider`)
   - `fix/<bug-name>` (e.g. `fix/adaptive-module-routing`)
   - `content/<batch-name>` (e.g. `content/math-algebra-questions`)
   - `docs/<topic>` (e.g. `docs/contabo-setup`)
   - `refactor/<target>` (e.g. `refactor/exam-state-machine`)

```bash
git checkout main
git pull upstream main
git checkout -b feat/my-new-feature
```

---

## 📝 Commit Message Guidelines

We enforce **Conventional Commits**:

Format: `<type>(<scope>): <short description>`

### Types
- `feat`: A new user-facing feature
- `fix`: A bug fix
- `content`: New questions, explanations, or vocab data
- `docs`: Documentation changes
- `style`: Formatting, missing semicolons, etc. (no code logic changes)
- `refactor`: Refactoring code without altering external behavior
- `perf`: Performance improvements
- `test`: Adding or updating tests
- `chore`: Build tasks, package updates, dev scripts

### Examples
- `feat(exam): add question flagging and strikethrough tool`
- `fix(auth): resolve session cookie refresh issue on safari`
- `content(math): add 30 advanced math questions with KaTeX formulas`
- `perf(db): add index on question domain and difficulty`

---

## 🚀 Pull Request Process

1. Ensure all checks pass locally before opening a PR:
   ```bash
   pnpm lint
   pnpm type-check
   pnpm test
   ```
2. Open a Pull Request against the `main` branch.
3. Fill out the PR template completely:
   - What problem does this PR solve?
   - Screenshots/video of UI changes (if applicable).
   - How did you test this change?
4. Request review from maintainers or tag relevant contributors.
5. Address review feedback promptly.

---

## 📂 Architecture & Folder Structure

```
scholarsat/
├── .github/
│   └── workflows/          # GitHub Actions CI/CD workflows
├── prisma/
│   ├── schema.prisma       # Database schema (PostgreSQL)
│   └── seed.ts             # Seed scripts for questions & sample users
├── data/
│   ├── questions/          # Question bank JSON/Markdown datasets
│   │   ├── reading-writing/
│   │   └── math/
│   └── vocab/              # SAT vocabulary decks
├── public/                 # Static assets (icons, images)
├── src/
│   ├── app/                # Next.js 15 App Router pages & API routes
│   │   ├── (auth)/         # Login, register, profile
│   │   ├── (dashboard)/    # Student dashboard, analytics, question bank
│   │   ├── exam/           # Fullscreen DSAT exam simulator
│   │   ├── vocab/          # Spaced repetition vocab trainer
│   │   └── api/            # Server endpoints & ephemeral AI proxy
│   ├── components/         # Reusable React components
│   │   ├── exam/           # Desmos, QuestionCard, Timer, ReviewScreen
│   │   ├── ai/             # BYOK modal, AI tutor chat, explanation stream
│   │   ├── vocab/          # Flashcard deck, SM-2 controls
│   │   └── ui/             # shadcn/ui primitives (Button, Dialog, etc.)
│   ├── hooks/              # Custom React hooks (useExam, useBYOK, useTimer)
│   ├── lib/                # Core utilities
│   │   ├── ai/             # Unified BYOK AI client (OpenAI, Claude, Gemini, DeepSeek)
│   │   ├── db.ts           # Prisma client instance
│   │   ├── srs.ts          # SuperMemo SM-2 spaced repetition logic
│   │   └── scoring.ts      # DSAT adaptive routing & IRT scoring logic
│   └── types/              # TypeScript definitions & Zod schemas
├── docker-compose.yml      # Local dev stack (Postgres + Redis)
├── docker-compose.prod.yml # Production Contabo stack
├── Caddyfile               # Caddy reverse proxy config (Auto-SSL)
└── README.md
```

---

## 📚 Adding Questions & Vocab

Content contributions do **not** require deep programming knowledge!

### How to Add Questions:
1. Create or edit a JSON file in `data/questions/<section>/<domain>.json`.
2. Adhere to the question schema:
   - Provide clear LaTeX syntax for math: use `$x$` for inline and `$$\frac{a}{b}$$` for block equations.
   - For Reading/Writing, include high-quality, authentic passages and plausible distractors.
   - Ensure the `explanation` clearly explains **why** the correct option is right and **why** each distractor is wrong.
3. Validate:
   ```bash
   pnpm test:questions
   ```

---

## 🔒 BYOK AI Guidelines

ScholarSAT adheres to strict privacy and zero-cost principles:
- **Never store API keys on the server.** No database columns for API keys.
- **Client-side only.** Keys stay in the user's browser `localStorage` / memory.
- When calling LLMs, use the unified interface in `src/lib/ai/`.
- Prompts must encourage **Socratic learning** (helping the student think through the problem) rather than immediately giving away the answer without guidance.

---

## 🧪 Testing & Quality Checks

Run the following commands before submitting your work:

```bash
# Type check TypeScript
pnpm type-check

# Run linter
pnpm lint

# Run unit and integration tests
pnpm test

# Build production bundle to verify compilation
pnpm build
```

---

*Thank you for helping us democratize SAT preparation for every student!* 🎓
