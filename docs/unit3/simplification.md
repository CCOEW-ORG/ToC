# Simplification of CFGs

Simplifying a CFG means removing redundant elements — variables and rules that do not contribute to the language — **without changing `L(G)`**. Simplified grammars are easier to reason about and are required as a precondition for conversion to Normal Forms.

There are three main simplification steps, and they should be applied in the order given here.

---

## Step 1: Eliminate Useless Symbols

A symbol (variable or terminal) is **useless** if it either:

1. **Does not generate any terminal string** (non-generating), or
2. **Is never reachable** from the start symbol `S`.

Both kinds must be removed. Importantly, **remove non-generating variables first**, then remove unreachable symbols — the order matters.

### Removing Non-Generating Variables

A variable `A` is **generating** if `A ⟹* w` for some `w ∈ Σ*` (i.e., it can eventually derive a string of terminals).

**Algorithm:**

1. Mark all variables that directly produce a terminal string (e.g., `A → ab` — `A` is generating).
2. Repeatedly mark variables whose rules produce strings of already-marked variables and terminals.
3. Repeat until no new variables are marked.
4. Any variable not marked is non-generating. Remove it and all rules that contain it.

**Example:**

```
S → AB | a
A → aA | a
B → bC
```

- `A` is generating (`A → a`).
- `S` is generating (`S → a`).
- `C` has no production rules → non-generating.
- `B → bC` contains `C` → `B` cannot derive a terminal string → `B` is non-generating.
- Remove `B`, `C`, and the rule `S → AB`.

Result:
```
S → a
A → aA | a
```

### Removing Unreachable Variables

A variable `A` is **reachable** from `S` if there exists a derivation `S ⟹* αAβ` for some strings `α, β`.

**Algorithm:**

1. Mark `S` as reachable.
2. For each marked variable, mark all variables appearing on the right-hand side of its rules.
3. Repeat until no new variables are marked.
4. Remove all unmarked variables and their rules.

**Example:**

```
S → AB | a
A → aA | a
B → b
C → c
```

- `S` is reachable (start).
- `A` and `B` are reachable (they appear in `S → AB`).
- `C` is never reachable from `S`.
- Remove `C → c`.

Result:
```
S → AB | a
A → aA | a
B → b
```

---

## Step 2: Eliminate ε-Productions

An **ε-production** is any rule of the form `A → ε`. These can cause complications in parsing and are removed in this step.

**Exception:** If `ε ∈ L(G)` (the empty string is in the language), then we keep only the rule `S → ε` for the start symbol. All other ε-productions are removed.

### Algorithm

1. Find all **nullable variables** — variables `A` such that `A ⟹* ε`.
   - `A` is nullable if `A → ε` is a rule, or if `A → B₁B₂…Bₖ` where every `Bᵢ` is nullable.

2. For every rule `A → α` where `α` contains nullable variables, add **new rules** that correspond to each possible subset of nullable variables being absent from `α`.

3. Remove all original ε-productions (except `S → ε` if `ε ∈ L(G)`).

### Example

```
S → AB | a
A → aA | ε
B → b
```

**Step 1:** Nullable variables: `A` (directly, via `A → ε`).

**Step 2:** The rule `S → AB` contains nullable variable `A`. Add `S → B` (with `A` absent).  
The rule `A → aA` contains nullable variable `A`. Add `A → a` (with the second `A` absent; the first `a` stays).

**Step 3:** Remove `A → ε`.

Result:
```
S → AB | B | a
A → aA | a
B → b
```

!!! warning "Do not remove all ε-productions blindly"
    If `ε ∈ L(G)`, you must keep `S → ε`. Removing it would change the language by excluding the empty string.

---

## Step 3: Eliminate Unit Productions

A **unit production** is a rule of the form `A → B` where both `A` and `B` are variables (non-terminals).

Unit productions add no structure — they just rename one variable as another. They can be eliminated by substituting the rules of `B` wherever `A → B` appears.

### Algorithm

For each unit production pair `A → B`:

1. Find all rules `B → α₁ | α₂ | …` (where each `αᵢ` is not a single variable, to avoid infinite loops).
2. Add rules `A → α₁ | α₂ | …` to the grammar.
3. Remove `A → B`.

Repeat until no unit productions remain.

### Example

```
S → A | b
A → B
B → a
```

- `A → B` and `B → a` → add `A → a`, remove `A → B`.
- `S → A` and `A → a` → add `S → a`, remove `S → A`.
- `A` and `B` are now unreachable → can be removed in a subsequent Step 1.

Result:
```
S → a | b
```

!!! warning "What is and is not a unit production"
    - `A → B` is a unit production. Remove it.
    - `A → a` (single terminal) is **not** a unit production. Keep it.
    - `A → u₁Bu₂` is **not** a unit production. Keep it — `B` is embedded in a longer string, not replacing `A` directly.

---

## Complete Simplification Example

**Original grammar:**

```
S → aBC | ε
A → aA | a
B → bC
C → c
D → d
```

**Step 1a (remove non-generating):**
- `A` generates (`A → a`). `C` generates (`C → c`). `D` generates (`D → d`).
- `B → bC`: `C` is generating, so `B` generates.
- `S → aBC`: `B`, `C` generate, so `S` generates.
- All variables are generating. Nothing removed in this substep.

**Step 1b (remove unreachable):**
- `S` is reachable.
- From `S → aBC`: `B`, `C` reachable.
- From `B → bC`: `C` already marked.
- `A` and `D` are never reachable from `S`.
- Remove `A → aA | a` and `D → d`.

After Step 1:
```
S → aBC | ε
B → bC
C → c
```

**Step 2 (remove ε-productions):**
- `S` is nullable (via `S → ε`). Is any other variable nullable? No.
- `ε ∈ L(G)`, so keep `S → ε`.
- No other rules contain `S` on the RHS, so no new rules to add.

After Step 2 (no change except confirmation):
```
S → aBC | ε
B → bC
C → c
```

**Step 3 (remove unit productions):** None present.

**Final simplified grammar:**
```
S → aBC | ε
B → bC
C → c
```

Language: `{ε, abcc}` — only two strings (a very small language!).
