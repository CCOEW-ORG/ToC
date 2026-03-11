# Building Blocks of Computation

Every concept in Theory of Computation is built on a small set of primitive notions. This page defines them carefully — these definitions will be used without re-introduction throughout the rest of the course.

---

## Symbols, Alphabets, and Strings

### 1. Symbol

A **symbol** is an atomic, indivisible unit. It has no internal structure — it simply *is*.

Examples: `0`, `1`, `a`, `b`, `$`, `·`

### 2. Alphabet (Σ)

An **alphabet** is a *finite, non-empty* set of symbols.

Examples:

```
Σ₁ = {0, 1}          — binary alphabet
Σ₂ = {a, b, c}
Σ₃ = {a, e, i, o, u}  — vowels
```

### 3. String (w)

A **string** over an alphabet Σ is a *finite sequence* of symbols drawn from Σ.

If `Σ = {a, b, c}`, then `ab`, `bca`, `aaa`, and `c` are all valid strings.

The **length** of a string `w`, written `|w|`, is the number of symbols it contains:

```
|abc| = 3
|aab| = 3
|a|   = 1
```

#### The Empty String (ε)

The **empty string** `ε` (epsilon) is the unique string of length zero: `|ε| = 0`. It contains no symbols but is still a valid string over any alphabet.

!!! warning "ε is not a symbol"
    `ε` is **not** a symbol in your alphabet. It is a string — specifically, the string with no symbols in it. The alphabet `Σ = {a, b}` does not contain `ε`; it contains two symbols, `a` and `b`.

### 4. Substring

A **substring** of a string `w` is any *contiguous* portion of `w`.

If `w = abcde`, then `abc`, `bcd`, `cde`, `b`, `abcde`, and `ε` are all substrings of `w`. But `ace` is **not** a substring (the symbols are not contiguous).

### 5. Concatenation

The **concatenation** of strings `w₁` and `w₂`, written `w₁w₂` or `w₁ · w₂`, is the string formed by placing `w₂` immediately after `w₁`.

```
w₁ = abc,  w₂ = def  →  w₁w₂ = abcdef
```

Properties of concatenation:

- `|w₁w₂| = |w₁| + |w₂|`
- `w · ε = ε · w = w`  (ε is the identity element)
- Concatenation is **associative**: `(w₁w₂)w₃ = w₁(w₂w₃)`
- Concatenation is **not commutative** in general: `ab ≠ ba`

---

## Operations on Alphabets

### 6. Kleene (Star) Closure: Σ\*

The **Kleene closure** `Σ*` is the set of *all possible strings* over Σ, **including** the empty string `ε`.

Formally, `Σ* = ⋃_{n=0}^{∞} Σⁿ`, where `Σⁿ` is the set of all strings of length exactly `n`:

```
Σ⁰ = {ε}
Σ¹ = {a, b}         (for Σ = {a, b})
Σ² = {aa, ab, ba, bb}
Σ³ = {aaa, aab, aba, abb, baa, bab, bba, bbb}
...

Σ* = Σ⁰ ∪ Σ¹ ∪ Σ² ∪ … = {ε, a, b, aa, ab, ba, bb, aaa, …}
```

!!! tip "Key Fact"
    `Σ*` is always **infinite** (as long as `Σ ≠ ∅`). This is why we need finite *representations* of languages — see below.

**Proof sketch that every finite string is in Σ\*:** Any string of length `k` over Σ is an element of `Σᵏ`, and `Σᵏ ⊆ Σ*` by definition of the union.

### 7. Positive Closure: Σ⁺

The **positive closure** `Σ⁺` is the set of all strings over Σ, **excluding** the empty string:

```
Σ⁺ = Σ* \ {ε}  =  Σ¹ ∪ Σ² ∪ Σ³ ∪ …
```

---

## Languages

A **language** `L` is any subset of `Σ*`:

```
L ⊆ Σ*
```

**Examples** over `Σ = {0, 1}`:

```
L₁ = {0, 00, 000, 0000, …}         — strings of only 0s (non-empty)
L₂ = {ε, 11, 101, 1001, 10001, …}  — a particular infinite set
L₃ = {}  = ∅                        — the empty language
L₄ = {0, 1}                         — a 2-element language
L₅ = Σ*                             — all strings, an infinite language
```

**How many languages are there over `Σ = {0, 1}`?** `Σ*` is countably infinite (it can be enumerated). The set of all subsets of a countably infinite set is *uncountably infinite*. So there are uncountably many languages over any non-trivial alphabet — far more than can ever be described by any finite notation or recognized by any machine.

### What Languages Are (and Are Not) in ToC

In formal language theory, a language is defined *purely through syntax* — symbol manipulation rules with no meaning attached:

| Formal languages **ignore** | Formal languages **only care about** |
|---|---|
| Meaning / semantics | Syntax |
| Interpretation | Structure |
| Truth | Generation rules / acceptance rules |
| Intent | |
| Context | |

**Analogy:** A compiler does not *understand* your program. It checks whether your source code string *conforms* to the grammar rules of the programming language. That's a formal language acceptance problem.

---

## Finite Representation of Languages

### The Problem

Languages are often infinite. We cannot store or manipulate an infinite list of strings directly. Yet:

- English has infinitely many valid sentences.
- A programming language has infinitely many valid programs.
- We need computers to work with these languages.

**Only languages with a finite representation can be reasoned about and recognized algorithmically.**

### The Solution: Constructors and Acceptors

We represent infinite languages in two dual ways:

| Approach | Tool | Role |
|---|---|---|
| **Language constructors** | Grammars | Generate strings that belong to L |
| **Language acceptors** | Machines (Automata), Regular Expressions | Decide whether a given string belongs to L |

```
                         Grammar
                        (constructor)
                             ↓
  Input string ──→  Machine / Regex ──→ {Accept, Reject}
                        (acceptor)
```

These two approaches are deeply connected — for regular languages, grammars, machines, and regular expressions all describe the *same* class of languages. Showing these equivalences is one of the main themes of Units 1–3.
