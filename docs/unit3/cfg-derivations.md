# Context-Free Grammars & Derivations

## Context-Free Grammars (CFGs)

A **context-free grammar (CFG)** is a grammar in which every production rule has the form:

```
A → α
```

where `A ∈ V` is a single non-terminal, and `α ∈ (V ∪ Σ)*` is any string of terminals and/or non-terminals (including the empty string `ε`).

The key difference from regular grammars: the right-hand side can have **any** combination of terminals and non-terminals, including multiple non-terminals and nested structures. This is what enables CFGs to describe languages like `{aⁿbⁿ}` that require matching/counting.

The "context-free" name comes from the fact that the non-terminal `A` can be replaced by `α` **regardless** of what surrounds it (the context does not affect which rules can be applied).

---

## Examples of CFGs

### Example 1: `L = {aⁿbⁿ | n ≥ 0}`

```
G = ({S}, {a, b}, {S → aSb | ε}, S)
```

Derivation of `aaabbb`:
```
S ⟹ aSb ⟹ aaSbb ⟹ aaaSbbb ⟹ aaabbb
```

### Example 2: Properly Matched Parentheses (Dyck Language)

```
S → SS | (S) | ε
```

Derivation of `(()())`:
```
S ⟹ (S) ⟹ (SS) ⟹ ((S)S) ⟹ (()S) ⟹ (()(S)) ⟹ (()())
```

### Example 3: Arithmetic Expressions

```
E → E + T | T
T → T * F | F
F → (E) | id
```

This grammar generates all valid arithmetic expressions with `+`, `*`, parentheses, and identifiers (variables/numbers).

### Example 4: Strings Where 1s Always Outnumber 0s

Language: `{ w ∈ {0,1}* | at every prefix of w, #1s > #0s }` (Ballot sequences / Bertrand ballot problem variant).

```
S → 1A
A → 1A0A | 1A | ε
```

!!! tip "CFGs vs Regular Grammars"
    Regular grammars can only build strings by appending symbols to one end. CFGs can "wrap" structure around the middle, which is why they can handle balanced/nested constructs that are impossible for FAs.

---

## Grammar Derivations

### Sentential Forms

A **sentential form** is any string (of terminals and/or non-terminals) that can be derived from the start symbol `S`. Formally, `α` is a sentential form if `S ⟹* α`.

A string consisting entirely of terminals that is derivable from `S` is a **sentence** of the grammar (i.e., a string in `L(G)`).

### Leftmost Derivation (LMD)

In a **leftmost derivation**, at each step we always expand the **leftmost** non-terminal in the current sentential form.

**Example** with `E → E + T | T`, `T → T * F | F`, `F → (E) | id`:

Leftmost derivation of `id + id * id`:
```
E ⟹ E + T           (expand leftmost E)
  ⟹ T + T           (expand leftmost E → T)
  ⟹ F + T           (expand leftmost T → F)
  ⟹ id + T          (expand F → id)
  ⟹ id + T * F      (expand T → T * F)
  ⟹ id + F * F      (expand T → F)
  ⟹ id + id * F     (expand F → id)
  ⟹ id + id * id    (expand F → id)
```

### Rightmost Derivation (RMD)

In a **rightmost derivation**, we always expand the **rightmost** non-terminal.

**Example:** Same grammar, deriving `id + id * id`:
```
E ⟹ E + T
  ⟹ E + T * F
  ⟹ E + T * id
  ⟹ E + F * id
  ⟹ E + id * id
  ⟹ T + id * id
  ⟹ F + id * id
  ⟹ id + id * id
```

!!! tip "Why do derivation orders matter?"
    Both leftmost and rightmost derivations produce the same set of sentences. However, parsers in compilers work in a specific order — LL parsers use leftmost derivation, LR parsers use rightmost (in reverse). The choice of derivation order can also reveal ambiguity in a grammar.

---

## Parse Trees

A **parse tree** is a hierarchical, visual representation of a derivation.

**Structure:**
- The **root** is the start symbol `S`.
- **Internal nodes** are non-terminals (`V`).
- **Leaf nodes** are terminals (`Σ`) or `ε`.
- The **children** of a node `A` in the parse tree correspond to the right-hand side of the rule applied to `A` in the derivation.
- Reading the leaves from left to right gives the derived string (the **yield** of the tree).

### Key Property

A parse tree captures the *structure* of a derivation without specifying the *order* in which rules were applied. Both the leftmost and rightmost derivations of the same string (with the same rule choices) give the same parse tree.

**A grammar is unambiguous if and only if every string in `L(G)` has exactly one parse tree.**

### Example Parse Tree

Grammar: `S → SS | (S) | ε`  
String: `(())()`

One derivation: `S ⟹ SS ⟹ (S)(S) ⟹ ((S))(S) ⟹ (())(S) ⟹ (())()`.

Parse tree:
```
         S
        / \
       S   S
       |   |
      (S)  ()
       |
      (S)
       |
       ε
```

---

## CFG Applications

### Palindromes

CFGs can "remember" the first half of a string to match against the second half — something no FA can do:

```
S → aSa | bSb | a | b | ε
```

This generates all palindromes over `{a, b}`. The key is that the rules `aSa` and `bSb` allow the grammar to "wrap" matching symbols around the recursive structure.

### Parenthesis Matching

The Dyck language (all properly matched parentheses) is a context-free language:

```
S → SS | (S) | ε
```

This language is not regular (you can prove this with the Pumping Lemma: the string `(ⁿ)ⁿ` cannot be pumped within the regular framework).
