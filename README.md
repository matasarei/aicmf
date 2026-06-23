# AICMF: AI-integrated Content Management Framework

AICMF is a **"Context-First" CMF** (Content Management Framework) designed as a modern, AI-integrated alternative to classic CMSes. 
In this framework, the filesystem is the editor, the database is the index, and the AI is the primary administrator and developer.

## 🚀 AI Bootup Instructions
> ⚠️ This repository **already contains a complete, working implementation**.
> **Clone and run it — do not rebuild it from scratch.** Never run
> `composer create-project` or regenerate `src/`; the code already exists.

If you are an AI agent tasked with setting up or developing this project:
1. **Read `SPEC.md`** for the full technical specification and architectural rules.
2. **Follow the `[AGENT INSTRUCTION SET]`** in `SPEC.md` (§5) — it installs the
   pinned dependencies and starts the stack against the existing codebase.
3. **TDD-First for changes:** When you *extend* the project, every new feature
   (Logic, API, CLI) starts with a test. Existing behaviour is already covered
   by the test suite — keep it green.

## 🧠 Core Philosophy
- **Filesystem as Editor:** Content is stored in `/content` as Markdown files with YAML frontmatter.
- **Database as Index:** A high-performance SQLite index (with optional RAG/Vector support) bridges flat files and the API.
- **Zero UI Admin:** There is no traditional admin panel. The AI agent manages the codebase, schema, and content synchronization.
- **Micro-Kernel Architecture:** Built on Symfony Flex for maximum portability and vertical scalability.

## 🛠 Tech Stack
- **Backend:** PHP 8.2 (Alpine) / Symfony Flex.
- **Database:** SQLite + Vector Embeddings (1536-dim).
- **Search:** Hybrid (Semantic + SQL FTS5).
- **Frontend:** Twig (Hot-loadable themes).
- **Infrastructure:** Docker (PHP-FPM + Nginx).

## 🏃 Quick Start
```bash
git clone https://github.com/matasarei/aicmf.git && cd aicmf
docker-compose build && docker-compose up -d
docker-compose exec php composer install
docker-compose exec php php bin/console app:sync
```
The site is then served at **http://localhost:8081**.

Tests can be run via:
```bash
docker-compose exec php php vendor/bin/phpunit
```
