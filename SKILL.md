# FDD — Falsifiability-Driven Development

**Version**: 1.1
**Origin**: José "El Arkitekt0" Espasa (zDk) — derived from Karl Popper's falsifiability principle
**Status**: Methodology validated across brAIngram (101/101), SHDD (291/291), FACADD (47 findings)
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

---

## Falsification Attack Vectors (v1.1 — Expanded)

### Core Vectors

| # | Vector | Category | Question to Answer |
|---|--------|----------|-------------------|
| 1 | **Boundary** | Input | What happens at min/max/zero/empty values? |
| 2 | **Concurrency** | Runtime | What happens with 1000 simultaneous requests? |
| 3 | **Adversarial** | Security | What does a malicious actor send? |
| 4 | **Missing** | Input | What if required input is absent? |
| 5 | **Corrupted** | Input | What if data is malformed/NaN/Inf/null? |
| 6 | **Cross-tenant** | Security | Can user A access user B's data? |
| 7 | **Timing** | Security | Can timing attacks reveal secrets? |
| 8 | **Resource** | Runtime | What if memory/CPU/disk is exhausted? |
| 9 | **Replay** | Security | Can old requests be replayed? |
| 10 | **Overflow** | Input | What if counts exceed MaxInt? |

### Extended Vectors (v1.1)

| # | Vector | Category | Question to Answer |
|---|--------|----------|-------------------|
| 11 | **Injection** | Security | Can SQL/NoSQL/Command injection occur? |
| 12 | **Serialization** | Security | Can deserialized data be crafted maliciously? |
| 13 | **State** | Runtime | What if state is corrupted between operations? |
| 14 | **Order** | Runtime | What if operations execute in wrong order? |
| 15 | **Encoding** | Input | What if data uses unexpected encoding (UTF-8, Latin-1)? |
| 16 | **Permission** | Security | Can unauthorized actions be performed? |
| 17 | **Dependency** | Runtime | What if an external dependency fails? |
| 18 | **Idempotency** | Runtime | Can the same operation be applied twice safely? |
| 19 | **Leakage** | Security | Does error output reveal sensitive information? |
| 20 | **Regression** | Quality | Does the fix break previously working functionality? |

### Vector Selection Matrix

| Component Type | Recommended Vectors |
|---------------|-------------------|
| API Endpoint | 1, 3, 4, 5, 11, 16, 19 |
| Database Layer | 1, 6, 11, 12, 13, 18 |
| Authentication | 3, 6, 7, 9, 16, 19 |
| File Processing | 1, 4, 5, 8, 15 |
| State Machine | 2, 13, 14, 18 |
| External Integration | 2, 8, 17 |
| UI Component | 1, 4, 5, 15 |
| Background Job | 2, 8, 13, 17 |

---

## Hypothesis Template (v1.1 — Standardized)

### Basic Template

```markdown
## FDD Hypothesis

**ID**: FDD-{module}-{number}
**Component**: {file.ts}::{function}
**Hypothesis**: "{Component} handles {case} correctly"
**Priority**: CRITICAL / HIGH / MEDIUM / LOW
**Vectors**: {list of vector numbers from catalog}

### Falsification Plan

| # | Vector | Input | Expected | Falsified? |
|---|--------|-------|----------|------------|
| 1 | {vector} | {specific input} | {expected behavior} | ✅/❌ |
| 2 | {vector} | {specific input} | {expected behavior} | ✅/❌ |
| 3 | {vector} | {specific input} | {expected behavior} | ✅/❌ |

### Verdict
- [ ] CONFIRMED — All falsifications failed (code survived)
- [ ] FALSIFIED — At least one falsification succeeded (code broke)

### Evidence
{test output, screenshots, logs}

### Iterations
- Loop 1 (seed={N}): {result}
- Loop 2 (seed={N}): {result}
- Loop 3 (seed={N}): {result}
- Variability: {X}% — DETERMINISTIC / FLAKY
```

### Advanced Template (with Zod Schema Validation)

```markdown
## FDD Hypothesis — Schema Validation

**ID**: FDD-{module}-{number}
**Component**: {file.ts}::{SchemaName}
**Hypothesis**: "{SchemaName} correctly validates and rejects invalid input"

### Schema Under Test
```typescript
const Schema = z.object({
  field1: z.string().max(256),
  field2: z.number().min(0).max(100),
  field3: z.enum(['a', 'b', 'c']),
});
```

### Falsification Tests

| # | Vector | Input | Expected | Actual | Pass? |
|---|--------|-------|----------|--------|-------|
| 1 | Boundary (min) | `{ field1: '', field2: 0, field3: 'a' }` | Valid | | |
| 2 | Boundary (max) | `{ field1: 'x'.repeat(257), field2: 101, field3: 'd' }` | Invalid | | |
| 3 | Missing | `{}` | Invalid | | |
| 4 | Corrupted | `{ field1: null, field2: 'abc', field3: 123 }` | Invalid | | |
| 5 | Type confusion | `{ field1: 123, field2: 'abc', field3: true }` | Invalid | | |

### Verdict
{CONFIRMED / FALSIFIED}
```

---

## Domain-Specific Validation Examples

### Example 1: Schema Validation (Zod)

**Hypothesis**: "BeliefStateSchema rejects invalid accessLevel values"

```typescript
import { describe, it, expect } from 'vitest';
import { BeliefStateSchema } from '../belief-state.js';

describe('FDD: BeliefState accessLevel validation', () => {
  // Vector 1: Boundary (valid values)
  it('accepts all valid AuthLevel values', () => {
    const levels = ['none', 'recon', 'user', 'authenticated', 'admin', 'system', 'domain_admin', 'full'];
    for (const level of levels) {
      expect(() => BeliefStateSchema.parse({ accessLevel: level, /* ... */ })).not.toThrow();
    }
  });

  // Vector 4: Missing
  it('rejects missing accessLevel', () => {
    expect(() => BeliefStateSchema.parse({})).toThrow();
  });

  // Vector 5: Corrupted
  it('rejects invalid accessLevel', () => {
    expect(() => BeliefStateSchema.parse({ accessLevel: 'invalid' })).toThrow();
    expect(() => BeliefStateSchema.parse({ accessLevel: '' })).toThrow();
    expect(() => BeliefStateSchema.parse({ accessLevel: null })).toThrow();
  });

  // Vector 1: Boundary (type confusion)
  it('rejects non-string accessLevel', () => {
    expect(() => BeliefStateSchema.parse({ accessLevel: 123 })).toThrow();
    expect(() => BeliefStateSchema.parse({ accessLevel: true })).toThrow();
  });
});
```

### Example 2: Security (Scope Enforcement)

**Hypothesis**: "isInScope prevents domain spoofing via dot-boundary bypass"

```typescript
import { describe, it, expect } from 'vitest';
import { matchesPattern } from '../scope-utils.js';

describe('FDD: Scope enforcement — dot-boundary', () => {
  // Vector 3: Adversarial (domain spoofing)
  it('rejects evil-notexample.com for *.example.com', () => {
    expect(matchesPattern('notexample.com', '*.example.com')).toBe(false);
    expect(matchesPattern('evil-example.com', '*.example.com')).toBe(false);
    expect(matchesPattern('example.com.evil.com', '*.example.com')).toBe(false);
  });

  // Vector 1: Boundary (valid subdomain)
  it('accepts sub.example.com for *.example.com', () => {
    expect(matchesPattern('sub.example.com', '*.example.com')).toBe(true);
    expect(matchesPattern('deep.sub.example.com', '*.example.com')).toBe(true);
  });

  // Vector 11: Injection (regex injection)
  it('escapes regex metacharacters in patterns', () => {
    expect(matchesPattern('test', '*.example.com')).toBe(false);
    expect(matchesPattern('a*b', '*.*')).toBe(true); // glob, not regex
  });
});
```

### Example 3: Concurrency (EventLog)

**Hypothesis**: "EventLog append is thread-safe under concurrent access"

```typescript
import { describe, it, expect } from 'vitest';
import { createEventLog, appendEvent, createEvent } from '../event-log.js';

describe('FDD: EventLog concurrency', () => {
  // Vector 2: Concurrency
  it('handles 1000 concurrent appends without data loss', async () => {
    let log = createEventLog('test-session');
    const promises = Array.from({ length: 1000 }, (_, i) =>
      Promise.resolve().then(() => {
        log = appendEvent(log, createEvent('belief_updated', 'test-session', { index: i }));
      })
    );
    await Promise.all(promises);
    expect(log.events).toHaveLength(1000);
  });

  // Vector 13: State corruption
  it('maintains append-only invariant', () => {
    let log = createEventLog('test-session');
    const event1 = createEvent('belief_updated', 'test-session', { a: 1 });
    const event2 = createEvent('belief_updated', 'test-session', { b: 2 });
    log = appendEvent(log, event1);
    log = appendEvent(log, event2);
    expect(log.events[0]).toEqual(event1);
    expect(log.events[1]).toEqual(event2);
  });
});
```

### Example 4: Performance (CVSS Calculation)

**Hypothesis**: "cvssScore completes in <10ms for any valid vector"

```typescript
import { describe, it, expect } from 'vitest';
import { cvssScore } from '../cvss.js';

describe('FDD: CVSS performance', () => {
  // Vector 8: Resource exhaustion
  it('completes in <10ms for worst-case vector', () => {
    const start = performance.now();
    for (let i = 0; i < 1000; i++) {
      cvssScore({ AV: 'N', AC: 'L', AT: 'N', PR: 'N', UI: 'N',
                  VC: 'H', VI: 'H', VA: 'H', SC: 'H', SI: 'H', SA: 'H' },
                CVSS_LOOKUP_GLOBAL_V4);
    }
    const elapsed = performance.now() - start;
    expect(elapsed).toBeLessThan(10000); // 1000 calls in <10s
  });
});
```

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
3. Select appropriate vectors from the catalog
4. Attempt to falsify by testing edge cases, adversarial inputs, and boundary conditions
5. Run 3+ loops with different seeds
6. Report: CRITICAL (falsification succeeded) / WARNING (insufficient falsification) / PASS (falsification failed = hypothesis holds)

---

## When to Apply FDD

| Scenario | FDD Required? | Recommended Vectors |
|----------|--------------|-------------------|
| New feature implementation | YES | 1, 4, 5, 13, 18 |
| Bug fix | YES | Original vector + 20 (regression) |
| Security hardening | YES + SHDD | 3, 6, 7, 9, 11, 16, 19 |
| Configuration change | YES | 1, 4, 5, 20 |
| Refactoring | YES | 1, 2, 13, 14, 18, 20 |
| API endpoint | YES | 1, 3, 4, 5, 11, 16, 19 |
| Database operation | YES | 1, 6, 11, 12, 13, 18 |
| Documentation update | NO | — |

---

## FDD Validation Loops

### Standard Loop Pattern

```bash
# Loop 1: Default order
npx vitest run --reporter=verbose

# Loop 2: Shuffled order (seed=42)
npx vitest run --reporter=verbose --sequence.seed=42

# Loop 3: Shuffled order (seed=123)
npx vitest run --reporter=verbose --sequence.seed=123

# Loop 4: Shuffled order (seed=777)
npx vitest run --reporter=verbose --sequence.seed=777
```

### Result Interpretation

| Pattern | Meaning | Action |
|---------|---------|--------|
| All loops: same pass/fail | Deterministic | ✅ CONFIRMED |
| Different failures per seed | Flaky tests | ❌ INVESTIGATE |
| All loops: same failure | Real bug | 🔧 FIX |
| Performance varies >50% | Race condition | 🔧 FIX |

---

## Anti-Patterns (What FDD is NOT)

- ❌ FDD is NOT "write more tests" — it's "write tests that TRY TO FAIL"
- ❌ FDD is NOT "coverage percentage" — 100% coverage with no falsification tests is FDD-invalid
- ❌ FDD is NOT "ask the user if we should continue" — FDD continues automatically
- ❌ FDD is NOT "one iteration is enough" — minimum 3x with different attack vectors
- ❌ FDD is NOT "only for security" — it applies to correctness, performance, usability
- ❌ FDD is NOT "random fuzzing" — it's targeted falsification with specific vectors

---

## References

- Karl Popper — *The Logic of Scientific Discovery* (1934)
- Karl Popper — *Conjectures and Refutations* (1963)
- Karl Popper — Falsifiability (Wikipedia)
- SHDD V3 — github.com/zdknet-0rgon/shdd-security-hacking (BDI + FDD validated)
- FACADD — github.com/zdknet-0rgon/facadd (4-type forensic audit)
- brAIngram — 101/101 tests FDD x3 iterations confirmed
- Vitest — Property-based testing, edge case testing (Context7)
- Zod 4 — Schema validation, boundary testing (Context7)

---

## License

Privado — zdknet-0rgon. Todos los derechos reservados.
