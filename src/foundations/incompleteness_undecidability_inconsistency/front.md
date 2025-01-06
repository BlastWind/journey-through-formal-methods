# Incompleteness_Undecidability_Inconsistency


- Why must non-terminating (non-total) functions be rejected in Coq?
Here is an example showing what would go wrong if Coq allowed non-terminating recursive functions:
	
```coq
Fixpoint loop_false (n : nat) : False := loop_false n.
```
As a consistent logic, this is not allowed. How would one define the evaluation function for 
a small imperative language with a `while` construct in Coq, then? One way is to not define 
a function (which ought to be total) in the first place, but a relation. We define an
inductive proposition whose return type is `Prop`. The cost of this is that we now need
to construct a proofs for evaluations.