# spec-kit — CLAUDE.md

MagnonOS mirror of the Spec Kit open-source toolkit for specification-driven development.
Part of MagnonOS Project-Aethra.

## Purpose

Magnon's mirror of the upstream spec-kit toolkit. Provides the CLI for creating, validating,
and managing technical specifications across all MagnonOS Project-* repos. See
`magnon-spec-kit` for the MagnonOS-extended version with constitution `@include` support.

## Tech stack

- See `docs/` and upstream documentation for CLI reference
- Node.js or Python-based (see package files)

## Dev commands

```bash
# See AGENTS.md and docs/ for full usage
spec-kit --help
spec-kit validate specs/
spec-kit new SPEC-ORG-NNN my-spec-title
```

## Key conventions

- Use `magnon-spec-kit` (not this repo) for MagnonOS-specific spec authoring with constitution support.
- Upstream contributions go to the upstream spec-kit repository.
- This mirror is updated when upstream releases new versions.

## What NOT to do

- Never add Magnon-specific changes to this mirror — they go in `magnon-spec-kit`.
- Never use `ubuntu-latest` CI runners — always `magnon-enterprise-runners`.

## CI

All GitHub Actions workflow jobs use `runs-on: magnon-enterprise-runners`.
