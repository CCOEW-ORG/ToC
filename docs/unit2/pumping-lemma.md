# Pumping Lemma for Regular Languages

## Motivation

We have shown that many languages *are* regular. But how do we prove that a language *is not* regular?

We cannot just say "I tried to build an FA and couldn't" — maybe we weren't clever enough. We need a *mathematical proof of impossibility*.

The **Pumping Lemma** provides exactly this tool. It states a property that every regular language *must* have. If a language violates this property, it cannot be regular.

---

## The Pumping Lemma

**Theorem (Pumping Lemma for Regular Languages):**

If `L` is a regular language, then there exists a constant `p ≥ 1` (the **pumping length**) such that every string `s ∈ L` with `|s| ≥ p` can be split into three parts:

```
s = x · y · z
```

satisfying all three of the following conditions:

1. `|xy| ≤ p` — the first two parts together are at most `p` symbols long.
2. `|y| ≥ 1` — the middle part `y` is non-empty.
3. For all `i ≥ 0`: `xyⁱz ∈ L` — we can "pump" `y` any number of times (including zero) and stay in `L`.

Here `yⁱ` means `y` repeated `i` times: `y⁰ = ε`, `y¹ = y`, `y² = yy`, etc.

### Why Does This Hold for Regular Languages?

If `L` is regular, some DFA `M` with `p` states accepts `L`. When `M` processes a string `s` with `|s| ≥ p`, it visits at least `p+1` states — but there are only `p` states. By the **Pigeonhole Principle**, some state `q` must be visited *twice* in the first `p+1` steps.

Let `x` be the input consumed up to the first visit to `q`, let `y` be the input consumed between the two visits to `q` (this is non-empty, giving condition 2), and let `z` be the remainder.

Since `y` takes the machine from `q` back to `q`, we can loop `y` any number of times and still end up at `q`. Since `z` then takes the machine from `q` to an accept state (as originally), `xyⁱz ∈ L` for all `i ≥ 0`.

!!! warning "The Pumping Lemma is a necessary, not sufficient, condition"
    The Pumping Lemma says: if `L` is regular, then it can be pumped. The contrapositive is: if `L` cannot be pumped, then `L` is not regular. **However**, some non-regular languages can be pumped too — so being "pumpable" does not prove a language is regular.

---

## Using the Pumping Lemma (Proof Structure)

To prove that language `L` is **not** regular using the Pumping Lemma:

1. **Assume** for contradiction that `L` is regular, and let `p` be its pumping length.
2. **Choose** a specific string `s ∈ L` with `|s| ≥ p`. (Choose `s` cleverly — make it hard to pump.)
3. **Consider all possible ways** to split `s = xyz` satisfying `|xy| ≤ p` and `|y| ≥ 1`.
4. **For each such split**, show that there exists some `i ≥ 0` such that `xyⁱz ∉ L`.
5. **Conclude** that no valid pumping decomposition exists, contradicting the Pumping Lemma. Therefore `L` is not regular.

---

## Worked Example 1: `L = {aⁿbⁿ | n ≥ 0}`

**Claim:** `L` is not regular.

**Proof:**

Assume `L` is regular with pumping length `p`. Choose `s = aᵖbᵖ ∈ L` (this has length `2p ≥ p`).

By the Pumping Lemma, `s = xyz` with `|xy| ≤ p` and `|y| ≥ 1`.

Since `|xy| ≤ p`, the substring `xy` lies entirely within the first `p` symbols of `s`, which are all `a`'s. Therefore `y = aᵏ` for some `k ≥ 1`.

Now consider `i = 2`: `xy²z = x·aa...a·y·z = aᵖ⁺ᵏbᵖ`.

This string has `p + k` copies of `a` but only `p` copies of `b`. Since `k ≥ 1`, we have `p + k ≠ p`, so `xy²z ∉ L`.

This contradicts the Pumping Lemma. Therefore `L` is **not regular**. ∎

---

## Worked Example 2: `L = {w ∈ {a,b}* | w is a palindrome}`

**Claim:** `L` is not regular.

**Proof:**

Assume `L` is regular with pumping length `p`. Choose `s = aᵖbaᵖ` (a palindrome of length `2p + 1 ≥ p`).

Since `|xy| ≤ p`, `xy` lies entirely in the leading block of `a`'s. So `y = aᵏ` for some `k ≥ 1`.

Consider `i = 0`: `xy⁰z = xz = aᵖ⁻ᵏbaᵖ`.

For this to be a palindrome, we need it to read the same forwards and backwards. The string has `p - k` copies of `a`, then `b`, then `p` copies of `a`. Since `k ≥ 1`, `p - k < p`, so it is **not** a palindrome.

Therefore `xy⁰z ∉ L`, contradicting the Pumping Lemma. Therefore `L` is **not regular**. ∎

---

## Worked Example 3: `L = {0ⁿ | n is a perfect square}`

**Claim:** `L` is not regular.

**Proof:**

Assume `L` is regular with pumping length `p`. Choose `s = 0^(p²) ∈ L`.

Split `s = xyz` with `|xy| ≤ p` and `|y| = k ≥ 1`. Then `y = 0ᵏ`.

Consider `xyⁱz = 0^(p² + (i-1)k)` for various `i`. For this to stay in `L`, `p² + (i-1)k` must be a perfect square for all `i ≥ 0`.

In particular, consider `i = 2`: the string has length `p² + k`. We need `p² + k` to be a perfect square. But `p² < p² + k ≤ p² + p < (p+1)²` — the length falls *strictly between* two consecutive perfect squares. Therefore `p² + k` is not a perfect square, so `xy²z ∉ L`.

This contradicts the Pumping Lemma. Therefore `L` is **not regular**. ∎

---

## Practice Problems

Use the Pumping Lemma to show the following languages are not regular:

1. `L = {0^m 1^n | m > n}`
2. `L = {aⁿ | n is prime}`
3. `L = {ww | w ∈ {a,b}*}` (strings that are their own repetition)
4. `L = {1^(n²) | n ≥ 0}`
