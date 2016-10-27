Worg Denotational Semantics
===

μ ∷ (Foldable b, Mergable t) => ∀a . *Quantification* → (Experience → t b a)

We call *t b a* _Quantifier_.

---

μ ∷ (Foldable b, Mergable t) => ∀a∀d . *Reduce* d → (b a → d) → t b a → t d

Open question — what really is _Reduce_? Looks familiar. Conceptually,
it's a way to unlift foldables, AHA! It's a result of a particular fold
under a Mergable. To get rid of explicitly defining Reduces, we need to
make Mergables we work with also instances of a Functor.

---

μ ∷ (Foldable f, Mergable t) => ∀d∀r . *Report* d r → (f t d → r)

Report is a particular fold over reduced (unlifted) _Quantifiers_.

---

```
class Mergable (t ∷ * → *) has
  merge ∷ (Monoid m) => t m → t m → t m
  mergeWith ∷ ∀a . (a → a) → t a → t a → t a
```

Open question — what really is _Mergable_? Looks familiar. It seems that
if extended with mergezero, Mergables are just Monoids. If we want to
get rid of it, we should see how to augument Monoid to conform with
API we want to have.
