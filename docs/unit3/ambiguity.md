# Ambiguity in CFGs

## Definition

A CFG `G` is **ambiguous** if there exists at least one string `w ∈ L(G)` that has two or more distinct **parse trees**.

Equivalently, `G` is ambiguous if some string has two or more distinct **leftmost derivations** (or two or more distinct rightmost derivations — these are equivalent conditions).

!!! warning "Distinct derivations ≠ distinct parse trees"
    A single parse tree can correspond to many different derivations (depending on the order you expand non-terminals). What matters for ambiguity is whether the *tree structure* — the hierarchical grouping — is unique.

    - Unique parse tree **does not** imply unique derivation.
    - **Two or more** distinct parse trees **does** imply ambiguity.

### Why Ambiguity Matters

Ambiguous grammars are problematic in practice because the parse tree encodes the *meaning* of the string (e.g., the order of operations in an arithmetic expression). Multiple parse trees mean multiple interpretations — and computers need a single, deterministic answer.

A **compiler** for a programming language cannot tolerate an ambiguous grammar: the same source code would have multiple possible meanings.

---

## Example 1: Arithmetic Expressions

### The Ambiguous Grammar

```
E → E + E | E * E | id
```

This grammar is ambiguous. Consider deriving `id + id * id`:

**Parse Tree 1** (interprets as `(id + id) * id` — wrong operator precedence):

```
       E
      / \  \
     E   *   E
    / \  \     \
   E  +  E     id
   |     |
  id    id
```

Wait — let's write it more precisely. Derivation 1 (`+` first):
```
E ⟹ E * E ⟹ (E + E) * E ⟹ (id + E) * E ⟹ (id + id) * E ⟹ (id + id) * id
```

Parse Tree 1: `*` is the root, left child is `E + E`.

```
        E
       /|\
      E  *  E
     /|\     \
    E + E    id
    |   |
   id  id
```

Derivation 2 (`*` first):
```
E ⟹ E + E ⟹ id + E ⟹ id + E * E ⟹ id + id * E ⟹ id + id * id
```

Parse Tree 2: `+` is the root, right child is `E * E`.

```
        E
       /|\
      E  +  E
      |    /|\
     id   E  *  E
          |     |
         id    id
```

These are **two distinct parse trees** for the same string `id + id * id`. The grammar is ambiguous.

### Removing the Ambiguity

The ambiguity arises because the grammar does not encode **operator precedence** (`*` binds tighter than `+`) or **associativity**.

**Unambiguous grammar:**

```
E → E + T | T
T → T * F | F
F → (E) | id
```

This forces `*` to always bind tighter than `+` (because `*` lives at the `T` level, below `+` at the `E` level). And both operators are left-associative (the left-recursive form `E → E + T` ensures left-to-right evaluation).

**Derivation of `id + id * id` in the unambiguous grammar:**

```
E ⟹ E + T ⟹ T + T ⟹ F + T ⟹ id + T ⟹ id + T * F ⟹ id + F * F ⟹ id + id * F ⟹ id + id * id
```

This is the **only** leftmost derivation. There is only one parse tree, with `+` at the top and `*` inside the right subtree.

---

## Example 2: The Dangling Else Problem

### The Ambiguous Grammar

Consider a grammar for `if-then-else` statements:

```
S → if C then S
  | if C then S else S
  | A
C → p | q | r
A → a | b | c
```

The string `if p then if q then a else b` is ambiguous. Two interpretations:

**Interpretation 1** — `else b` belongs to the **inner** `if`:
```
if p then (if q then a else b)
```

**Interpretation 2** — `else b` belongs to the **outer** `if`:
```
(if p then if q then a) else b
```

Both are valid derivations under the grammar.

### Removing the Ambiguity

The standard fix: **every `else` matches the nearest unmatched `if`** (Interpretation 1 above). We encode this by splitting `S` into:

- `Sm` (matched): an `if-then-else` where the `then` branch is also fully matched.
- `Su` (unmatched): an `if-then` with no `else`, or an `if-then-else` where the `then` branch is unmatched.

**Unambiguous grammar:**

```
S → Sm | Su
Sm → if C then Sm else Sm | A
Su → if C then S | if C then Sm else Su
C → p | q | r
A → a | b | c
```

Under this grammar, `if p then if q then a else b` has the unique derivation:

```
S ⟹ Su ⟹ if C then S ⟹ if p then S ⟹ if p then Sm
  ⟹ if p then if C then Sm else Sm
  ⟹ if p then if q then Sm else Sm
  ⟹ if p then if q then A else Sm
  ⟹ if p then if q then a else Sm
  ⟹ if p then if q then a else A
  ⟹ if p then if q then a else b
```

The `else b` is unambiguously attached to the inner `if q`.

---

## General Strategies for Removing Ambiguity

| Strategy | When to Apply |
|---|---|
| **Set precedence levels** | When operators of different "strength" are at the same grammar level |
| **Set associativity** (left or right recursion) | When the same operator can group left or right |
| **Split non-terminals** | When a non-terminal is overloaded (e.g., matched vs. unmatched statements) |
| **Factor out common prefixes** | When two rules share a common prefix, they can be merged |
| **Simplify/restructure rules** | Remove redundant choices that create ambiguity |

!!! danger "Inherently Ambiguous Languages"
    Some context-free languages are **inherently ambiguous** — every CFG for them is ambiguous. No grammar transformation can remove the ambiguity. An example is:

    ```
    L = {aⁱbʲcᵏ | i = j  or  j = k}
    ```

    For strings where `i = j = k`, any parse tree must "decide" which equality is the reason for acceptance — and there is no way to make that decision uniquely.
