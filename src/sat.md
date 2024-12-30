# SAT

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