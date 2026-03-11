# ε-NFA and Automata Conversions

## ε-NFA (Epsilon-NFA)

An **ε-NFA** (epsilon-NFA) is an NFA that also allows **spontaneous transitions** — transitions that consume *no input symbol*. These are written as `ε`-transitions.

### Formal Definition

An ε-NFA is also a 5-tuple `M = (Q, Σ, δ, q₀, F)`, but the transition function is extended:

```
δ : Q × (Σ ∪ {ε}) → P(Q)
```

That is, from any state the machine may spontaneously fork into copies in other states without reading any symbol.

### Why ε-Transitions Are Useful

ε-transitions make it easy to *compose* smaller machines:

- To accept `L₁ ∪ L₂`, add a new start state with ε-transitions into the start states of the machines for `L₁` and `L₂`.
- To accept `L₁ · L₂` (concatenation), connect the accept states of the `L₁` machine to the start state of the `L₂` machine with ε-transitions.
- These constructions are the foundation of the regex-to-NFA conversion in Unit 2.

### Example ε-NFA

```
δ     | 0        | 1           | ε
------|---------|------------|-------
→ q₀  | {q₀}    | {q₀, q₁}   | ∅
  q₁  | {q₂}    | ∅           | {q₂}
  q₂  | ∅       | {q₃}        | ∅
* q₃  | {q₃}    | {q₃}        | ∅
```

Here, from `q₁` the machine can spontaneously move to `q₂` without consuming a symbol.

---

## ε-Closure

The **ε-closure** of a state `q`, written `E(q)` or `ε-closure(q)`, is the set of all states reachable from `q` by following **zero or more** ε-transitions (including `q` itself).

Formally: `E(q) = { p ∈ Q | p is reachable from q via ε-transitions alone }`.

The ε-closure is extended to sets: `E(S) = ⋃_{q ∈ S} E(q)`.

### Computing ε-Closure

Use a BFS/DFS starting from the state, following only ε-edges:

**For the example above:**

```
E(q₀) = {q₀}          — no ε-transitions out of q₀
E(q₁) = {q₁, q₂}      — q₁ →ε q₂; from q₂ no further ε-transitions
E(q₂) = {q₂}          — no ε-transitions out of q₂
E(q₃) = {q₃}          — no ε-transitions out of q₃
```

!!! warning "Always include the state itself"
    A state is always in its own ε-closure, even if it has no outgoing ε-transitions.

---

## Conversion 1: ε-NFA → NFA

Given ε-NFA `M₁ = (Q, Σ_ε, δ, q₀, F)`, construct an equivalent NFA `M₂ = (Q, Σ, δ', q₀', F')`:

**Algorithm:**

1. States remain the same: `Q' = Q`.
2. New transition function: `δ'(q, a) = E( ⋃_{r ∈ E(q)} δ(r, a) )`  for `a ∈ Σ` (not ε).
   In words: from state `q`, first follow all ε-transitions to compute `E(q)`, then for each state in `E(q)` take the `a`-transition, then follow all ε-transitions from every state you reach.
3. New start state: `q₀' = E(q₀)` — actually `q₀` stays the same, but note that if `E(q₀)` contains an accept state, then `q₀'` must also be an accept state.
4. Accept states: `F' = { q ∈ Q | E(q) ∩ F ≠ ∅ }` — any state from which an accept state is reachable via ε alone becomes an accept state.

### Worked Example: ε-NFA → NFA

Using the ε-NFA from the example above:

**ε-closures:**

```
E(q₀) = {q₀},  E(q₁) = {q₁, q₂},  E(q₂) = {q₂},  E(q₃) = {q₃}
```

**Computing δ':**

For each state `q` and each symbol `a ∈ {0, 1}`:

`δ'(q, a) = E( ⋃_{r ∈ E(q)} δ(r, a) )`

| State | 0 | 1 |
|---|---|---|
| → `q₀` | `E(δ(q₀,0)) = E({q₀}) = {q₀}` | `E(δ(q₀,1)) = E({q₀,q₁}) = {q₀}∪{q₁,q₂} = {q₀,q₁,q₂}` |
| `q₁` | `E(δ(q₁,0)∪δ(q₂,0)) = E({q₂}∪∅) = {q₂}` | `E(δ(q₁,1)∪δ(q₂,1)) = E(∅∪{q₃}) = {q₃}` |
| `q₂` | `E(∅) = ∅` | `E({q₃}) = {q₃}` |
| \* `q₃` | `E({q₃}) = {q₃}` | `E({q₃}) = {q₃}` |

**Resulting NFA δ' table (compact):**

| State | 0 | 1 |
|---|---|---|
| → `q₀` | `{q₀}` | `{q₀, q₁, q₂}` |
| `q₁` | `{q₂}` | `{q₃}` |
| `q₂` | `∅` | `{q₃}` |
| \* `q₃` | `{q₃}` | `{q₃}` |

*(Note: `q₁` was collapsed with `q₂`'s transitions because `E(q₁) = {q₁, q₂}`.)*

---

## Conversion 2: NFA → DFA (Subset Construction)

This is the fundamental algorithm proving NFA ⊆ DFA in expressive power.

Given NFA `Mₙ = (Q, Σ, δ, q₀, F)`, construct DFA `M_D = (Q', Σ, δ', q₀', F')`:

**Key idea:** Each *state* of the DFA corresponds to a *set of states* of the NFA — specifically, all states the NFA might currently be in across all non-deterministic branches.

**Algorithm:**

1. `Q' ⊆ P(Q)` — DFA states are subsets of NFA states; `|Q'| ≤ 2|Q|`.
2. `δ'(R, a) = ⋃_{r ∈ R} δ(r, a)` for `R ∈ Q'`, `a ∈ Σ`.
3. `q₀' = {q₀}` (or `E(q₀)` for ε-NFAs).
4. `F' = { R ∈ Q' | R ∩ F ≠ ∅ }` — a DFA state is accepting if it contains at least one NFA accept state.

!!! tip "Build states lazily to minimize the DFA"
    In practice, most of the `2|Q|` possible subsets are never reached. **Start from `{q₀}` and only add a new DFA state when it is referenced by a transition.** This gives the minimal reachable DFA directly and avoids filling out a huge table.

### Worked Example: NFA → DFA

**NFA:**

| State | 0 | 1 |
|---|---|---|
| → `q₀` | `{q₀}` | `{q₀, q₁, q₂}` |
| `q₁` | `{q₂}` | `∅` |
| `q₂` | `∅` | `{q₃}` |
| \* `q₃` | `{q₃}` | `{q₃}` |

**Step-by-step subset construction (lazy):**

Start: `q₀' = {q₀}`.

| DFA state `R` | `δ'(R, 0)` | `δ'(R, 1)` |
|---|---|---|
| → `{q₀}` | `δ(q₀,0) = {q₀}` | `δ(q₀,1) = {q₀,q₁,q₂}` |
| `{q₀,q₁,q₂}` | `δ(q₀,0)∪δ(q₁,0)∪δ(q₂,0) = {q₀}∪{q₂}∪∅ = {q₀,q₂}` | `δ(q₀,1)∪δ(q₁,1)∪δ(q₂,1) = {q₀,q₁,q₂}∪∅∪{q₃} = {q₀,q₁,q₂,q₃}` |
| `{q₀,q₂}` | `{q₀}∪∅ = {q₀}` | `{q₀,q₁,q₂}∪{q₃} = {q₀,q₁,q₂,q₃}` |
| \* `{q₀,q₁,q₂,q₃}` | `{q₀}∪{q₂}∪∅∪{q₃} = {q₀,q₂,q₃}` | `{q₀,q₁,q₂}∪∅∪{q₃}∪{q₃} = {q₀,q₁,q₂,q₃}` |
| \* `{q₀,q₂,q₃}` | `{q₀}∪∅∪{q₃} = {q₀,q₃}` | `{q₀,q₁,q₂}∪{q₃}∪{q₃} = {q₀,q₁,q₂,q₃}` |
| \* `{q₀,q₃}` | `{q₀}∪{q₃} = {q₀,q₃}` | `{q₀,q₁,q₂}∪{q₃} = {q₀,q₁,q₂,q₃}` |

Accept states: any DFA state containing `q₃` (marked with \*).

This is the minimal reachable DFA. Note that only 6 of the `2⁴ = 16` possible subsets were ever reached.

---

## Conversion 3: ε-NFA → DFA (Direct)

Combines both conversions. Given ε-NFA `M₁ = (Q, Σ, δ, q₀, F)`, construct DFA `M_D`:

**Algorithm:**

1. `Q' ⊆ P(Q)`, `|Q'| ≤ 2|Q|`.
2. `δ'(R, a) = E( ⋃_{r ∈ R} δ(r, a) )` — apply ε-closure after every transition.
3. `q₀' = E(q₀)` — start state is the ε-closure of the NFA start state.
4. `F' = { R ∈ Q' | R ∩ F ≠ ∅ }`.

### Worked Example: ε-NFA → DFA

**ε-NFA:**

| State | `a` | `b` | `ε` |
|---|---|---|---|
| → `q₀` | `{q₀, q₁}` | `{q₀}` | `∅` |
| `q₁` | `∅` | `{q₂}` | `{q₃}` |
| `q₂` | `∅` | `{q₃}` | `∅` |
| `q₃` | `{q₄}` | `∅` | `{q₁}` |
| \* `q₄` | `{q₄}` | `{q₄}` | `∅` |

**ε-closures:**

```
E(q₀) = {q₀}
E(q₁) = {q₁, q₃}      (q₁ →ε q₃; from q₃ →ε q₁ — already in set)
E(q₂) = {q₂}
E(q₃) = {q₁, q₃}      (q₃ →ε q₁; from q₁ →ε q₃ — already in set)
E(q₄) = {q₄}
```

**Start state:** `q₀' = E(q₀) = {q₀}`.

**Lazy construction:**

| DFA state `R` | `δ'(R, a)` | `δ'(R, b)` |
|---|---|---|
| → `{q₀}` | `E({q₀,q₁}) = {q₀}∪{q₁,q₃} = {q₀,q₁,q₃}` | `E({q₀}) = {q₀}` |
| `{q₀,q₁,q₃}` | `E({q₀,q₁}∪∅∪{q₄}) = {q₀,q₁,q₃}∪{q₄} = {q₀,q₁,q₃,q₄}` | `E({q₀}∪{q₂}∪∅) = {q₀}∪{q₂} = {q₀,q₂}` |
| \* `{q₀,q₁,q₃,q₄}` | `E({q₀,q₁}∪∅∪{q₄}∪{q₄}) = {q₀,q₁,q₃,q₄}` | `E({q₀}∪{q₂}∪∅∪{q₄}) = {q₀,q₂,q₄}` |
| `{q₀,q₂}` | `E({q₀,q₁}∪∅) = {q₀,q₁,q₃}` | `E({q₀}∪{q₃}) = {q₀}∪{q₁,q₃} = {q₀,q₁,q₃}` |
| \* `{q₀,q₂,q₄}` | `E({q₀,q₁}∪∅∪{q₄}) = {q₀,q₁,q₃,q₄}` | `E({q₀}∪{q₃}∪{q₄}) = {q₀,q₁,q₃,q₄}` |

Accept states: any DFA state containing `q₄` (marked \*).

The resulting DFA has 5 reachable states.

---

## Interconvertibility Summary

All three models — ε-NFA, NFA, and DFA — are **equivalent** in expressive power. Any language accepted by one is accepted by all three.

```
ε-NFA  ←→  NFA  ←→  DFA
```

The conversions show:

| Conversion | States in result |
|---|---|
| ε-NFA → NFA | Same number of states (at most) |
| NFA → DFA | At most `2|Q|` states (exponential blowup possible) |
| ε-NFA → DFA | At most `2|Q|` states |

!!! tip "DFA Minimization"
    After converting an NFA to a DFA using subset construction, you may obtain many unreachable or equivalent states. You can minimize the DFA using the **Myhill-Nerode Theorem** and the **Table-Filling (Distinguishability) Method**. This is recommended further reading.
