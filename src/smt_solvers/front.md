# SMT_Solvers


Unfiltered notes below:

## 1. A History of Satisfiability
The history of satisfiability is best understood along its logic roots.

In logic, syntax and semantics are distinct.

Syntax is associated with proof theory, syntactic derivation, axioms
with a specified grammatical forms, inference rules. 

Semantics deals with model theory, satisfiability with respect to worlds.

In logic, there are two key terms: validity and consistency. They are interdefinable.
if we make use of $\neg$:
the argument from $ p_1,\dots,p_n $ to $ q $ is valid if and only if the
set $ \set{p_1, \dots, p_n, \neg q} $ is inconsistent. 

In proof theory, validity is called derivability. Consistency is just called consistency. 

Derivability: $ q $ is derivable 
from $ p_1, \dots, p_n $ (in symbols, $ p_1, \dots, p_n \vdash q $)
if and only if you can syntactically prove you using inference rules.there is a proof in the axiom system.

In model theory, validity is just called validity. Consistency is called satisfiability. $ A \vDash p $ means that $ p $
is true in the structure $ A $. 

Satisfiability, or semantic consistency: $ \set{p_1, \dots, p_n} $ is satisfiable
if and only if for some $ A, A \vDash p_1, \dots, A \vDash p_n $.


An axiom system is 


## Resources
Handbook of satisfiability