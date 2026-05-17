# FDD — Falsifiability-Driven Development

**Version**: 1.1
**Origin**: José "El Arkitekt0" Espasa (zDk) — derived from Karl Popper's falsifiability principle
**Status**: Validated across brAIngram (101/101), SHDD (291/291), FACADD (47 findings)
**Market**: NO equivalent exists — original methodology

## What Is FDD?

FDD is a development methodology based on Karl Popper's principle: **a claim is only scientifically valid if it can be proven WRONG**. Every feature, fix, or assertion must be tested by actively trying to BREAK it before it can be considered correct.

## Triple Spec: TDD + SHDD + FDD = Zero-Errors-Team

| Methodology | Focus | Question |
|------------|-------|----------|
| **TDD** | Expected behavior | "Does the code do what I intended?" |
| **FDD** | Epistemological validation | "Can I prove this WRONG?" |
| **SHDD** | Security | "Can an adversary break this?" |

## v1.1 — What's New

- **20 Falsification Attack Vectors** (up from 10) — organized by category
- **Standardized Hypothesis Template** — with Zod schema validation variant
- **Domain-Specific Examples** — schema, security, concurrency, performance
- **Vector Selection Matrix** — which vectors for which component types
- **FDD Validation Loops** — standardized 3+ loop pattern with seeds
- **SDD Integration** — formal verify phase integration

## Attack Vector Catalog

| # | Vector | Category |
|---|--------|----------|
| 1 | Boundary | Input |
| 2 | Concurrency | Runtime |
| 3 | Adversarial | Security |
| 4 | Missing | Input |
| 5 | Corrupted | Input |
| 6 | Cross-tenant | Security |
| 7 | Timing | Security |
| 8 | Resource | Runtime |
| 9 | Replay | Security |
| 10 | Overflow | Input |
| 11 | Injection | Security |
| 12 | Serialization | Security |
| 13 | State | Runtime |
| 14 | Order | Runtime |
| 15 | Encoding | Input |
| 16 | Permission | Security |
| 17 | Dependency | Runtime |
| 18 | Idempotency | Runtime |
| 19 | Leakage | Security |
| 20 | Regression | Quality |

## Quick Start

```bash
# Clone
git clone https://github.com/zdknet-0rgon/fdd-falsifiability-driven.git

# Use in your project
# 1. Read SKILL.md for the full methodology
# 2. Select vectors from the catalog
# 3. Write falsification tests
# 4. Run 3+ loops with different seeds
# 5. Verify 0% variability
```

## Repos

| Framework | Repo |
|-----------|------|
| **FDD** | https://github.com/zdknet-0rgon/fdd-falsifiability-driven |
| **SHDD** | https://github.com/zdknet-0rgon/shdd-security-hacking |
| **FACADD** | https://github.com/zdknet-0rgon/facadd |
| **Trident** | https://github.com/zdknet-0rgon/trident-specs |

## License

Privado — zdknet-0rgon.
