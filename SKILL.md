# FDD — Falsifiability-Driven Development

**Version**: 1.0  
**Origin**: José "El Arkitekt0" Espasa (zDk) — derived from Karl Popper's falsifiability principle  
**Status**: Methodology validated across brAIngram development (101/101 tests, 121/121 SHDD tests)  
**Market**: NO equivalent exists — original methodology  

---

## Overview

FDD (Falsifiability-Driven Development) is a development methodology based on Karl Popper's principle of falsifiability: **a claim is only scientifically valid if it can be proven WRONG**. In software terms: every feature, fix, or assertion must be tested by actively trying to BREAK it before it can be considered correct.

Unlike TDD (which tests expected behavior) or SHDD (which attacks security), FDD focuses on **epistemological validation** — proving that what we believe about our code is TRUE by failing to prove it FALSE.

---

## The Core Principle

> **If you CANNOT falsify a hypothesis, the hypothesis holds.**

This is NOT the same as "tests pass therefore code is correct." FDD requires:

1. **Formulate hypothesis**: "This code does X correctly"
2. **Design falsification test**: Actively try to prove "This code does NOT do X correctly"
3. **Execute falsification**: Run the test attempting to break the code
4. **Result**: If falsification FAILS (code survives), hypothesis holds. If falsification SUCCEEDS (code breaks), fix and re-iterate.

---

## FDD Protocol

### Mandatory Rules (INVIOLABLE)

1. **100% pass or nothing** — 81% is NOT acceptable. Zero-Errors-Team means ZERO errors.
2. **Never ask "should we continue?"** — FDD continues automatically until 100% pass.
3. **>3 iterations minimum** — One pass is insufficient. Validate multiple times with different attack vectors.
4. **Context7 in EVERY iteration** — Consult latest documentation before each validation cycle.
5. **Engram communication constant** — Save before, during, and after each iteration.

### FDD Cycle

```
┌──────────────────────────────────────────────────────────┐
│                    FDD CYCLE                              │
│                                                          │
│  1. HYPOTHESIS  — "Component X handles case Y correctly" │
│        │                                                  │
│        ▼                                                  │
│  2. FALSIFICATION DESIGN                                 │
│     - What input would BREAK this?                        │
│     - What edge case was NOT considered?                  │
│     - What concurrent access pattern?                     │
│     - What adversarial input?                             │
│        │                                                  │
│        ▼                                                  │
│  3. EXECUTE FALSIFICATION                                │
│     - Write test that SHOULD fail if code is wrong        │
│     - Run with -race flag                                 │
│     - Run with fuzzing if applicable                      │
│        │                                                  │
│        ▼                                                  │
│  4. EVALUATE                                             │
│     ┌──────────────────────────────────────┐             │
│     │ Falsification SUCCEEDED (code broke) │ → FIX → goto 1│
│     │ Falsification FAILED (code survived) │ → Hypothesis holds│
│     └──────────────────────────────────────┘             │
│        │                                                  │
│        ▼                                                  │
│  5. ITERATE (minimum 3x with different attack vectors)   │
│        │                                                  │
│        ▼                                                  │
│  6. CONFIRM — Hypothesis validated by falsifiability     │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### Falsification Attack Vectors

| Vector | Question to Answer |
|--------|-------------------|
| **Boundary** | What happens at min/max/zero values? |
| **Concurrency** | What happens with 1000 simultaneous requests? |
| **Adversarial** | What does a malicious actor send? |
| **Missing** | What if required input is absent? |
| **Corrupted** | What if data is malformed/NaN/Inf? |
| **Cross-tenant** | Can user A access user B's data? |
| **Timing** | Can timing attacks reveal secrets? |
| **Resource** | What if memory/CPU/disk is exhausted? |
| **Replay** | Can old requests be replayed? |
| **Overflow** | What if counts exceed MaxInt? |

---

## FDD + TDD + SHDD = Zero-Errors-Team

The triple validation spec combines three complementary methodologies:

| Methodology | Focus | Approach |
|------------|-------|----------|
| **TDD** | Expected behavior | Write tests that define what code SHOULD do |
| **FDD** | Epistemological validation | Try to PROVE that what we believe is WRONG |
| **SHDD** | Security | Attack the system as a malicious actor would |

**TDD** asks: "Does the code do what I intended?"  
**FDD** asks: "Is my belief about correctness FALSIFIABLE and SURVIVING falsification?"  
**SHDD** asks: "Can an adversary break this?"

All three MUST pass for Zero-Errors-Team.

---

## FDD Validation Examples (from brAIngram)

### Example 1: JWT Timing Attack Fix
- **Hypothesis**: "JWT legacy token comparison is timing-safe"
- **Falsification**: Measure response times with correct vs incorrect tokens across 1000 requests
- **Result**: Falsification SUCCEEDED (timing leak detected)
- **Fix**: Replace `==` with `subtle.ConstantTimeCompare`
- **Re-falsification**: Timing leak eliminated → hypothesis now holds

### Example 2: SQLite Multi-tenancy
- **Hypothesis**: "SQLite queries filter by user_id correctly"
- **Falsification**: Insert observation for user A, query as user B
- **Result**: Falsification SUCCEEDED (cross-tenant access)
- **Fix**: Add `WHERE user_id = ?` to ALL SQLite queries
- **Re-falsification**: Cross-tenant access blocked → hypothesis holds

### Example 3: Rate Limiter XFF Spoofing
- **Hypothesis**: "Rate limiter cannot be bypassed via X-Forwarded-For"
- **Falsification**: Send requests with spoofed XFF headers
- **Result**: Falsification SUCCEEDED (bypass possible when trustedProxies configured)
- **Fix**: Only trust XFF from explicitly configured proxy IPs
- **Re-falsification**: Spoofed XFF ignored → hypothesis holds

---

## Integration with SDD

FDD integrates into the SDD (Spec-Driven Development) workflow at the **verify** phase:

```
spec → design → tasks → apply → VERIFY (FDD) → archive
                              ↑
                              └── FDD validates here
```

During `sdd-verify`, the validator must:
1. Load specs and tasks
2. For EACH requirement, formulate a falsification hypothesis
3. Attempt to falsify by testing edge cases, adversarial inputs, and boundary conditions
4. Report: CRITICAL (falsification succeeded) / WARNING (insufficient falsification) / PASS (falsification failed = hypothesis holds)

---

## When to Apply FDD

| Scenario | FDD Required? |
|----------|--------------|
| New feature implementation | YES — falsify every acceptance criterion |
| Bug fix | YES — falsify that the fix actually prevents the bug |
| Security hardening | YES + SHDD — dual validation |
| Configuration change | YES — falsify that old behavior isn't broken |
| Refactoring | YES — falsify that behavior is identical pre/post refactor |
| Documentation update | NO — but verify code matches docs |

---

## Anti-Patterns (What FDD is NOT)

- ❌ FDD is NOT "write more tests" — it's "write tests that TRY TO FAIL"
- ❌ FDD is NOT "coverage percentage" — 100% coverage with no falsification tests is FDD-invalid
- ❌ FDD is NOT "ask the user if we should continue" — FDD continues automatically
- ❌ FDD is NOT "one iteration is enough" — minimum 3x with different attack vectors
- ❌ FDD is NOT "only for security" — it applies to correctness, performance, usability

---

## References

- Karl Popper — *The Logic of Scientific Discovery* (1959)
- Karl Popper — *Conjectures and Refutations* (1963)
- SHDD V2 — github.com/zdknet-0rgon/shdd-security-hacking (BDI + FDD validated)
- brAIngram — 101/101 tests FDD x3 iterations confirmed
