# Properties of Context-Free Languages

## Closure Properties

A class of languages is **closed under an operation** if applying that operation to languages in the class always produces a language still in the class.

### CFLs ARE Closed Under

#### Union

If `L₁` and `L₂` are CFLs with grammars `G₁` and `G₂`, their union `L₁ ∪ L₂` is also a CFL.

**Construction:** Add a new start symbol `S_new` and rules `S_new → S₁ | S₂` (where `S₁` and `S₂` are the start symbols of `G₁` and `G₂`).

#### Concatenation

`L₁ · L₂` is a CFL. Add `S_new → S₁ S₂`.

#### Kleene Star

`L*` is a CFL for any CFL `L`. Add `S_new → S_new S | ε`.

### CFLs are NOT Closed Under

#### Intersection

!!! danger "CFLs are not closed under intersection"
    The intersection of two CFLs may not be a CFL.

    **Counterexample:**
    ```
    L₁ = {aⁿbⁿcᵏ | n, k ≥ 0}    (CFL — equal a's and b's, any c's)
    L₂ = {aᵏbⁿcⁿ | n, k ≥ 0}    (CFL — any a's, equal b's and c's)
    ```

    Both are CFLs (straightforward CFG constructions). But:
    ```
    L₁ ∩ L₂ = {aⁿbⁿcⁿ | n ≥ 0}
    ```

    This is the classic example of a **context-sensitive** (Type 1) language that is not context-free. It can be shown non-CFL using the Pumping Lemma for CFLs.

**Exception:** The intersection of a CFL with a **regular language** is always a CFL. (This is a useful and commonly tested fact.)

#### Complement

!!! danger "CFLs are not closed under complementation"
    If CFLs were closed under complement, and since they are closed under union, they would also be closed under intersection (by De Morgan's law: `L₁ ∩ L₂ = ¬(¬L₁ ∪ ¬L₂)`). But they are not closed under intersection — contradiction. Therefore, they cannot be closed under complement either.

### Summary Table

| Operation | Regular Languages | Context-Free Languages |
|---|---|---|
| Union | ✅ | ✅ |
| Concatenation | ✅ | ✅ |
| Kleene Star | ✅ | ✅ |
| Complement | ✅ | ❌ |
| Intersection | ✅ | ❌ |
| Intersection with Regular | ✅ (trivially) | ✅ |
| Difference | ✅ | ❌ |

---

## Decision Properties

A property of a language class is **decidable** if there is an algorithm that, given a finite description of a language (e.g., a grammar), always terminates and correctly answers "yes" or "no."

### Decidable for CFLs

| Property | Question | Algorithm |
|---|---|---|
| **Emptiness** | Is `L(G) = ∅`? | Check if start symbol is generating |
| **Finiteness** | Is `L(G)` finite? | Check for cycles in the "generates" relation |
| **Membership** | Is `w ∈ L(G)`? | CYK algorithm (`O(n³)` in the length of `w`) |

### Undecidable for CFLs

| Property | Question |
|---|---|
| **Equivalence** | Is `L(G₁) = L(G₂)`? |
| **Ambiguity** | Is grammar `G` ambiguous? |
| **Regularity** | Is `L(G)` also a regular language? |
| **Intersection emptiness** | Is `L(G₁) ∩ L(G₂) = ∅`? |

!!! warning "Undecidability"
    "Undecidable" does not mean "usually hard" — it means **no algorithm can exist** that correctly answers the question for all inputs. This is a mathematical impossibility, not a current engineering limitation. These results are proven using reductions from the Halting Problem (covered in Unit 5).

---

## The Pumping Lemma for CFLs

Just as regular languages have a Pumping Lemma, so do context-free languages — but the structure is different.

**Theorem (Pumping Lemma for CFLs):**

If `L` is a CFL, there exists a pumping length `p` such that every string `s ∈ L` with `|s| ≥ p` can be split as `s = uvxyz` where:

1. `|vy| ≥ 1` — at least one of `v` or `y` is non-empty.
2. `|vxy| ≤ p` — the middle section is bounded.
3. For all `i ≥ 0`: `uvⁱxyⁱz ∈ L` — we pump `v` and `y` together.

**Why the different structure?** In CFLs recognized by PDAs, the "pumping" corresponds to pumping a loop in the pushdown automaton — but the PDA stack means two parts of the string (surrounding the middle `x`) are pumped simultaneously.

**Example use:** Prove `L = {aⁿbⁿcⁿ | n ≥ 0}` is not a CFL.

Assume CFL with pumping length `p`. Take `s = aᵖbᵖcᵖ`. Any split `s = uvxyz` with `|vxy| ≤ p` means `vxy` spans at most two of the three symbol types. So `v` and `y` together contain only `a`'s and `b`'s, or only `b`'s and `c`'s.

Pumping `i = 2`: we increase the count of at most two symbol types, but not the third. The resulting string cannot have equal counts of all three — so it is not in `L`. Contradiction. ∎
