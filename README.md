# FDD — Falsifiability-Driven Development

**Version**: 1.0
**Origin**: José "El Arkitekt0" Espasa (zDk) — derived from Karl Popper's falsifiability principle
**Status**: Validated across brAIngram (101/101 tests) and SHDD (121/121 tests)
**Market**: NO equivalent exists — original methodology

## What Is FDD?

FDD is a development methodology based on Karl Popper's principle: **a claim is only scientifically valid if it can be proven WRONG**. Every feature, fix, or assertion must be tested by actively trying to BREAK it before it can be considered correct.

Unlike TDD (which tests expected behavior) or SHDD (which attacks security), FDD focuses on **epistemological validation** — proving that what we believe about our code is TRUE by failing to prove it FALSE.

## Triple Spec: TDD + SHDD + FDD = Zero-Errors-Team

| Methodology | Focus | Question |
|------------|-------|----------|
| **TDD** | Expected behavior | "Does the code do what I intended?" |
| **FDD** | Epistemological validation | "Is my belief about correctness FALSIFIABLE and SURVIVING falsification?" |
| **SHDD** | Security | "Can an adversary break this?" |

## FDD Protocol

1. **Formulate hypothesis**: "This code does X correctly"
2. **Design falsification test**: Actively try to prove "This code does NOT do X correctly"
3. **Execute falsification**: Run the test attempting to break the code
4. **Evaluate**: If falsification FAILS (code survives) → hypothesis holds
5. **Iterate**: Minimum 3x with different attack vectors
6. **Confirm**: Hypothesis validated by falsifiability

## Falsification Attack Vectors

| Vector | Question |
|--------|----------|
| Boundary | What happens at min/max/zero values? |
| Concurrency | What happens with 1000 simultaneous requests? |
| Adversarial | What does a malicious actor send? |
| Missing | What if required input is absent? |
| Corrupted | What if data is malformed/NaN/Inf? |
| Cross-tenant | Can user A access user B's data? |
| Timing | Can timing attacks reveal secrets? |
| Resource | What if memory/CPU/disk is exhausted? |
| Replay | Can old requests be replayed? |
| Overflow | What if counts exceed MaxInt? |

## Integration

FDD integrates into SDD at the **verify** phase:

```
spec → design → tasks → apply → VERIFY (FDD) → archive
```

## References

- Karl Popper — *The Logic of Scientific Discovery* (1959)
- SHDD — github.com/zdknet-0rgon/shdd-security-hacking
- FACADD — github.com/zdknet-0rgon/facadd

## License

Privado — zdknet-0rgon.
