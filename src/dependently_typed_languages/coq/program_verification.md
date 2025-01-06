# Program Verification

We finished `Imp.v` knowing:
1. We can write fixpoints (total functions) for `aeval` and `beval`,
2. However, `ceval` can not be a fixpoint, since 
there is no way of totally unfolding a while expression.
3. The solution is to write `ceval` as a relation (not even a function).
In Coq, this means to make `ceval`'s result a `Prop`, which is allowed
to not terminate.
