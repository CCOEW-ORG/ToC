# Introduction to Formal Languages & Finite Automata

## What Is a Machine (in the ToC Sense)?

A **machine** is an abstract mechanical model of a function. It takes some input and produces some output:

```
Input  ──→  Machine  ──→  Output
```

For our purposes, the internal workings of a machine are understood as purely mechanical symbol-manipulation processes. Whether a physical implementation is feasible is irrelevant — we are studying *abstract* machines.

### The Simplest Machine: A String Acceptor

The most basic machine is one whose only job is to decide whether an input string is "acceptable":

```
Input string  ──→  Machine  ──→  {Accept, Reject}
```

The machine reads the input string one symbol at a time. After reading the last symbol, it halts. If it ends in a designated **accept state**, the string is accepted; otherwise it is rejected.

The set of all strings accepted by a machine `M` is called the **language recognized by M**, written `L(M)`.

---

## Finite State Machine (Finite Automaton)

A **Finite State Machine (FSM)** — also called a **Finite Automaton (FA)** — is the simplest string acceptor. Its defining characteristics are:

- It has a **finite** amount of memory — just enough to remember which *state* it is currently in.
- It reads its input **left to right**, one symbol at a time, and **cannot go back**.
- After each symbol, it transitions to a new state (determined by its transition function).

### What FSMs Can and Cannot Count

Because an FSM has only finitely many states, there is a hard limit on what it can "remember":

| FSMs **can** count/check | FSMs **cannot** count/check |
|---|---|
| Even number of 1s | Equal number of 0s and 1s |
| Number of symbols mod k | Count up to an input value `n` |
| Exactly three 0s | Whether a string is a palindrome of arbitrary length |
| Whether a pattern appears | Whether `n` copies of one symbol are followed by `n` copies of another |

The key insight: an FSM can only track a fixed, finite amount of information. Anything requiring *unbounded counting* is beyond its reach.

---

## Machines and Languages: A Sneak Peek

Before diving into the formal definitions, here is a preview of how the three main language-representation tools relate:

### Grammar (Constructor)

Given `Σ = {a, b}`, build the language `L = {ε, ab, aabb, aaabbb, …} = {aⁿbⁿ | n ≥ 0}`:

```
Start symbol:       S
Terminals:          {a, b}
Non-terminals:      {S}
Production rules:   S → aSb | ε
```

This grammar *generates* strings in `L` by repeatedly applying the production rules.

### Machine / Regular Expression (Acceptor)

Given `Σ = {a, b}`, recognize all strings containing `bab` as a substring:

```
Regex:  (a|b)* bab (a|b)*
```

Or equivalently, a finite automaton that scans the input and accepts if and only if `bab` appears somewhere in the string.

!!! tip "The Big Picture"
    - Grammars **build** languages from the inside out.
    - Machines and regexes **check** strings from the outside in.
    - For the classes of languages we study in Units 1–3, both views describe *exactly the same set of languages*. Proving that equivalence is a central goal of this course.
