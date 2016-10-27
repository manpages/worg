Worg Denotational Semantics
===

μ ∷ (Foldable b, Monoid t, Functor t) => ∀a . *Quantification* → (Experience → t (b a))

We call _t (b a)_ “_Quantifier_”.

The inner foldable layer of Quantifier is intended to store data,
primarily *TimeRange*s, along with some structure. An example of using
this generalization is useful is tracking composite Experiences such as
metings. While tracking a meeting, operator or software used to perform
the meeting can keep track of how long was each participant talking,
yielding a structure akin to *[(Participant, TimeRange)]*. This data
structure might be more complex if, for example, participants have a
hierarchy or roles. This structure has to be foldable for us to be able
to discard the structure while preserving the data in order to generate
a Report.

The outer monoid layer of a Quantifier is required for merging
Quantifiers that carry foldable data of the same type together. The
outer layer is a slice in the multiverse of experience types. For
instance, one can describe their experiences and activities as the
following tree:

```

Activities
  Self-development
    Lerning
      Mathematics
      ...
      StarCraft
      ...
    Excercising
      Mathematics
      ...
      Athletics
      ...
  Creation
    Original
      Worg
      ...
    Derivative
      RSCoin
      ...
  Overhead
    Fun
      Walks
      ...
      Friends
      ...
    Obligations
      Cross-department management
      ...
```

This defines a universe of activities, a point in this universe is a
path in the tree from root to a given node (not neccessarily a leaf).
The same person can have another way to look at their life that is
completely unrelated to the presented tree. Some of (ideally, most of)
experiences, however, inhabit both universes at the same time and the
foldable data recorded should be tracked in both. Another person might
have a simple structure that describes their experiences and activities,
for example a list:

```
[ Eat, Pray, Love ]
```  
  
No matter how complex of a system a person uses to describe their life,
outer monoid layer will be able to eventually be used to populate all
the universes with folded data, iterating over collected Quantifiers via
_mappend_. We are using Monoid on the outer layer instead of Semigroup
because we are mostly working with *TimeRange*s, so notion of zero makes
sense and corresponds to the 0-length TimeRange.

Outer layer also has to be a Functor because in the process of
generating Reports, we will have to fold and coerce foldable data
carried by Quantifiers, so if the outer layer isn't also a Functor, we
will end up with the following:

μ ∷ (Foldable b, Monoid t) => ∀a∀d . *Reduce* d → (b a → d) → t (b a) → t d

Which is exactly _fmap_.

---

μ ∷ (Foldable f, Mergable t) => ∀d∀r . *Report* d r → (f (t d) → r)

Report is a particular fold over reduced (unlifted) _Quantifiers_.

---

type Experience
type TimeRange
