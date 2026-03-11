# Equivalence of Regular Expressions and Finite Automata

## The Main Theorem

**Theorem:** A language is regular (accepted by some FA) if and only if it is described by some regular expression.

This means:

- Every RE can be converted to an equivalent ε-NFA → NFA → DFA.
- Every FA can be converted to an equivalent RE (using Arden's Theorem or state elimination).

---

## Part 1: RE → ε-NFA

We show that for each of the six base cases of a regular expression, we can build an ε-NFA. Larger REs are handled by composing these base machines using the union, concatenation, and star constructions from Unit 2 (Regular Languages).

### Base Cases

**RE: `a`** (single symbol)  
Language: `{a}`

```
q₀ ──a──→ q₁ (accept)
```

**RE: `ε`** (empty string)  
Language: `{ε}`

```
→ q₀ (accept)     (start state is also the accept state; accepts ε immediately)
```

**RE: `∅`** (empty language)  
Language: `{}`

```
→ q₀    q₁ (accept)     (no path from q₀ to q₁; nothing is accepted)
```

### Inductive Cases

**RE: `(R₁ | R₂)` — Union**  
Build machines `N₁` and `N₂` for `R₁` and `R₂`. Connect a new start state to both via ε-transitions:

```
           ε ──→  N₁
q_new
           ε ──→  N₂
```

**RE: `(R₁ · R₂)` — Concatenation**  
Connect the accept states of `N₁` to the start state of `N₂` via ε-transitions:

```
──→ N₁ ──ε──→ N₂ (accept)
```

**RE: `(R₁*)` — Star**  
Add a new start/accept state and loop:

```
              ┌────────ε────────┐
              ↓                 │
q_new ──ε──→ N₁ ──ε──→ q_new
(accept)
```

### Algorithm: Building an NFA from a RE

1. Identify the smallest sub-expressions of the regex.
2. Build a base-case NFA for each atomic sub-expression.
3. Combine using the inductive constructions, working from the inside of the expression outward.

---

## Worked Example: RE `(ab | ba)*` → ε-NFA

**Step 1:** Build NFA for `a`:

```
q₀ ──a──→ q₁
```

**Step 2:** Build NFA for `ab` (concatenate `a` and `b`):

```
q₀ ──a──→ q₁ ──b──→ q₂
```

**Step 3:** Build NFA for `b`:

```
q₀ ──b──→ q₁
```

**Step 4:** Build NFA for `ba` (concatenate `b` and `a`):

```
q₃ ──b──→ q₄ ──a──→ q₅
```

**Step 5:** Combine `ab | ba` with union construction — add new start state `q₀'` with ε-transitions into both machines:

```
       ε ──→ q₀ ──a──→ q₁ ──b──→ q₂ (accept₁)
q₀'
       ε ──→ q₃ ──b──→ q₄ ──a──→ q₅ (accept₂)
```

**Step 6:** Apply the star construction — add a new start/accept state `q_s` with ε into `q₀'`, and ε-transitions from both accept states back to `q₀'`:

```
              ε ──→  q₀ ──a──→ q₁ ──b──→ q₂ ──ε──┐
q_s ──ε──→ q₀'                                         ──ε──→ q_s
              ε ──→  q₃ ──b──→ q₄ ──a──→ q₅ ──ε──┘
(q_s is accept)
```

This ε-NFA accepts exactly the language of `(ab | ba)*`.

---

## Practice Problems: RE → NFA

Construct equivalent ε-NFAs (then convert to DFA if required) for:

1. `bb(aa)*bb`
2. `(a|b)*aba`
3. `(a|ba)*`
4. `(a|b)*abb(a|b)*`
5. `(b*ab*ab*)*`

---

## Part 2: FA → RE

For this direction, see **Arden's Theorem** on the next page, which gives a systematic algebraic method for converting any DFA to an equivalent regular expression.
