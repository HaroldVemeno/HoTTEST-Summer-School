# Week 02 - Agda Exercises

## Please read before starting the exercises

**The exercises are designed to increase in difficulty so that we can cater to
our large and diverse audience. This also means that it is *perfectly fine* if
you don't manage to do all exercises: some of them are definitely a bit hard for
beginners and there are likely too many exercises! You *may* wish to come back
to them later when you have learned more.**

Having said that, here we go!

This is a markdown file with Agda code, which means that it displays nicely on
GitHub, but at the same time you can load this file in Agda and fill the holes
to solve exercises.

**Please make a copy of this file to work in, so that it doesn't get overwritten
  (in case we update the exercises through `git`)!**

```agda
{-# OPTIONS --without-K --allow-unsolved-metas #-}

module 02-My-Exercises where

open import prelude
open import decidability
open import sums
```

## Part I: Propositions as types


### Exercise 1 (★)

Prove
```agda
uncurry : {A B X : Type} → (A → B → X) → (A × B → X)
uncurry f (a , b) = f a b

curry : {A B X : Type} → (A × B → X) → (A → B → X)
curry f a b = f (a , b)
```
You might know these functions from programming e.g. in Haskell.
But what do they say under the propositions-as-types interpretation?


### Exercise 2 (★)

Consider the following goals:
```agda
[i] : {A B C : Type} → (A × B) ∔ C → (A ∔ C) × (B ∔ C)
[i] (inl (a , b)) = inl a , inl b
[i] (inr c) = inr c , inr c

[ii] : {A B C : Type} → (A ∔ B) × C → (A × C) ∔ (B × C)
[ii] (inl a , c) = inl (a , c)
[ii] (inr b , c) = inr (b , c)

[iii] : {A B : Type} → ¬ (A ∔ B) → ¬ A × ¬ B
pr₁ ([iii] ¬ab) a = ¬ab (inl a)
pr₂ ([iii] ¬ab) b = ¬ab (inr b)

[iv] : {A B : Type} → ¬ (A × B) → ¬ A ∔ ¬ B
[iv] x = {!!} --impossible

[v] : {A B : Type} → (A → B) → ¬ B → ¬ A
[v] f ¬b a  = ¬b (f a )

[vi] : {A B : Type} → (¬ A → ¬ B) → B → A
[vi] = {!!} -- impossible

[vii] : {A B : Type} → ((A → B) → A) → A
[vii] = {!!} -- pierce is impossible

[viii] : {A : Type} {B : A → Type}
    → ¬ (Σ a ꞉ A , B a) → (a : A) → ¬ B a
[viii] naba a nba = naba (a , nba)

[ix] : {A : Type} {B : A → Type}
    → ¬ ((a : A) → B a) → (Σ a ꞉ A , ¬ B a)
pr₁ ([ix] x) = {!!}
pr₂ ([ix] x) = {!!} -- no

[x] : {A B : Type} {C : A → B → Type}
      → ((a : A) → (Σ b ꞉ B , C a b))
      → Σ f ꞉ (A → B) , ((a : A) → C a (f a))
pr₁ ([x] f) a = pr₁ (f a)
pr₂ ([x] f) a = pr₂ (f a)
```
For each goal determine whether it is provable or not.
If it is, fill it. If not, explain why it shouldn't be possible.
Propositions-as-types might help.


### Exercise 3 (★★)

```agda
¬¬_ : Type → Type
¬¬ A = ¬ (¬ A)

¬¬¬ : Type → Type
¬¬¬ A = ¬ (¬¬ A)
```
In the lecture we have discussed that we can't  prove `∀ {A : Type} → ¬¬ A → A`.
What you can prove however, is
```agda
tne : ∀ {A : Type} → ¬¬¬ A → ¬ A
tne ¬¬¬a a = ¬¬¬a λ ¬a -> ¬a a
```


### Exercise 4 (★★★)
Prove
```agda
¬¬-functor : {A B : Type} → (A → B) → ¬¬ A → ¬¬ B
¬¬-functor f ¬¬a ¬b = ¬¬a λ a -> ¬b (f a)

¬¬-kleisli : {A B : Type} → (A → ¬¬ B) → ¬¬ A → ¬¬ B
¬¬-kleisli f ¬¬a = tne (¬¬-functor f ¬¬a)
```
Hint: For the second goal use `tne` from the previous exercise

## Part II: `_≡_` for `Bool`

**In this exercise we want to investigate what equality of booleans looks like.
In particular we want to show that for `true false : Bool` we have `true ≢ false`.**

### Exercise 1 (★)

Under the propositions-as-types paradigm, an inhabited type corresponds
to a true proposition while an uninhabited type corresponds to a false proposition.
With this in mind construct a family
```agda
bool-as-type : Bool → Type
bool-as-type true = 𝟙
bool-as-type false = 𝟘
```
such that `bool-as-type true` corresponds to "true" and
`bool-as-type false` corresponds to "false". (Hint:
we have seen canonical types corresponding true and false in the lectures)


### Exercise 2 (★★)

Prove
```agda
bool-≡-char₁ : ∀ (b b' : Bool) → b ≡ b' → (bool-as-type b ⇔ bool-as-type b')
bool-≡-char₁ b .b (refl .b) = id , id
```


### Exercise 3 (★★)

Using ex. 2, concldude that
```agda
true≢false : ¬ (true ≡ false)
true≢false x = pr₁ (bool-≡-char₁ _ _ x ) ⋆

true≢false' : ¬ (true ≡ false)
true≢false' ()
```
You can actually prove this much easier! How?


### Exercise 4 (★★★)

Finish our characterisation of `_≡_` by proving
```agda
bool-≡-char₂ : ∀ (b b' : Bool) → (bool-as-type b ⇔ bool-as-type b') → b ≡ b'
bool-≡-char₂ true true x = refl true
bool-≡-char₂ true false x = 𝟘-elim (pr₁ x ⋆)
bool-≡-char₂ false true x = 𝟘-elim (pr₂ x ⋆)
bool-≡-char₂ false false x = refl false
```


## Part III (🌶)
A type `A` is called *discrete* if it has decidable equality.
Consider the following predicate on types:
```agda
has-bool-dec-fct : Type → Type
has-bool-dec-fct A = Σ f ꞉ (A → A → Bool) , (∀ x y → x ≡ y ⇔ (f x y) ≡ true)
```
Prove that
```agda
decidable-equality-char : (A : Type) → has-decidable-equality A ⇔ has-bool-dec-fct A
pr₁ (pr₁ (decidable-equality-char A) dec) a b with dec a b
... | inl _ = true
... | inr _ = false
pr₁ (pr₂ (pr₁ (decidable-equality-char A) dec) a b) a≡b with dec a b
... | inl _ = refl true
... | inr a≢b = 𝟘-elim (a≢b a≡b)
pr₂ (pr₂ (pr₁ (decidable-equality-char A) dec) a b) fab≡true with dec a b
... | inl a≡b = a≡b
pr₂ (decidable-equality-char A) (f , p) a b with p a b
... | x , y with f a b
... | true = inl (y (refl true))
... | false = inr λ a≡b -> true≢false (sym (x a≡b))

```