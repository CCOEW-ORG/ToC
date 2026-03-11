# Normal Forms: CNF and GNF

Normal forms are standardized formats for CFGs. Converting to a normal form does not change the language generated; it constrains the *shape* of the production rules, which makes certain algorithms (like CYK parsing) much easier to implement and analyze.

---

## Chomsky Normal Form (CNF)

### Definition

A CFG is in **Chomsky Normal Form (CNF)** if every production rule is of exactly one of two forms:

```
A → BC    (two variables)
A → a     (single terminal)
```

where `A`, `B`, `C ∈ V` and `a ∈ Σ`. The exception: the start symbol may have the rule `S → ε` if `ε ∈ L(G)`.

### Why CNF?

- Parse trees in CNF are **binary trees** (each internal node has exactly 2 children or is a leaf).
- The **CYK (Cocke-Younger-Kasami) algorithm** — an efficient `O(n³)` parsing algorithm for CFLs — requires the grammar to be in CNF.
- CNF grammars are easy to reason about for theoretical proofs.

### Converting to CNF

**Preconditions:** First apply all three simplification steps (remove useless symbols, ε-productions, and unit productions).

**Algorithm:**

1. Add a new start symbol `S_new → S`. (Ensures the start symbol never appears on the right-hand side of any rule.)

2. **Eliminate ε-productions** (see Simplification page).

3. **Eliminate unit productions** (see Simplification page).

4. **Convert remaining rules to CNF form** by:
   - For each terminal `a` appearing in a rule with two or more symbols on the RHS, create a new variable `X_a → a` and replace `a` with `X_a` in the longer rule.
   - For each rule `A → B₁B₂B₃…Bₖ` with `k ≥ 3`, break it into a chain of binary rules by introducing new variables.

### Worked Example

**Original grammar:**
```
S → aAB | b
A → a
B → b
```

**Step 1:** Add `S_new → S`.
```
S_new → S
S → aAB | b
A → a
B → b
```

**Step 2:** No ε-productions.

**Step 3:** Eliminate unit production `S_new → S`. Replace with `S_new → aAB | b`.
```
S_new → aAB | b
S → aAB | b           (S is now unreachable — will be removed)
A → a
B → b
```

Remove unreachable `S`:
```
S_new → aAB | b
A → a
B → b
```

**Step 4:** Convert `S_new → aAB` to CNF.
- Create `X_a → a` for the terminal `a`.
- Replace: `S_new → X_a AB`
- `X_a AB` has 3 symbols — introduce `Y → AB`.
- Result: `S_new → X_a Y`

Final CNF:
```
S_new → X_a Y | b
Y → AB
A → a
B → b
X_a → a
```

All rules are now of the form `A → BC` or `A → a`. ✓

### Longer Example

**Original:**
```
S → aAB | bB | ε
A → aA | a
B → b
```

After full simplification (applying Steps 1–3):

- `S → ε` is kept (since `ε ∈ L(G)`).
- No unit productions.
- Remaining: `S → aAB | bB`, `A → aA | a`, `B → b`.

**Convert to CNF:**

For `S → aAB`:
- `a` in a multi-symbol rule → `X_a → a`. Replace: `S → X_a AB`.
- `X_a AB` has 3 symbols → `S → X_a Y₁` and `Y₁ → AB`.

For `S → bB`:
- `b` in a multi-symbol rule → `X_b → b`. Replace: `S → X_b B`.

For `A → aA`:
- `a` in a multi-symbol rule → `S → X_a A` (reuse `X_a`).

Final CNF:
```
S → X_a Y₁ | X_b B | ε
Y₁ → AB
A → X_a A | a
B → b
X_a → a
X_b → b
```

---

## Greibach Normal Form (GNF)

### Definition

A CFG is in **Greibach Normal Form (GNF)** if every production rule is of the form:

```
A → a α
```

where `a ∈ Σ` is a single terminal and `α ∈ V*` is a (possibly empty) string of **variables** only.

Every rule must start with exactly one terminal, followed by zero or more variables.

### Why GNF?

- Every derivation step produces exactly **one terminal**. For a string of length `n`, exactly `n` derivation steps are needed.
- GNF is useful for constructing Pushdown Automata from CFGs and for certain parsing strategies.

### Converting to GNF

The conversion to GNF is more involved than CNF. The key challenge is eliminating **left recursion** — rules of the form `A → Aα`, which have the variable `A` as the first symbol on the right-hand side.

**Informal algorithm:**

1. Ensure all rules are in CNF (or at least simplified).
2. Order the variables `A₁, A₂, …, Aₙ`.
3. For each variable `Aᵢ`, ensure that the first symbol in every RHS of `Aᵢ`'s rules is either a terminal or `Aⱼ` with `j > i` (no "backward" references).
4. Eliminate direct left recursion using the standard transformation.
5. Substitute rules back to ensure every RHS starts with a terminal.

### Worked Example

**Original grammar:**
```
S → AB
A → aA | a
B → b
```

All rules for `A` and `B` already start with a terminal. Only `S → AB` does not.

**Step 1:** Expand `A` into `S`'s rules:

Since `A → aA | a`, substitute:
```
S → aAB | aB        (replacing A with its productions)
```

**Step 2:** Check remaining rules:

```
S → aAB | aB         ✓ starts with 'a'
A → aA | a           ✓ starts with 'a'
B → b                ✓ starts with 'b'
```

All rules already satisfy GNF requirements!

**Final GNF:**
```
S → aAB | aB
A → aA | a
B → b
```

!!! tip "GNF and left recursion"
    If the original grammar has left recursion (e.g., `A → Aa | b`), you first need to eliminate it before converting to GNF. Left recursion elimination uses a standard transformation that introduces a new variable:

    ```
    A → Aa | b
    ```
    becomes:
    ```
    A → bA'
    A' → aA' | ε
    ```

    Then substitute to ensure everything starts with a terminal.

---

## CNF vs. GNF: Summary

| Feature | CNF | GNF |
|---|---|---|
| Rule form | `A → BC` or `A → a` | `A → aα` |
| Parse tree shape | Binary tree | — |
| Steps per derivation | 2n-1 for string of length n | Exactly n |
| Main use | CYK algorithm, theoretical analysis | PDA construction, parsing strategy |
| Handles ε? | Only via `S → ε` | Only via `S → ε` |
