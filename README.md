# AICMF — AI-integrated Content Management Framework

![License: MIT](https://img.shields.io/badge/license-MIT-2de2e6)
![PHP 8.2+](https://img.shields.io/badge/php-8.2%2B-777bb4)
![Symfony 7](https://img.shields.io/badge/symfony-7.x-000000)
![SQLite](https://img.shields.io/badge/database-SQLite-003b57)
![Tests: PHPUnit](https://img.shields.io/badge/tests-PHPUnit-3c9cd7)

**AICMF** is a **"Context-First" CMF** (Content Management Framework) — a modern,
AI-first alternative to classic CMSes like WordPress and to headless CMS platforms.
The filesystem is the editor, the database is a throwaway index, and an **AI agent
is the primary administrator and developer**: flat-file Markdown content, a
rebuildable SQLite search index with optional vector embeddings (RAG-ready), and
zero admin UI.

**Live showcase:** [hcnotes.cc](https://hcnotes.cc) — a real production site built
on AICMF ([source](https://github.com/matasarei/hcnotes)).

## 🚀 AI Bootup Instructions
> ⚠️ This repository **already contains a complete, working implementation**.
> **Clone and run it — do not rebuild it from scratch.** Never run
> `composer create-project` or regenerate `src/`; the code already exists.

If you are an AI agent tasked with setting up or developing this project:
1. **Read [`AGENTS.md`](AGENTS.md)** for the working rules (container-first,
   TDD-first, what to touch and what not to).
2. **Read [`SPEC.md`](SPEC.md)** for the full technical specification and the
   `[AGENT INSTRUCTION SET]` (§5) — it installs the pinned dependencies and
   starts the stack against the existing codebase.
3. **TDD-First for changes:** When you *extend* the project, every new feature
   (logic, API, CLI) starts with a test. Existing behaviour is already covered
   by the test suite — keep it green.

## 🧠 Core Philosophy
- **Filesystem as Editor:** Content lives in `/content` as Markdown files with
  YAML frontmatter — versionable, greppable, AI-editable.
- **Database as Index:** A high-performance SQLite index (with optional
  RAG/vector-embedding support) bridges flat files and the API. Delete it and
  re-sync any time — it's a derived artifact, never a source of truth.
- **Zero UI Admin:** No admin panel, no plugin marketplace. The AI agent manages
  the codebase, schema, and content synchronization.
- **Micro-Kernel Architecture:** Built on Symfony Flex for maximum portability
  and vertical scalability. No build step, no npm.

## 🛠 Tech Stack
- **Backend:** PHP 8.2 (Alpine) / [Symfony 7](https://symfony.com) Flex micro-kernel
- **Content:** Markdown + YAML frontmatter, parsed with Parsedown
- **Database:** SQLite + optional vector embeddings (1536-dim)
- **Search:** Hybrid — semantic (cosine similarity) with SQL full-text fallback
- **Templating:** Twig (hot-loadable themes in `src/Themes/`)
- **SEO:** live `/sitemap.xml` rendered from the index, `robots.txt`,
  per-page meta descriptions
- **Infrastructure:** Docker (PHP-FPM + Nginx), Apache `.htaccess` fallback

## 🏃 Quick Start
```bash
git clone https://github.com/matasarei/aicmf.git && cd aicmf
docker compose up -d --build
docker compose exec php composer install
docker compose exec php php bin/console app:sync   # index the content
```
The site is then served at **http://localhost:8081**. It boots in `dev` out of
the box — `.env` ships a dummy secret, so no extra setup is needed locally.

Run the tests with:
```bash
docker compose exec php php vendor/bin/phpunit
```

## ⚙️ Configuration & Production
Configuration is read by Symfony from `.env` (committed dev defaults) and
`.env.local` (your overrides, git-ignored). `docker-compose` does **not** inject
`APP_ENV`, `APP_SECRET`, or `DATABASE_URL` as container env vars — real
environment variables would take precedence over `.env.local` and prevent
switching to production. `.env.local` is the single source of truth on a server.

```bash
cp .env.local.example .env.local   # set APP_ENV=prod, a real APP_SECRET, SITE_BASE_URL
docker compose up -d --build
docker compose exec php composer install --no-dev --optimize-autoloader
docker compose exec php php bin/console cache:clear --env=prod
docker compose exec php php bin/console app:sync
```

On a server, `make update` (see [`Makefile`](Makefile)) pulls the latest code,
clears the cache, and re-indexes content in one run — works with both Docker
and Podman.

### Search-engine indexing
Crawlers see the site through `public/robots.txt` and a dynamic `/sitemap.xml`
rendered straight from the SQLite index, so every `app:sync` refreshes the
sitemap automatically. Sitemap links are built from `SITE_BASE_URL` — set it to
your public https origin in `.env.local` (behind a TLS proxy the container only
sees plain HTTP, so the request scheme can't be trusted). In `dev` mode Symfony
adds `X-Robots-Tag: noindex` on its own, which keeps localhost out of the index.

## ✍️ Writing Content
Drop a Markdown file into `content/articles/` with frontmatter, then re-index:

```markdown
---
title: My Post
date: 2026-08-20
description: One-line summary (used for SEO meta description).
tags: [tag-a, tag-b]
---

Body in Markdown…
```

```bash
docker compose exec php php bin/console app:sync
```

## 📄 License
Released under the [MIT License](LICENSE) © Yevhen Matasar.
