# Regular Languages & Operations

## What Is a Regular Language?

A language `L` is called a **regular language** if there exists some finite automaton (DFA, NFA, or ε-NFA — all equivalent) that accepts it:

```
L is regular  ⟺  ∃ FA M such that L(M) = L
```

### Examples of Regular Languages

- `L = { w ∈ {0,1}* | w ends with 101 }`
- `L = { w ∈ {0,1}* | w contains '11' or '00' as a substring }`
- `L = { w ∈ {a,b}* | w starts with 'ab' and ends with 'ba' }`
- All finite languages (any finite set of strings is regular — you can build a DFA for it)

### Examples of Non-Regular Languages

These languages require counting beyond the capability of any finite automaton:

- `L = { w ∈ {0,1}* | w contains equal numbers of 0s and 1s }`
- `L = { aⁿbⁿ | n ≥ 0 }` — equal counts of a's and b's
- `L = { w ∈ {a,b}* | w is a palindrome }`

We can prove non-regularity rigorously using the **Pumping Lemma** (see Unit 2, Pumping Lemma page).

---

## Regular Operations

Three operations on languages are called **regular operations** because the class of regular languages is **closed** under all three.

Let `A` and `B` be languages over the same alphabet Σ.

### 1. Union

```
A ∪ B = { x | x ∈ A  or  x ∈ B }
```

**Example:** If `A = {a, aa}` and `B = {b, bb}`, then `A ∪ B = {a, aa, b, bb}`.

### 2. Concatenation

```
A ∘ B = { xy | x ∈ A  and  y ∈ B }
```

Every string in `A ∘ B` is formed by picking a string from `A` followed by a string from `B`.

**Example:** If `A = {ab, c}` and `B = {d, ef}`, then `A ∘ B = {abd, abef, cd, cef}`.

!!! warning "Concatenation is not commutative"
    In general, `A ∘ B ≠ B ∘ A`. The concatenation operation preserves order.

### 3. Kleene Star

```
A* = { x₁x₂…xₖ | k ≥ 0  and each xᵢ ∈ A }
```

`A*` contains all strings formed by concatenating **zero or more** strings from `A`. The `k = 0` case gives the empty string `ε`, so `ε ∈ A*` always.

**Example:** If `A = {ab}`, then `A* = {ε, ab, abab, ababab, …}`.

!!! tip "Why are these called 'regular' operations?"
    They are called regular operations because — as we prove below — applying any of them to regular languages always produces a regular language. The class of regular languages is *closed* under union, concatenation, and star.

---

## Closure Under Regular Operations

We prove that regular languages are closed under each of the three operations. The proofs construct NFAs for the combined language from NFAs for the component languages.

### Closure Under Union

**Theorem:** If `A` and `B` are regular languages, then `A ∪ B` is regular.

**Proof:** Since `A` and `B` are regular, there exist NFAs `Mₐ` and `M_b` accepting them. Construct a new NFA `M` as follows:

1. Create a new start state `q_new`.
2. Add ε-transitions from `q_new` to the start states of both `Mₐ` and `M_b`.
3. Keep all states, transitions, and accept states of both machines.

```
          ε ──→  Mₐ (accepts A)
q_new
          ε ──→  M_b (accepts B)
```

`M` accepts a string `x` iff `x ∈ A` or `x ∈ B`. Therefore `L(M) = A ∪ B`, which is regular. ∎

### Closure Under Concatenation

**Theorem:** If `A` and `B` are regular languages, then `A ∘ B` is regular.

**Proof:** Let `Mₐ` accept `A` and `M_b` accept `B`. Construct `M`:

1. Keep all states of `Mₐ` and `M_b`.
2. The start state of `M` is the start state of `Mₐ`.
3. Add ε-transitions from each accept state of `Mₐ` to the start state of `M_b`.
4. The accept states of `M` are the accept states of `M_b` only (the accept states of `Mₐ` become non-accepting in `M`).

```
x ──→  Mₐ ──ε──→  M_b ──→ (accept)
```

`Mₐ` processes the `x` portion; when it "accepts" that prefix, ε-transitions hand control to `M_b` to process `y`. `M` accepts `xy` iff `x ∈ A` and `y ∈ B`. ∎

### Closure Under Star

**Theorem:** If `A` is a regular language, then `A*` is regular.

**Proof:** Let `Mₐ` accept `A`. Construct `M`:

1. Create a new start state `q_new` that is also an accept state (to handle the `k=0` case, accepting `ε`).
2. Add an ε-transition from `q_new` to the start state of `Mₐ`.
3. Add ε-transitions from each accept state of `Mₐ` back to the start state of `Mₐ` (to allow repeated concatenation).

```
              ┌────────────ε──────────────┐
              ↓                           │
q_new ──ε──→  (start of Mₐ) ──…──→ (accept of Mₐ)
  ↑
(also accept)
```

This allows the machine to "restart" `Mₐ` zero or more times. ∎

---

## Additional Closure Properties

Regular languages are closed under several other operations:

| Operation | Closed? |
|---|---|
| Union | ✅ Yes |
| Concatenation | ✅ Yes |
| Kleene Star | ✅ Yes |
| Complement | ✅ Yes (swap accept and non-accept states in the DFA) |
| Intersection | ✅ Yes (via De Morgan: `A ∩ B = ¬(¬A ∪ ¬B)`, or by product construction) |
| Difference (`A \ B`) | ✅ Yes (`A \ B = A ∩ ¬B`) |
| Reversal | ✅ Yes |

!!! danger "Contrast with Context-Free Languages"
    Context-free languages are closed under union, concatenation, and star — but **NOT** under intersection or complement. This is one of the key differences between regular and context-free languages.
