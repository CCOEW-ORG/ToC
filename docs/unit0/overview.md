# Overview & Motivation

## Why Theory of Computation?

Before building machines that compute, it is worth stepping back and asking what *computation* itself means. Theory of Computation (ToC) provides the mathematical tools to:

1. **Define computation precisely.** What counts as a valid computational step? What does it mean to "solve" a problem?
2. **Classify problems by difficulty.** Some problems are easy; some are hard; some are provably impossible for any machine to solve. ToC gives us the language to talk about these distinctions rigorously.
3. **Identify fundamental problems.** Many apparently different problems turn out to be equivalent at a deeper level. ToC lets us reduce one problem to another and understand their shared essence.
4. **Understand the limits of machines.** No matter how fast a computer becomes, certain problems remain forever unsolvable. These are not engineering limitations — they are mathematical facts.

### Core Questions This Course Explores

- What is computation? What does it mean to "compute" something?
- How do you check whether a statement is correct (decidability)?
- What is computational complexity? What is computability?
- How far can we push a given computational model? Where does it break?
- Are problems compressible to more fundamental abstract problems? What is the fundamental computational problem?

---

## The Journey Through This Course

We will build up our theory in layers, each layer adding more computational power:

```
Finite Automata  →  Pushdown Automata  →  Turing Machines
    (Unit 1)            (Unit 4)              (Unit 5)
      ↑                   ↑                     ↑
Regular Languages   Context-Free Lang.    Recursively Enumerable
    (Unit 2)            (Unit 3)              Languages
```

At each stage we ask: *What languages can this machine recognize? What can it not?*

!!! tip "The Central Question of ToC"
    Nearly every topic in this course is, at its core, an answer to one deceptively simple question:

    **Given a string `w` and a language `L`, does `w` belong to `L`?**

    This is the *set membership problem*, and the different computational models we study are different strategies for solving it — each with different power and different limitations.
