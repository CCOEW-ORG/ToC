# The Chomsky Hierarchy

## Going Beyond Finite Memory

Finite automata are powerful tools, but their defining limitation is that they can only track a *fixed, finite* amount of information. They cannot count to an arbitrary number, match opening and closing brackets, or verify that two halves of a string have equal lengths.

These tasks require more memory. The Chomsky Hierarchy organizes all formal languages into four nested classes, each characterized by the type of machine that recognizes it and the type of grammar that generates it.

---

## The Hierarchy

```
┌─────────────────────────────────────────────────────────┐
│             Recursively Enumerable (Type 0)             │
│   ┌─────────────────────────────────────────────────┐   │
│   │         Context-Sensitive (Type 1)              │   │
│   │   ┌─────────────────────────────────────────┐   │   │
│   │   │       Context-Free (Type 2)             │   │   │
│   │   │   ┌─────────────────────────────────┐   │   │   │
│   │   │   │     Regular (Type 3)            │   │   │   │
│   │   │   └─────────────────────────────────┘   │   │   │
│   │   └─────────────────────────────────────────┘   │   │
│   └─────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

Each inner class is strictly contained within the outer classes. A Type 3 language is also Type 2, Type 1, and Type 0 — but not vice versa.

---

## Summary Table

| Type | Class | Machine | Grammar | Example |
|---|---|---|---|---|
| Type 3 | Regular | Finite Automaton (DFA/NFA) | Regular Grammar | `aⁿ`, strings ending in `ab` |
| Type 2 | Context-Free | Pushdown Automaton (PDA) | Context-Free Grammar | `aⁿbⁿ`, matched brackets |
| Type 1 | Context-Sensitive | Linear Bounded Automaton | Context-Sensitive Grammar | `aⁿbⁿcⁿ` |
| Type 0 | Recursively Enumerable | Turing Machine | Unrestricted Grammar | Halting problem (semi-decidable) |

### What Changes Between Levels?

| Level | Memory Model |
|---|---|
| Regular | No memory beyond current state (finite) |
| Context-Free | A **stack** (last-in, first-out; unbounded depth) |
| Context-Sensitive | A **tape** bounded by input length (linear space) |
| Recursively Enumerable | An **unbounded tape** (full Turing machine) |

---

## Decidability

An important practical distinction:

- **Type 3 (Regular):** Membership is decidable in `O(n)` time (just run the DFA).
- **Type 2 (CFL):** Membership is decidable, e.g., via the CYK algorithm in `O(n³)` time.
- **Type 1 (Context-Sensitive):** Membership is decidable but may require exponential time.
- **Type 0 (RE):** Membership is only *semi-decidable* — a Turing machine will eventually accept strings in the language, but may loop forever on strings not in the language. The halting problem is Type 0.

---

## Why This Course Focuses on Types 3 and 2

Types 3 and 2 are the most directly applicable to software engineering:

- **Type 3 (Regular languages):** Used in lexical analysis (tokenizers, scanners), `grep`, URL patterns, input validation.
- **Type 2 (Context-free languages):** Used in parsing programming languages, XML/HTML validation, compiler front-ends.

Understanding these two levels — their power, their limits, and the machines and grammars that characterize them — is the core goal of Units 1–4 of this course.
