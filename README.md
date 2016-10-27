Worg Denotational Semantics
===

### Meanings

μ ∷ (Foldable b, Monoid t, Functor t) => ∀a . *Quantification* → (Experience → t (b a))

We call _t (b a)_ “_Quantifier_”.

The inner foldable layer of Quantifier is intended to store data,
primarily *TimeRange*s, along with some structure. An example of using
this generalization is useful is tracking composite experiences such as
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
outer monoid layer will be able to eventually be used (???!!!) to
populate all the universes with folded data, iterating over collected
Quantifiers via _mappend_. We are using Monoid on the outer layer
instead of Semigroup because we are mostly working with *TimeRange*s, so
notion of zero makes sense and corresponds to the 0-length TimeRange.

Outer layer also has to be a Functor because in the process of
generating Reports, we will have to fold and coerce foldable data
carried by Quantifiers, so if the outer layer isn't also a Functor, we
will end up with the following:

μ ∷ (Foldable b, Monoid t) => ∀a ∀d . *Reduce* d → (b a → d) → t (b a) → t d

Which is exactly _fmap_.

---

μ ∷ (Alternative f, Foldable f, Monoid t, Functor t) => ∀d ∀r . *Report* d r → (f (t d) → r)

With Quantifier specified, we see that Report is just a particular fold
over reduced Quantifiers.

---

### Types

type Experience

type TimeRange

---

### Functions

Machinery of _worg_ is all about the following functions:

```
conj ∷ (Alternative f, Foldable f) => a → f a → f a
conj x xs = pure x <|> xs
```

This function[1] is to be used while appending Quantifiers, building up a
Foldable which later will be folded by Report. As the result of applying
*conj*, we get a foldable structure per type carried by Quantifier.
Quantifiers that carry a raw TimeRange will be placed into one foldable,
Quantifiers that carry structured data (see our meeting example from
above) will end up in another foldable. To generate meaningful Reports
we need to coerce the carried data.

We coerce carried data simply by applying _fmap (fmap $ foldl coerce)_
to each aformentioned foldable. Return type of _coerce_, clearly,
matches the type value of the first type variable (d) of *Report* that
is getting generated.

After foldables are coerced we can merge those into one big sequence and
finally generate Report going from _f (t d)_ to an arbitrary report type
_r_, where _f_ is the “big” foldable containing the Quantifiers from a
single universe, _t_ is the point (???!!!) in this universe and _d_ is
coerced data.

---

### Footnotes

[1]:

Work on `conj` function can be taken a step further, describing a subset
of sequence-like Alternative types with the following rules:

```
class (Foldable f, Alternative f) => Sequence f where
  injection ∷ (\x → fold (pure x) = x)
  iso ∷ (\k xs ys → foldMap k xs <> foldMap k ys = foldMap k (xs <|> ys))
```

This way we can write the following code, forcing ourselves and other
users to denote explicitly structures that should follow sequence laws.

```
class (Foldable f, Alternative f) => Sequence f where
  conj ∷ a → f a → f a
```

(???!!!):

We're not sure that it makes sense to include `conj` machinery. In fact,
we have a hunch that we're solving the same problem of building up the
state of the universe twice — once by having Monoid instance and
calling _mappend_ when new Quantifier with the matching foldable inside
arrived, and once when we're building Sequences out of Quantifiers that
share a universe and underlying Foldables. Instead we should consider
using a Semigroup here, populating universes with simple (<>) and
reworking process of Report construction.
