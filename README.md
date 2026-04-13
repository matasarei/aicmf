# AICMF: AI-integrated Content Management Framework

AICMF is a **"Context-First" CMF** (Content Management Framework) designed as a modern, AI-integrated alternative to classic CMSes. 
In this framework, the filesystem is the editor, the database is the index, and the AI is the primary administrator and developer.

## 🚀 AI Bootup Instructions
If you are an AI agent tasked with initializing or developing this project:
1. **Read `SPEC.md`** for the full technical specification and architectural rules.
2. **Follow the `[AGENT INSTRUCTION SET]`** in `SPEC.md` to bootstrap the environment.
3. **TDD-First Workflow:** Every functional feature (Logic, API, CLI) starts with a test. We prioritize behavioral verification over dogmatic unit coverage.

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

## 🏃 Quick Start (Human)
Once initialized by an agent:
```bash
docker-compose up -d
docker-compose exec app php bin/console app:sync
```
Tests can be run via:
```bash
docker-compose exec app php bin/phpunit
```
