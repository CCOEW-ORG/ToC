# Mathematical Foundations

This section reviews the mathematical vocabulary used throughout the course. These are not new ideas — you have seen most of them in Discrete Mathematics — but the ToC framing is worth making explicit.

---

## Sets

A **set** is an *unordered* collection of distinct objects. The two defining properties are:

- **Order does not matter:** $\{1, 2, 3\} = \{3, 2, 1\}$
- **Duplicates do not matter:** $\{1, 2, 2, 3\} = \{1, 2, 3\}$

Sets can be described by listing elements, or by a defining property:

$$A = \{1, 2, 3\}$$

$$B = \{x \mid x \text{ is a prime number ending in } 3\}$$

$$S = \{x \mid x \text{ is an even number less than } 10\} = \{2, 4, 6, 8\}$$

The **empty set** is written $\emptyset$ or $\{\}$.

### Power Set

The **power set** of $A$, written $\mathcal{P}(A)$ or $2^A$, is the set of all subsets of $A$.

**Example:** If $A = \{1, 2, 3\}$, then:

$$\mathcal{P}(A) = \{\emptyset,\ \{1\},\ \{2\},\ \{3\},\ \{1,2\},\ \{1,3\},\ \{2,3\},\ \{1,2,3\}\}$$

!!! tip "Size of the Power Set"
    If $|A| = n$, then $|\mathcal{P}(A)| = 2^n$. This grows fast: a set with 4 elements has 16 subsets, one with 10 elements has 1024.

---

## Sequences and Tuples

A **sequence** is an *ordered* list of objects where both order and duplicates matter:

- $(1, 2, 3) \neq (3, 2, 1)$ — order matters
- $(1, 2, 3) \neq (1, 2, 3, 3)$ — duplicates matter

A **tuple** is a *finite* sequence. Tuples of specific sizes have conventional names:

| Size | Name |
|---|---|
| 1 | singleton (1-tuple) |
| 2 | pair (2-tuple) |
| 3 | triple (3-tuple) |
| 4 | quadruple |
| $n$ | $n$-tuple |

---

## Cartesian Product

The **Cartesian product** (or *cross product*) of sets $A_1, A_2, \ldots, A_k$ is the set of all $k$-tuples $(a_1, a_2, \ldots, a_k)$ where $a_i \in A_i$:

$$A_1 \times A_2 \times \cdots \times A_k = \{\ (a_1, a_2, \ldots, a_k) \mid a_i \in A_i \text{ for each } i\ \}$$

**Example:** Let $A = \{\text{Tea, Coffee}\}$, $B = \{\text{Toast, Eggs}\}$, $C = \{\text{Apple, Banana}\}$. Then $A \times B \times C$ contains all 8 triples such as $(\text{Tea, Toast, Apple})$, $(\text{Coffee, Eggs, Banana})$, and so on.

The size of a Cartesian product is:

$$|A_1 \times A_2 \times \cdots \times A_k| = |A_1| \cdot |A_2| \cdot \ldots \cdot |A_k|$$

---

## Functions

A **function** is a rule that assigns to each element of the *domain* exactly one element of the *range*:

$$f : D \to R$$

meaning $f$ maps domain $D$ to range $R$, with $f(\text{input}) = \text{output}$ where $\text{input} \in D$ and $\text{output} \in R$.

The domain can be a Cartesian product, giving functions of multiple arguments:

$$f(a_1, a_2, \ldots, a_n) = \text{output}$$

The **arity** of such a function is $n$ (the number of arguments).

!!! warning "Domain vs. Range vs. Codomain"
    In some textbooks, *range* refers only to the set of values *actually produced* by $f$, while *codomain* is the full set $R$ declared in $f : D \to R$. In ToC, the distinction rarely matters, but be aware of it when reading other sources.

---

## Graphs

A **graph** is an ordered pair $G = (V, E)$ where $V$ is a finite set of **vertices** (nodes) and $E$ is a set of **edges**.

- **Undirected graph** (no self-loops): $E \subseteq \big\{\, \{u, v\} \mid u, v \in V,\ u \neq v \,\big\}$
- **Directed graph** (digraph): $E \subseteq \big\{\, (u, v) \mid u, v \in V \,\big\}$ — here order matters, so $(u, v) \neq (v, u)$

We use directed graphs extensively when drawing **state transition diagrams** for automata.

---

## Closure

### Closed Under an Operation

A set $A$ is **closed under** an operation $\circ$ if applying $\circ$ to any two members of $A$ always produces another member of $A$:

$$\forall\, x, y \in A : x \circ y \in A$$

**Example:** The natural numbers $\mathbb{N}$ are closed under addition ($3 + 5 = 8 \in \mathbb{N}$) but *not* under subtraction ($3 - 5 = -2 \notin \mathbb{N}$).

### Closure of a Set

The **closure** of a set $S$ under a rule or operation is the *smallest* superset of $S$ that is closed under that rule — obtained by repeatedly applying the rule until no new elements can be generated.

**Examples:**

- The closure of $\{0, 1\}$ under addition is $\mathbb{W}$ (the whole numbers).
- The closure of $\{-1, 1\}$ under addition is $\mathbb{Z}$ (all integers).

!!! tip "Why Closure Matters in ToC"
    The concept of closure appears constantly. We will ask: *Is the set of regular languages closed under union? Under intersection? Under complement?* A positive answer means any combination of regular languages using that operation stays regular — a very useful property when designing machines.