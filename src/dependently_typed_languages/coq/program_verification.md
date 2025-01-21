# Program Verification

We finished `Imp.v` knowing:
1. We can write fixpoints (total functions) for `aeval` and `beval`,
2. However, `ceval` can not be a fixpoint, since 
there is no way of totally unfolding a while expression.
3. The solution is to write `ceval` as a relation.
In Coq, we construct `ceval` as an inductive proposition: `ceval`'s result is a `Prop`, which is allowed
to never terminate.
4. Although `ceval` is defined as a relation, it is in fact a partial function. This is captured in the following theorem:
```coq
Theorem ceval_deterministic: forall c st st1 st2,
     st =[ c ]=> st1  ->
     st =[ c ]=> st2 ->
     st1 = st2.
```

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
(call it `cequiv`) becomes less obvious: Two commands are behaviorally equivalent
if, for any starting state, they either both diverge and they both terminate in the
same final state.

```coq
Definition cequiv (c1 c2 : com) : Prop :=
  forall (st st' : state),
    (st =[ c1 ]=> st') <-> (st =[ c2 ]=> st').
```

Implications capture the scenario where both programs diverge. Since, a divergent program `c` 
has the precise property of
```coq
forall (st st' : state),
  ~ (st =[ c ]=> st')
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

Recall, congruence is an equivalence relation on a set $S$. But importantly, 
it must be constructed against some operation $*$ between elements of $S$.

Your equivalence relation $\sim$ is congruent if for all $s_1 \sim s_2$
and $t_1 \sim t_2$, you have $s_1 * t_1 \sim s_2 * t_2$. The `cequiv`
congruence (yet to be proven) is a relation on the set of all programs 
and $*$ are the compositional constructors.


Congruence is desirable, since this tells us that the equivalences of two subprograms implies
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

### Extension 1: Non-determinism

As of now, our language Imp has a deterministic, partial evaluation function.
The struggle thus far has been in wrestling with this partiality. Proving
program equivalence is not a simple `reflexivity` call. Instead,
we must use `ceval`'s constructors.

Let's complicate the picture even more: What if we added non-determinism 
to Imp? This is a realistic feature of programming languages. For example,

```c
x = 0;
f(++x, x);
```
This demonstrates an undefined behavior in C: The order in which function
arguments are evaluated is unspecified.
The compiler could choose to evaluate either of the arguments first
depending on which is more optimized on the target machine.
So, `f` could be invoked with either `f(1, 1)` or `f(1, 0)`.

Evidently, we can't model such nondeterminism in our language Imp yet.
To support general nondeterminism, we add a `HAVOC` keyword, such that after 
executing the program
```
HAVOC Y;
Z := Y * 2
```
the value of [Y] can be *any* number, while the value of [Z] is
twice that of [Y].

Question: How should we extend `Inductive ceval` to build a havoc
command?

Answer: We allow `x` to take on any value after `havoc x`. So
our definition uses a `forall n`:

```coq
| E_Havoc : forall st n x,
    st =[ havoc x ]=> (x !-> n ; st)
```

In Imp, some programs always terminate and some programs always diverge.
But in our new language which we call Himp, some programs can
nondeterministically terminate in some runs and diverge in others.

A simpler program demonstrating this spirit is `p5`:

```coq
Definition p5 : com :=
  <{ while X <> 1 do
       havoc X
     end }>.
```

(TODO: I don't think this is true...)
Behaviorally, `p5` actually always terminates since there is a 
nonzero probability of assigning `X := 1`. But, the "sometimes terminate and sometimes diverge" 
trait can be seen in each iteration of the loop.