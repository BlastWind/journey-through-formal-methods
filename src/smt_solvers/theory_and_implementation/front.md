# SMT Theory and Implementation

## SAT

SAT is a NP-complete problem.

Input to SAT solver is a propositional formula in CNF (conjunctive normal form).
The CNF is a conjunction of one or more clauses, where a clause is a disjunction
of literals. In other words: One outer, multi-arged AND of many ORs.

For example, here's the CNF for the constraints on a single sudoku cell:

```clojure
(and (or x1 x2 x3 ...)
     (or (not x1) (not x2))
     (or (not x1) (not x3))
     (or (not x1) (not x4))
	 ...
     (or (not x8) (not x9))

)
```

The cell needs to be one of the values between `[1..9]`, and it can not be 
simultaenously more than one value.

Looking at the sudoku 
## Blob
asdf

## Interfacing Solvers with SAT


### Keywords
(TODO: Some of these should go into a SAT fundamentals page)

Atomic formula: An atomic formula is the smallest predicate grounded in a theory, they do not compose with logical connectives.
For example, an atomic formula in difference arithmetic is $t -s \le c$. This whole formula corresponds to a single
boolean variable in SAT.

Literal: Either an atomic formula or a negated atomic formula.

Theory Solver: A procedure that SMT solvers use for deciding the satisfiability of conjunctions of literals.

CNF (Conjunctive Normal Form)

Decide, Propagate, Backtrack

Theory Lemma

### Example Theory Solver: Difference Arithmetic
In difference arithmetic, literals are of form $t -s \le c$ where $t, s` are variables and `c` a constant.

Difference arithmetic is a subset of linear arithmetic 
that can be sped up due to their unique structure.

More precisely, we can search for negative cycles in a graph
where $t - s \le c$ corresponds to an edge from node $s$ to $t$ with
weight $c$.

By finding negative cycles, 

Solving (TODO: what?) in difference arithmetic 


### Resources
- [Satisfiability Modulo Theories: Introduction and Applications](https://dl.acm.org/doi/10.1145/1995376.1995394)