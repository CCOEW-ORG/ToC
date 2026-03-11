# Regular Expressions

## Informal Introduction

In arithmetic, expressions like `5 × 4 + 3(32 ÷ 16)` are built from numbers and operators. When *evaluated*, they yield a numeric result.

**Regular expressions (regexes)** work analogously, but:

- The "numbers" are languages (sets of strings).
- The "operators" are the three regular operations: union (`|` or `+`), concatenation (`.` or juxtaposition), and star (`*`).
- When evaluated, a regex yields a **language**.

---

## Formal Definition (Inductive)

A **regular expression** over alphabet Σ is defined inductively:

| Form | Description | Language Denoted |
|---|---|---|
| `a` where `a ∈ Σ` | Atomic symbol | `{a}` |
| `ε` | Empty string | `{ε}` |
| `∅` | Empty set | `{}` |
| `(R₁ \| R₂)` | Union of `R₁` and `R₂` | `L(R₁) ∪ L(R₂)` |
| `(R₁ · R₂)` | Concatenation of `R₁` and `R₂` | `L(R₁) ∘ L(R₂)` |
| `(R₁*)` | Kleene star of `R₁` | `L(R₁)*` |

Everything else is syntactic sugar built on top of these six forms.

!!! warning "∅ vs ε"
    - `∅` denotes the **empty language** (no strings at all). A machine for `∅` never accepts.
    - `ε` denotes the language containing **only the empty string** `{ε}`. A machine for `ε` accepts only the empty input.
    These are completely different.

---

## Expansion Order (Precedence)

Analogous to BODMAS in arithmetic, regular expression operators have precedence:

1. **Parentheses** — innermost first
2. **Star** (`*`) — highest precedence among operators
3. **Concatenation** (`.`) — middle precedence
4. **Union** (`|`) — lowest precedence

**Example:** `ab*|c` is parsed as `(a(b*)) | c`, **not** as `a(b* | c)` or `(ab)* | c`.

---

## Worked Examples

### Example 1

```
a*b*
```

"Zero or more `a`'s followed by zero or more `b`'s."

```
L = {ε, a, b, aa, ab, bb, aaa, aab, abb, bbb, …}
```

### Example 2

```
(a|b)*
```

"Zero or more symbols, each of which is either `a` or `b`." This is the set of **all strings** over `{a, b}`:

```
L = {ε, a, b, aa, ab, ba, bb, aaa, …} = {a, b}*
```

### Example 3

```
(b*ab*ab*)*
```

Break it down: `b*ab*ab*` is any string with exactly two `a`'s (and any number of `b`'s before, between, and after). The outer star means we can have **any number of repetitions** of this, giving strings with an even number of `a`'s:

```
L = {ε, aa, baa, aba, aab, baab, baba, …} = {w | w has an even number of a's}
```

### Example 4

```
(a|b)*a(a|b)(a|b)
```

"Any string ending in a symbol `a` at position 3 from the right." The third character from the end must be `a`; the last two can be anything:

```
L = {aaa, aab, aba, abb, baaa, baab, …}
     = {w | the third symbol from the right of w is 'a'}
```

### Example 5 (Combined)

```
(0 ∪ 1) · 11 · (0 ∪ 1)
```

Evaluated step by step:

- `(0 ∪ 1) = {0, 1}`
- `11 = {11}` (the string "eleven")
- `(0 ∪ 1) · 11 · (0 ∪ 1) = {0110, 1110, 0111, 1111}`

---

## Common Regex Patterns

| Regex | Language |
|---|---|
| `(0|1)*` | All binary strings |
| `(0|1)*1` | Binary strings ending in 1 |
| `0*10*` | Binary strings with exactly one 1 |
| `(0|1)*11(0|1)*` | Binary strings containing `11` |
| `(aa)*` | Strings of `a`'s with even length |
| `(a|b)*aba(a|b)*` | Strings over `{a,b}` containing `aba` |

---

## Practice Problems

Write regular expressions for the following languages over `Σ = {a, b}`:

1. All strings that start and end with `a`.
2. All strings of odd length.
3. All strings that do **not** contain `bb` as a substring.
4. All strings with at least two `b`'s.
5. All strings where every `a` is immediately followed by a `b`.
