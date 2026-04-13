# AICMF Specification

## 1. System Architecture
- **Framework:** Symfony Flex (Micro-kernel).
- **Runtime:** PHP 8.2-FPM (Alpine).
- **Concept:** A "Context-First" CMF (alternative to traditional CMS) where the filesystem is the editor.
- **Data Source:** `/content` folder (Markdown + YAML Frontmatter).

## 2. Mandatory File Structure
```plaintext
/
├── bin/console             # Symfony CLI
├── config/                 # Service & Route definitions
├── content/                # Article Markdown files
├── data/                   # SQLite + Cache (Writeable)
├── src/
│   ├── Command/            # app:sync
│   ├── Controller/         # API & Web routes
│   ├── Modules/            # PSR-4 dynamic extensions
│   ├── Repository/         # SQLite access
│   └── Themes/             # Twig templates
├── tests/                  # PHPUnit suites
├── public/                 # Web root
│   ├── index.php
│   ├── .htaccess           # Apache Fallback
│   └── nginx.conf          # Nginx Site Config
├── docker-compose.yml
└── composer.json
```

## 3. Technical Requirements

### A. Indexing Engine (`app:sync`)
1. **Recursion:** Scan `/content` for `.md` files.
2. **Parsing:** Extract YAML Frontmatter (title, date, tags, description) and Markdown body.
3. **Database:** Upsert into `search_index` table (slug, title, content, tags, embedding).
4. **Embeddings:** If `AI_KEY` exists, generate 1536-dim vectors for the content.

### B. Hybrid Search API (`GET /api/search?q=`)
1. **Semantic:** If embeddings are present, use Cosine Similarity.
2. **Fallback:** SQL `LIKE` or FTS5 on `title` and `content`.
3. **Output:** JSON array of matching article metadata.

### C. Infrastructure & Hosting
- **Nginx:** Serve `public/` as root. Try files `$uri /index.php$is_args$args`.
- **Apache:** `.htaccess` must redirect all non-file requests to `index.php`.
- **Permissions:** `data/` and `content/` must be writeable by `www-data`.

## 4. Development Workflow: TDD-First
AICMF follows a TDD-First workflow for all functional logic, commands, and APIs:
1. **Test-First:** Define the requirement via a failing test (Unit, Integration, or Functional).
2. **Implement:** Write the minimum code required to pass the test.
3. **Refactor:** Optimize for readability and performance while maintaining "Green" status.
4. **Validation:** Features are considered complete only when their behavior is verified by the test suite.

## 5. [AGENT INSTRUCTION SET]
1. **Bootstrap:** `git init`. Initialize Symfony Flex project.
2. **Setup:** Install `erusev/parsedown`, `symfony/orm-pack`, `phpunit/phpunit`.
3. **Environment:** Generate `.env.local` with `APP_SECRET`.
4. **Implementation Path:**
   - [TDD] Create `tests/Command/SyncCommandTest.php` -> Implement `app:sync`.
   - [TDD] Create `tests/Controller/SearchControllerTest.php` -> Implement `/api/search`.
   - [TDD] Create `tests/Integration/DatabaseTest.php` -> Setup SQLite Schema.
5. **UI:** Setup Twig in `src/Themes/default/`.
6. **Docker:** Create Alpine-based PHP 8.2-FPM and Nginx containers.
7. **Demo:** Add `/content/articles/hello.md` and run `app:sync`.
