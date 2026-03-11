# Non-Deterministic Finite Automata (NFA)

## Motivation: The Cost of Memory Management in DFAs

Consider building a DFA to accept all strings over `{a, b}` that *end with* `ab`.

To recognize a pattern of length `k` at the *end* of an input string, a DFA must implicitly "remember" the last `k` symbols seen. This is done by encoding all relevant suffixes into the state structure, which requires states to shift their "memory window" as each new symbol arrives.

For the "ends with `ab`" example, three states are needed:
- One for the general case (haven't just seen `a`)
- One for "the last symbol was `a`"
- One for "the last two symbols were `ab`" (accept state)

This is manageable. But for more complex patterns — especially patterns involving multiple alternatives — the state count and the transition bookkeeping grow rapidly. The designer is doing *implicit memory management inside the state space*.

### The Non-Deterministic Alternative

What if, instead of building one machine that carefully tracks all possibilities in its state structure, we allow the machine to **explore multiple computational paths simultaneously**?

The idea: when the machine faces an ambiguous choice, it **forks** into multiple copies of itself. Each copy independently continues processing the remaining input. The overall machine **accepts** if *at least one copy* ends in an accept state.

This is **non-determinism**: the machine does not commit to a single path. It explores all possibilities in parallel.

---

## Formal Definition

A **Non-Deterministic Finite Automaton (NFA)** is a 5-tuple:

```
M = (Q, Σ, δ, q₀, F)
```

| Component | Description |
|---|---|
| `Q` | Finite, non-empty set of states |
| `Σ` | Input alphabet |
| `δ` | Transition function: `δ : Q × Σ → P(Q)` |
| `q₀` | Start state |
| `F` | Set of accept states, `F ⊆ Q` |

The crucial difference from a DFA is the transition function:

- **DFA:** `δ : Q × Σ → Q` — returns exactly **one** next state
- **NFA:** `δ : Q × Σ → P(Q)` — returns a **set** of next states (possibly empty, possibly containing multiple states)

`P(Q)` is the **power set** of `Q` — the set of all subsets of `Q`.

### What the Different Cases Mean

| `δ(q, a)` value | Meaning |
|---|---|
| `{p}` | Deterministic: go to state `p` |
| `{p, r}` | Non-deterministic: fork into copies going to `p` and `r` |
| `∅` | No transition: this copy of the machine **dies** |

### Acceptance in an NFA

An NFA accepts a string `w` if there **exists** at least one computational path through the NFA that, after reading all of `w`, ends in an accept state.

Equivalently, let `δ̂(q, w)` be the set of all states reachable from `q` by reading `w` (following all possible non-deterministic choices):

```
M accepts w  ⟺  δ̂(q₀, w) ∩ F ≠ ∅
```

---

## NFA Example: Strings Ending with `ab`

Compare the DFA and NFA for `L = { w ∈ {a,b}* | w ends with ab }`:

**DFA** (requires careful memory management):

| State | `a` | `b` |
|---|---|---|
| → `q₀` | `q₁` | `q₀` |
| `q₁` | `q₁` | `q₂` |
| \* `q₂` | `q₁` | `q₀` |

3 states, 6 transitions.

**NFA** (direct, intuitive design):

| State | `a` | `b` |
|---|---|---|
| → `q₀` | `{q₀, q₁}` | `{q₀}` |
| `q₁` | `∅` | `{q₂}` |
| \* `q₂` | `∅` | `∅` |

3 states, 3 (non-empty) transitions.

The NFA is more natural: `q₀` is the "haven't started the suffix yet" state, `q₁` means "just saw `a` — maybe this is the start of the final `ab`", and `q₂` means "just completed `ab`". The non-determinism handles the fact that we don't know *which* `a` in the string will be the last one.

### Trace: Processing `w = aab` with the NFA above

| Step | Input symbol | Active states | Reasoning |
|---|---|---|---|
| Start | — | `{q₀}` | Start state |
| Read `a` | `a` | `{q₀, q₁}` | `δ(q₀, a) = {q₀, q₁}` |
| Read `a` | `a` | `{q₀, q₁}` | `δ(q₀,a)={q₀,q₁}`, `δ(q₁,a)=∅`; union = `{q₀,q₁}` |
| Read `b` | `b` | `{q₀, q₂}` | `δ(q₀,b)={q₀}`, `δ(q₁,b)={q₂}`; union = `{q₀,q₂}` |
| End | — | `{q₀, q₂}` | `q₂ ∈ F` → **ACCEPT** |

---

## Power of Non-Determinism

!!! warning "Non-determinism does NOT add computational power"
    NFAs and DFAs recognize exactly the **same class of languages** — the regular languages. Non-determinism makes machine *descriptions* more concise and intuitive, but it does not allow machines to solve problems that DFAs cannot.

    Formally: for every NFA, there exists a DFA that accepts the same language. (The DFA may have exponentially more states — up to `2|Q|` — but it exists.)

The benefit of NFAs is purely one of **design convenience**:

- NFA descriptions can be *exponentially* more concise than their equivalent DFAs.
- NFAs make it much easier to prove closure properties of regular languages (see Unit 2).
- NFAs are the natural intermediate form when converting regular expressions to automata.

---

## NFA Examples: `...11...` or `...101...`

**Language:** `L = { w ∈ {0,1}* | w contains `11` or `101` as a substring }`

**NFA** (4 states, 6 transitions):

A state `q₀` loops on both `0` and `1` (we haven't started a potential pattern). On seeing `1`, the machine forks: one copy guesses we are starting `11`, another guesses we are starting `101`.

| State | `0` | `1` |
|---|---|---|
| → `q₀` | `{q₀}` | `{q₀, q₁, q₃}` |
| `q₁` | `∅` | `{q₂}` |
| \* `q₂` | `{q₂}` | `{q₂}` |
| `q₃` | `{q₄}` | `∅` |
| `q₄` | `∅` | `{q₂}` |

*(Here `q₁→q₂` handles `11`, and `q₃→q₄→q₂` handles `101`.)*

---

## Practice Problems

Construct NFAs for the following languages over `Σ = {a, b}`:

1. `L = { w | w ends with `ba` }`
2. `L = { w | w contains `aba` as a substring }`
3. `L = { w | w has `aa` or `bb` as a substring }`
4. `L = { w | |w| mod 2 = 0 or w ends with `a` }` *(hint: use ε-transitions to combine two smaller NFAs)*
