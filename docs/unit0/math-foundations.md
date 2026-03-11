# Mathematical Foundations

This section reviews the mathematical vocabulary used throughout the course. These are not new ideas — you have seen most of them in Discrete Mathematics — but the ToC framing is worth making explicit.

---

## Sets

A **set** is an *unordered* collection of distinct objects. The two defining properties are:

- **Order does not matter:** `{1, 2, 3} = {3, 2, 1}`
- **Duplicates do not matter:** `{1, 2, 2, 3} = {1, 2, 3}`

Sets can be described by listing elements, or by a property:

```
A = {1, 2, 3}
B = {x | x is a prime number ending in 3}
S = {x | x is an even number less than 10}  →  S = {2, 4, 6, 8}
```

The **empty set** is written `∅` or `{}`.

### Power Set

The **power set** of `A`, written `P(A)` or `2^A`, is the set of all subsets of `A`.

**Example:** If `A = {1, 2, 3}`, then:

```
P(A) = { ∅, {1}, {2}, {3}, {1,2}, {1,3}, {2,3}, {1,2,3} }
```

!!! tip "Size of the Power Set"
    If `|A| = n`, then `|P(A)| = 2ⁿ`. This grows fast: a set with 4 elements has 16 subsets, one with 10 elements has 1024.

---

## Sequences and Tuples

A **sequence** is an *ordered* list of objects where both order and duplicates matter:

- `(1, 2, 3) ≠ (3, 2, 1)` — order matters
- `(1, 2, 3) ≠ (1, 2, 3, 3)` — duplicates matter

A **tuple** is a *finite* sequence. Tuples of specific sizes have conventional names:

| Size | Name |
|---|---|
| 1 | singleton (1-tuple) |
| 2 | pair (2-tuple) |
| 3 | triple (3-tuple) |
| 4 | quadruple |
| n | n-tuple |

---

## Cartesian Product

The **Cartesian product** (or *cross product*) of sets `A₁, A₂, …, Aₖ` is the set of all `k`-tuples `(a₁, a₂, …, aₖ)` where `aᵢ ∈ Aᵢ`:

```
A₁ × A₂ × … × Aₖ = { (a₁, a₂, …, aₖ) | aᵢ ∈ Aᵢ for each i }
```

**Example:**

```
A = {Tea, Coffee},  B = {Toast, Eggs},  C = {Apple, Banana}

A × B × C = {
  (Tea,    Toast, Apple),  (Tea,    Toast, Banana),
  (Tea,    Eggs,  Apple),  (Tea,    Eggs,  Banana),
  (Coffee, Toast, Apple),  (Coffee, Toast, Banana),
  (Coffee, Eggs,  Apple),  (Coffee, Eggs,  Banana)
}
```

The size of the Cartesian product: `|A₁ × A₂ × … × Aₖ| = |A₁| · |A₂| · … · |Aₖ|`.

---

## Functions

A **function** is a rule that assigns to each element of the *domain* exactly one element of the *range*:

```
f : D → R       means f maps domain D to range R
f(input) = output,   input ∈ D,  output ∈ R
```

The domain can be a Cartesian product, giving functions of multiple arguments:

```
f(a₁, a₂, …, aₙ) = output
```

The **arity** of such a function is `n` (the number of arguments).

!!! warning "Domain vs. Range vs. Codomain"
    In some textbooks, *range* refers only to the set of values *actually produced* by `f`, while *codomain* is the set `R` declared in `f : D → R`. In ToC, the distinction rarely matters, but be aware of it when reading other sources.

---

## Graphs

A **graph** is an ordered pair `G = (V, E)` where:

- `V` is a finite set of **vertices** (nodes).
- `E` is a set of **edges**.

For an **undirected graph** (no self-loops): `E ⊆ { {u, v} | u, v ∈ V,  u ≠ v }`.

For a **directed graph** (digraph): `E ⊆ { (u, v) | u, v ∈ V }` — here order matters, so `(u, v) ≠ (v, u)`.

We use directed graphs extensively when drawing **state transition diagrams** for automata.

---

## Closure

### Closed Under an Operation

A set `A` is **closed under** an operation `○` if applying `○` to any two members of `A` always produces another member of `A`:

```
∀ x, y ∈ A :  x ○ y ∈ A
```

**Example:** The natural numbers `ℕ` are closed under addition (`3 + 5 = 8 ∈ ℕ`) but *not* under subtraction (`3 − 5 = −2 ∉ ℕ`).

### Closure of a Set

The **closure** of a set `S` under a rule/operation is the *smallest* superset of `S` that is closed under that rule. It is obtained by repeatedly applying the rule until no new elements can be generated.

**Examples:**

- The closure of `{0, 1}` under addition is `ℕ` (the whole numbers) — you can reach any non-negative integer by adding 0s and 1s.
- The closure of `{-1, 1}` under addition is `ℤ` (all integers).

!!! tip "Why Closure Matters in ToC"
    The concept of closure appears constantly. We will ask: *Is the set of regular languages closed under union? Under intersection? Under complement?* A positive answer means any combination of regular languages using that operation stays regular — a very useful property when designing machines.
