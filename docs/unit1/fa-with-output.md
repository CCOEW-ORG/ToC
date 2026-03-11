# Finite Automata with Output: Moore and Mealy Machines

So far, our finite automata have produced only a binary output — accept or reject. We now extend them to produce a richer output at each step. Two classical models exist: **Mealy machines** and **Moore machines**.

---

## Overview

| Feature | Mealy Machine | Moore Machine |
|---|---|---|
| Output depends on | Current state **and** current input | Current state **only** |
| When output is produced | On each **transition** | In each **state** |
| Output function | `λ : Q × Σ → Δ` | `λ : Q → Δ` |

Both models produce an output string as they process an input string, rather than a simple accept/reject.

---

## Mealy Machine

### Formal Definition

A **Mealy machine** is a 6-tuple:

```
M = (Q, Σ, Δ, δ, λ, q₀)
```

| Component | Description |
|---|---|
| `Q` | Finite set of states |
| `Σ` | Input alphabet |
| `Δ` | Output alphabet |
| `δ` | State transition function: `δ : Q × Σ → Q` |
| `λ` | Output function: `λ : Q × Σ → Δ` |
| `q₀` | Start state |

The output is associated with each **transition edge**. In a state transition diagram, edges are labelled `input/output`.

### Example

```
Q = {q₀, q₁, q₂},  Σ = {0, 1},  Δ = {0, 1}

δ table:             λ table:
State | 0  | 1      State | 0 | 1
  q₀  | q₁ | q₀      q₀  | 0 | 0
  q₁  | q₁ | q₂      q₁  | 0 | 1
  q₂  | q₂ | q₂      q₂  | 1 | 1
```

---

## Moore Machine

### Formal Definition

A **Moore machine** is a 6-tuple:

```
M = (Q, Σ, Δ, δ, λ, q₀)
```

The difference from Mealy: the output function is `λ : Q → Δ` (output depends only on state, not on input). The output is associated with each **state node**.

### Example

```
Q = {q₀, q₁, q₂},  Σ = {0, 1},  Δ = {0, 1}

δ table:             λ table:
State | 0  | 1      State | output
  q₀  | q₁ | q₀      q₀  |   0
  q₁  | q₁ | q₂      q₁  |   0
  q₂  | q₂ | q₂      q₂  |   1
```

Note: In a Moore machine, the output is produced as the machine *enters* a state (including the start state), so a string of length `n` produces `n+1` output symbols. (Some conventions produce only `n` outputs by ignoring the start-state output; be consistent with whichever convention your course uses.)

---

## Mealy to Moore Conversion

A Mealy machine can be converted to an equivalent Moore machine. The key challenge: in a Mealy machine, the same state can produce different outputs depending on *which* input caused the transition into it. In a Moore machine, a state has a single fixed output. The solution is to **split** any state that would need to produce different outputs into multiple copies — one per distinct output value.

### Algorithm

**Step 1:** Define a combined transition-output table `T`:

```
T(q, a) = (δ(q, a), λ(q, a))
```

This gives pairs `(next_state, output)` for each `(state, input)`.

**Step 2:** Identify target states that appear with more than one output value across all transitions. Split each such state `qᵢ` into states `qᵢ₀`, `qᵢ₁`, … — one per distinct output value.

Update all transitions: wherever `(qᵢ, v)` appeared as a target, replace it with the appropriate `qᵢᵥ`.

**Step 3:** From `T'` (the updated table), extract:
- `δ'` (state transitions) — just the first component of each pair.
- `λ'` (state output) — the output value associated with each new state (the subscript on the state name).

### Worked Example: Mealy → Moore

**Original Mealy machine:**

| State | 0 (δ, λ) | 1 (δ, λ) |
|---|---|---|
| `q₀` | `q₁, 0` | `q₀, 0` |
| `q₁` | `q₂, 1` | `q₃, 0` |
| `q₂` | `q₂, 0` | `q₃, 1` |
| `q₃` | `q₁, 0` | `q₂, 0` |

**Step 1: Build T table:**

| State | 0 | 1 |
|---|---|---|
| `q₀` | `(q₁, 0)` | `(q₀, 0)` |
| `q₁` | `(q₂, 1)` | `(q₃, 0)` |
| `q₂` | `(q₂, 0)` | `(q₃, 1)` |
| `q₃` | `(q₁, 0)` | `(q₂, 0)` |

**Step 2: Identify states needing splitting.**

- `q₂` appears as target with output `1` (from `q₁` on `0`) and output `0` (from `q₂` on `0`, `q₃` on `1`) → split into `q₂₀` and `q₂₁`.
- `q₃` appears as target with output `0` (from `q₁` on `1`) and output `1` (from `q₂` on `1`) → split into `q₃₀` and `q₃₁`.
- `q₀` and `q₁` always appear with the same output → no split needed.

**Step 3: Build Moore machine tables:**

| State | 0 | 1 | Moore output `λ'` |
|---|---|---|---|
| `q₀` | `q₁` | `q₀` | 0 |
| `q₁` | `q₂₁` | `q₃₀` | 0 |
| `q₂₀` | `q₂₀` | `q₃₁` | 0 |
| `q₂₁` | `q₂₀` | `q₃₁` | 1 |
| `q₃₀` | `q₁` | `q₂₀` | 0 |
| `q₃₁` | `q₁` | `q₂₀` | 1 |

---

## Moore to Mealy Conversion

Converting in the other direction is generally simpler — Moore machines often have more states than their Mealy equivalents, so this conversion tends to *reduce* the number of states.

### Algorithm

**Step 1:** Define `T`:

```
T(q, a) = (δ(q, a),  λ(δ(q, a)))
```

The output is determined by the *target* state's Moore output.

**Step 2:** Find transitions where different source states transition to the same target state with the same output. These source states can potentially be **merged** — combine them into a single state if all their transitions match identically.

**Step 3:** From `T'`, extract `δ'` (transitions) and `λ'` (output function — now on transitions, not states).

### Worked Example: Moore → Mealy

**Original Moore machine:**

| State | 0 | 1 | `λ` |
|---|---|---|---|
| `q₀` | `q₁` | `q₄` | 0 |
| `q₁` | `q₄` | `q₃` | 1 |
| `q₂` | `q₀` | `q₁` | 1 |
| `q₃` | `q₁` | `q₄` | 1 |
| `q₄` | `q₃` | `q₂` | 0 |

**Step 1: Build T table** (output = `λ` of target state):

| State | 0 | 1 |
|---|---|---|
| `q₀` | `(q₁, 1)` | `(q₄, 0)` |
| `q₁` | `(q₄, 0)` | `(q₃, 1)` |
| `q₂` | `(q₀, 0)` | `(q₁, 1)` |
| `q₃` | `(q₁, 1)` | `(q₄, 0)` |
| `q₄` | `(q₃, 1)` | `(q₂, 1)` |

**Step 2: Merge states** — `q₀` and `q₃` have identical transition pairs in T. Merge into `q₀₃`.

| State | 0 | 1 |
|---|---|---|
| `q₀₃` | `(q₁, 1)` | `(q₄, 0)` |
| `q₁` | `(q₄, 0)` | `(q₀₃, 1)` |
| `q₂` | `(q₀₃, 0)` | `(q₁, 1)` |
| `q₄` | `(q₀₃, 1)` | `(q₂, 1)` |

**Step 3: Extract Mealy machine:**

`δ'` and `λ'`:

| State | δ'(q,0) | λ'(q,0) | δ'(q,1) | λ'(q,1) |
|---|---|---|---|---|
| `q₀₃` | `q₁` | 1 | `q₄` | 0 |
| `q₁` | `q₄` | 0 | `q₀₃` | 1 |
| `q₂` | `q₀₃` | 0 | `q₁` | 1 |
| `q₄` | `q₀₃` | 1 | `q₂` | 1 |

The resulting Mealy machine has 4 states, down from the Moore machine's 5.

---

## Key Differences: Mealy vs. Moore

!!! tip "Equivalence"
    Every Mealy machine has an equivalent Moore machine and vice versa. They are not different in what functions they can compute — only in how that computation is expressed. In practice, Mealy machines tend to use fewer states (since one state can produce different outputs for different inputs), while Moore machines are sometimes easier to reason about because the output depends only on where you are, not on how you got there.
