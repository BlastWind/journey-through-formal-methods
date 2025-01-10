# Program Verification

We finished `Imp.v` knowing:
1. We can write fixpoints (total functions) for `aeval` and `beval`,
2. However, `ceval` can not be a fixpoint, since 
there is no way of totally unfolding a while expression.
3. The solution is to write `ceval` as a relation (not even a function).
In Coq, this means to make `ceval`'s result a `Prop`, which is allowed
to not terminate.

`Prop`s can be thought of in many ways:
1. In the lens of decidability, `Prop` is a vaguer `Bool`: If the `Prop`
at hand happens to be decidable, then it acts just like a `Bool`. However,
`Prop` is also allowed to be undecidable,
2. In the lens of constructability, `Prop` throws away its witnesses.
Consequently, given `Definition fooable B P := exists b : B, P b.`, a `Prop`,
we won't be able to `destruct` on it and obtain the `b` used. For this scenario,
we would need a dependent pair, which is a `Type`: `Definition fooable P := {b : B | P b}.`
3. In the lens of extractability. `Prop`s get erased during Coq extraction.

Since `ceval` is not a fixpoint, the property that two cprograms are the same
becomes not so obvious. We need this: Two commands are behaviorally equivalent
if, for any starting state, they either both diverge and they both terminate in the
same final state.

```coq
Definition cequiv (c1 c2 : com) : Prop :=
  forall (st st' : state),
    (st =[ c1 ]=> st') <-> (st =[ c2 ]=> st').
```

Implications capture the divergence possibility. Since, a divergent program `c` 
has the precise property of
```coq
forall (st st' : state),
  ~ (st =[ c ]=> st')å
```

We can now prove behavioral equivalences between different programs. Here is one such interesting 
equivalence:
```coq
Theorem loop_unrolling : forall b c,
  cequiv
    <{ while b do c end }>
    <{ if b then c ; while b do c end else skip end }>.
```

Now, `cequiv` is a mathematical equivalence (it is straightforward to show reflexivity, 
symmetry, and transtivity). However, it is not so obvious that behavioral equivalence
is a congruence.

But congruence is desirable, since this tells us that the equivalences of two subprograms implies
the equivalence of the larger programs in which they are embedded. For example,
```
              cequiv c1 c1'
              cequiv c2 c2'
         --------------------------
         cequiv (c1;c2) (c1';c2')
```
This showcases just one embedding: The embedding of smaller programs into a larger sequence program.

There are five constructors that builds larger programs out of smaller ones: `E_Seq`, `E_WhileTrue`, `E_WhileFalse`,
`E_IfTrue` and `E_IfFalse`. We need to show congruence for each of these separately.