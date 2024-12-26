# Interfacing Solvers with SAT


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