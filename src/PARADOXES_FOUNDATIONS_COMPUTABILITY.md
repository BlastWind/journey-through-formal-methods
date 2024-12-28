
# Paradoxes, Foundations, and Computability

1901. Russell's paradox. This is a paradox expressable (encodable) in naive set theory and formal logic.

Addressing his own paradox, Russell built his theory of types, which appeared in his and Whitehead's *Principia Mathematica*. This is the first type theory.

The theory of types presents a hierarchy of types, which is still seen in coq's universes.

But we can't just abondon set theory and start using theory of types (TODO: Why)! So, the Zermelo-Fraenkel set theory with the axiom of choice (ZFC) is also developed in response to the foundational crisis.

Simultaenously, the axiomatic method became a de facto standard: the proof of a theorem must result from
explicit axioms and previously proven theorems.

1920. Hilbert's program. 

As a solution to the foundational crisis, Hilbert sought to ground all existing theories to 
a finite, complete set of axioms, and provide a proof that these axioms were consistent. Ultimately,
the consistency of all of mathematics could be reduced to basic arithmetic.

In other words, Hilbert sought for a foundation that was:
1. Complete: All true math statements can be proven in the foundation,
2. Consistent: A proof that no contradiction can be obtained in the foundation,
3. Decidability: There should be an algorithm for deciding the truth or falsity
of any math statement.

(TODO: Godel Incompleteness, then Turing and Church takes defining computability and algorithms)

****
### Uncompiled