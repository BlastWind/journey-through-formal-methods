# Propositions As Types

Curry noticed that every proof in propositional logic corresponded (bidirectionally) to a function (term) in the (simply) typed lambda calculus. For example, consider

```
λx:a->b. λy:a. x y
```
, since this is proper function (term) in the simply typed lambda calculus, it witnesses the truth of some statement in propositional logic, intuitively, we know this is
```
(a->b) -> a -> b
```

A false statement in propositional logic will correspond to a badly-typed lambda expression. No typed lambdas should be able to witness something false!

What happens if we upgrade and complicate the propositional logic? Consider the predicate logic, which is propositional logic with quantiifers.

Well, since propositional logic is practically 1-to-1 with the simply typed lambda calculus, we will need new constructs to handle the quantifiers. Howard and
De Bruijn introduced the dependent product and dependent pairs for the universal
and existential quantiifer, respectively.

```
∀x:Nat. ∃y:Nat. x < y

-- corresponds to

Π(x: Nat) → Σ(y : Nat) × (x < y)
```

Here is a crucial question: Logic is more intuitive than the type of some lambda term.
So are there situations where it would serve us better if we think in terms of type theory rather than logic?

Yes. Type theory is constructive by nature, logic needs not be.

In `Coq`, this means one can extract and compute with `Type` (constructive type theory) but not with `Prop` (logical propositions). 
For example, given a proof of `A \/ B`, which is defined as an inductive prop, you do not have access to whether an `A` or `B` was constructed to witness `A \/ B`. 
To obtain the witness, we have to use `orb`, which is the `Type` way of doing things.

As a further example, if you wish to obtain a witness, you must use a dependent pair; if you do not, a regular existential proposition is fine.
```coq
Definition foo' (B: Type) P := exists b: B, P b.
Definition bar' B P (H: foo' B P) : B.
  unfold fooable in H.
  destruct H. (* Error here *)

Definition foo'' P := {b: B | P b}.
Definition bar'' B P (H: foo'' B P) : B.
  unfold fooable in H.
  destruct H. (* Just fine *)
  exact x.
Qed.

(* Or, we could just stay in prop land, the only diff here is B: Prop)
Definition foo' (B: Prop) P := exists b: B, P b.
Definition bar' B P (H: foo' B P) : B.
  unfold fooable in H.
  destruct H. (* Error here *)
```


## Resources
https://en.wikipedia.org/wiki/Dependent_type