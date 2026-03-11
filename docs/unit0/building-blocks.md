# Building Blocks of Computation

Every concept in Theory of Computation is built on a small set of primitive notions. This page defines them carefully — these definitions will be used without re-introduction throughout the rest of the course.

---

## Symbols, Alphabets, and Strings

### 1. Symbol

A **symbol** is an atomic, indivisible unit. It has no internal structure — it simply *is*.

Examples: $0,\ 1,\ a,\ b,\ \$,\ \cdot$

### 2. Alphabet ($\Sigma$)

An **alphabet** is a *finite, non-empty* set of symbols.

Examples:

$$\Sigma_1 = \{0, 1\} \quad \text{(binary alphabet)}$$

$$\Sigma_2 = \{a, b, c\}$$

$$\Sigma_3 = \{a, e, i, o, u\} \quad \text{(vowels)}$$

### 3. String ($w$)

A **string** over an alphabet $\Sigma$ is a *finite sequence* of symbols drawn from $\Sigma$.

If $\Sigma = \{a, b, c\}$, then $ab$, $bca$, $aaa$, and $c$ are all valid strings.

The **length** of a string $w$, written $|w|$, is the number of symbols it contains:

$$|abc| = 3 \qquad |aab| = 3 \qquad |a| = 1$$

#### The Empty String ($\varepsilon$)

The **empty string** $\varepsilon$ (epsilon) is the unique string of length zero: $|\varepsilon| = 0$. It contains no symbols but is still a valid string over any alphabet.

!!! warning "$\varepsilon$ is not a symbol"
    $\varepsilon$ is **not** a symbol in your alphabet. It is a string — specifically, the string with no symbols in it. The alphabet $\Sigma = \{a, b\}$ does not contain $\varepsilon$; it contains two symbols, $a$ and $b$.

### 4. Substring

A **substring** of a string $w$ is any *contiguous* portion of $w$.

If $w = abcde$, then $abc$, $bcd$, $cde$, $b$, $abcde$, and $\varepsilon$ are all substrings of $w$. But $ace$ is **not** a substring (the symbols are not contiguous).

### 5. Concatenation

The **concatenation** of strings $w_1$ and $w_2$, written $w_1 w_2$ or $w_1 \cdot w_2$, is the string formed by placing $w_2$ immediately after $w_1$:

$$w_1 = abc,\quad w_2 = def \quad \Rightarrow \quad w_1 w_2 = abcdef$$

Properties of concatenation:

- $|w_1 w_2| = |w_1| + |w_2|$
- $w \cdot \varepsilon = \varepsilon \cdot w = w \quad$ ($\varepsilon$ is the identity element)
- Concatenation is **associative**: $(w_1 w_2) w_3 = w_1 (w_2 w_3)$
- Concatenation is **not commutative** in general: $ab \neq ba$

---

## Operations on Alphabets

### 6. Kleene (Star) Closure: $\Sigma^*$

The **Kleene closure** $\Sigma^*$ is the set of *all possible strings* over $\Sigma$, **including** the empty string $\varepsilon$. Formally:

$$\Sigma^* = \bigcup_{n=0}^{\infty} \Sigma^n$$

where $\Sigma^n$ is the set of all strings of length exactly $n$:

$$\Sigma^0 = \{\varepsilon\}$$

$$\Sigma^1 = \{a, b\} \quad \text{(for } \Sigma = \{a,b\}\text{)}$$

$$\Sigma^2 = \{aa, ab, ba, bb\}$$

$$\Sigma^3 = \{aaa, aab, aba, abb, baa, bab, bba, bbb\}$$

$$\Sigma^* = \Sigma^0 \cup \Sigma^1 \cup \Sigma^2 \cup \cdots = \{\varepsilon,\ a,\ b,\ aa,\ ab,\ ba,\ bb,\ aaa,\ \ldots\}$$

!!! tip "Key Fact"
    $\Sigma^*$ is always **infinite** (as long as $\Sigma \neq \emptyset$). This is why we need finite *representations* of languages — see below.

**Proof sketch:** Any string of length $k$ over $\Sigma$ is an element of $\Sigma^k$, and $\Sigma^k \subseteq \Sigma^*$ by definition of the union. Therefore every finite string over $\Sigma$ is in $\Sigma^*$.

### 7. Positive Closure: $\Sigma^+$

The **positive closure** $\Sigma^+$ is the set of all strings over $\Sigma$, **excluding** the empty string:

$$\Sigma^+ = \Sigma^* \setminus \{\varepsilon\} = \Sigma^1 \cup \Sigma^2 \cup \Sigma^3 \cup \cdots$$

---

## Languages

A **language** $L$ is any subset of $\Sigma^*$:

$$L \subseteq \Sigma^*$$

**Examples** over $\Sigma = \{0, 1\}$:

$$L_1 = \{0, 00, 000, 0000, \ldots\} \quad \text{— strings of only 0s (non-empty)}$$

$$L_2 = \{\varepsilon, 11, 101, 1001, 10001, \ldots\} \quad \text{— a particular infinite set}$$

$$L_3 = \emptyset \quad \text{— the empty language}$$

$$L_4 = \{0, 1\} \quad \text{— a 2-element language}$$

$$L_5 = \Sigma^* \quad \text{— all strings}$$

**How many languages exist over $\Sigma = \{0,1\}$?** $\Sigma^*$ is countably infinite. The set of all subsets of a countably infinite set is *uncountably infinite*. So there are uncountably many possible languages — far more than can ever be described by any finite notation or recognized by any machine.

### What Languages Are (and Are Not) in ToC

In formal language theory, a language is defined *purely through syntax* — symbol manipulation rules with no intrinsic meaning attached:

| Formal languages **ignore** | Formal languages **only care about** |
|---|---|
| Meaning / semantics | Syntax |
| Interpretation | Structure |
| Truth | Generation rules / acceptance rules |
| Intent | |
| Context | |

**Analogy:** A compiler does not *understand* your program. It checks whether your source code string *conforms* to the grammar rules of the programming language. That is a formal language acceptance problem.

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
| **Language constructors** | Grammars | Generate strings that belong to $L$ |
| **Language acceptors** | Machines (Automata), Regular Expressions | Decide whether a given string belongs to $L$ |

```
    Grammar
(constructor)
        ↓
  Input string ──→  Machine / Regex ──→ {Accept, Reject}
                        (acceptor)
```

These two approaches are deeply connected — for regular languages, grammars, machines, and regular expressions all describe the *same* class of languages. Showing these equivalences is one of the main themes of Units 1–3.