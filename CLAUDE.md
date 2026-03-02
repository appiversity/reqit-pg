# CLAUDE.md — reqit-pg

## What This Is

This is the **reqit-pg** npm package — PostgreSQL integration for the Reqit product family (Phase 2). It provides the database schema, AST materialization, transitive closure computation, catalog rollover, and query patterns for storing and querying reqit catalogs in PostgreSQL.

## Project Family

Reqit is organized as 4 sibling projects:

| Project | What | Visibility |
|---------|------|-----------|
| **reqit-specs** | Design docs, case studies, strategy, materials | Private |
| **reqit-sdk** | Phase 1 — `reqit` npm package: parser, AST, resolver, auditor, exporters | Open source |
| **reqit-pg** (this repo) | Phase 2 — `reqit-pg` npm package: PostgreSQL schema, materialization, rollover | Open source |
| **reqit-catalog** | Phase 3 — Self-hosted web app: Express 5, Pug, HTMX, Bootstrap 5, PostgreSQL | Open source |

**Dependency chain:** reqit-sdk → reqit-pg → reqit-catalog

All design documents live in `../reqit-specs/design/`. Read `../reqit-specs/design/strategy.md` for the master plan.

## Key Specs

These design documents define what this package implements:

| Spec | What |
|------|------|
| [04-schema.md](../reqit-specs/design/04-schema.md) | Database schema — all tables, FKs, indexes, query patterns |
| [05-queries.md](../reqit-specs/design/05-queries.md) | Common SQL queries against the schema |
| [08-sdk.md](../reqit-specs/design/08-sdk.md) | SDK API — PG-specific sections (materialization, rollover) |

## What This Package Does

- **Schema management** — DDL for all catalog tables (courses, programs, degrees, attainments, requirements)
- **AST materialization** — Store JSON AST requirement trees in the relational schema for querying
- **Transitive closure** — Compute and maintain prerequisite chains for fast reachability queries
- **Catalog rollover** — Copy a catalog from one academic year to the next, preserving stems
- **Query patterns** — Optimized queries for common catalog operations

## Dependencies

- **Runtime:** reqit-sdk (peer dependency)
- **Database:** PostgreSQL

## Critical Constraints

- **Composite FK on all catalog tables:** `FOREIGN KEY (institution, ay) REFERENCES academic_year(institution, ay)`
- **Stems for cross-AY identity.** Every catalog entity has an integer `stem` column.
- **Three representations:** This package handles AST ↔ Relational. reqit-sdk handles Text ↔ AST.
- **FERPA boundary:** Reqit never stores student data.
- **No HTTP/REST.** API layer is reqit-catalog's responsibility.

## What NOT to Do

- Do not store student data
- Do not implement HTTP/REST endpoints — that's reqit-catalog
- Do not duplicate parser/AST logic — depend on reqit-sdk
- Do not support databases other than PostgreSQL
