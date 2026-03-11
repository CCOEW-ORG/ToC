# Deterministic Finite Automata (DFA)

## Formal Definition

A **Deterministic Finite Automaton (DFA)** is a 5-tuple:

```
M = (Q, Σ, δ, q₀, F)
```

where:

| Component | Description |
|---|---|
| `Q` | Finite, non-empty set of **states** |
| `Σ` | **Input alphabet** (finite, non-empty set of symbols) |
| `δ` | **Transition function**: `δ : Q × Σ → Q` |
| `q₀` | **Start state** (also called initial state): `q₀ ∈ Q` |
| `F` | Set of **accept states** (also called final states): `F ⊆ Q` |

### Why "Deterministic"?

The transition function `δ : Q × Σ → Q` maps each `(state, symbol)` pair to **exactly one** next state. Given the current state and the current input symbol, the machine's next step is completely determined — there is no choice. Consequently, every input string has exactly one computational path through the DFA.

---

## How a DFA Accepts a String

A DFA `M` processes a string `w = w₁w₂…wₙ` as follows:

1. Start in state `q₀`.
2. For each symbol `wᵢ` (from left to right), apply the transition function: move from the current state `q` to state `δ(q, wᵢ)`.
3. After reading the last symbol `wₙ`, the machine is in some state `q'`.
   - If `q' ∈ F` → **M accepts w**
   - If `q' ∉ F` → **M rejects w**

The **language of M**, written `L(M)`, is the set of all strings M accepts:

```
L(M) = { w ∈ Σ* | M accepts w }
```

### Extended Transition Function δ̂

It is useful to define `δ̂(q, w)` — the state the DFA reaches from state `q` after reading the entire string `w`:

```
δ̂(q, ε) = q
δ̂(q, wa) = δ(δ̂(q, w), a)      for string w and symbol a
```

Then: `M accepts w  ⟺  δ̂(q₀, w) ∈ F`.

---

## State Transition Diagram (STD)

A DFA is most naturally visualized as a directed graph:

- **Nodes** represent states.
- **Edges** represent transitions: an edge from `q` to `p` labelled `a` means `δ(q, a) = p`.
- The **start state** is indicated by an arrow with no source (or the label `→`).
- **Accept states** are drawn with a double circle.

---

## Worked Example 1: Even Number of 1s

**Language:** `L = { w ∈ {0,1}* | w contains an even number of 1s }`

**DFA Design:**

The machine needs to remember one bit of information: *have I seen an even or odd number of 1s so far?* Two states suffice.

```
M = (Q, Σ, δ, q₀, F)

Q = {q_even, q_odd}
Σ = {0, 1}
q₀ = q_even      (start: 0 ones seen so far, which is even)
F = {q_even}
```

Transition table:

| State | 0 | 1 |
|---|---|---|
| →\* `q_even` | `q_even` | `q_odd` |
| `q_odd` | `q_odd` | `q_even` |

*(→ marks start state, \* marks accept state)*

Reading a `0` never changes the parity of the count; reading a `1` flips it. The machine accepts if and only if it ends in `q_even`.

**Acceptance trace for `w = 1101`:**

```
q_even --1→ q_odd --1→ q_even --0→ q_even --1→ q_odd
```
Final state `q_odd ∉ F` → **rejected** (three 1s, an odd count).

**Acceptance trace for `w = 110`:**

```
q_even --1→ q_odd --1→ q_even --0→ q_even
```
Final state `q_even ∈ F` → **accepted** (two 1s, an even count).

---

## Worked Example 2: Strings Starting with 'a' and Ending with 'b'

**Language:** `L = { w ∈ {a,b}* | w starts with 'a' and ends with 'b' }`

**States needed:**
- `q₀` — start (nothing read yet)
- `q₁` — read at least one symbol, and the string so far starts with `a` and currently ends with `a` (or we are mid-string)
- `q₂` — the string started with `a` and currently ends with `b` → this is our accept state
- `q_dead` — a dead/trap state for strings that start with `b` (they can never be accepted)

Transition table:

| State | `a` | `b` |
|---|---|---|
| → `q₀` | `q₁` | `q_dead` |
| `q₁` | `q₁` | `q₂` |
| \* `q₂` | `q₁` | `q₂` |
| `q_dead` | `q_dead` | `q_dead` |

!!! note "Dead (Trap) States"
    A **dead state** (also called a **trap state**) is a non-accepting state with transitions only to itself. Once the machine enters a dead state, it can never reach an accept state. Every complete DFA must be *total* — `δ` must be defined for every `(state, symbol)` pair — so dead states are often needed to handle "impossible" inputs.

---

## How to Write a Complete DFA Answer

When answering a DFA question in examinations or assignments, follow this format:

1. **Identify `Σ`** if not given.
2. **Restate the language `L`** in set-builder notation.
3. **Give 3–5 sample strings** that belong to `L`, and 2–3 that do not.
4. **Draw the State Transition Diagram (STD).**
5. **Write the State Transition Table** for `δ`. Mark the start state (→) and accept states (\*) clearly.
6. **Define the machine as a quintuple**: explicitly list `Q`, `Σ`, `δ` (as the table), `q₀`, and `F`.
7. **Show a full acceptance trace** for at least one string in `L`.
8. *(Optional)* Show a rejection trace for a string not in `L`.

---

## Practice Problems

Construct a complete DFA (STD + transition table + quintuple) for each of the following:

1. `Σ = {1}`, `L = { w | |w| is a multiple of 3 }` (i.e., the number of 1s is divisible by 3)
2. `Σ = {0, 1}`, `L = { w | w contains at least one 1 }`
3. `Σ = {0, 1}`, `L = { w | w contains at least two 1s }`
4. `Σ = {0, 1}`, `L = { w | w starts with 1 and ends with 1 }`
5. `Σ = {0, 1}`, `L = { w | w has `101` as a substring }`

!!! tip "Strategy for Problem 1"
    If you need to detect "number of symbols is a multiple of `k`", you need exactly `k` states arranged in a cycle, where each incoming symbol advances you one step around the cycle.
