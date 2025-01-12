# SMT_Solvers


Unfiltered notes below:

## 1. A History of Satisfiability

### 1.1 Encountering the definition of Satisfiability
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
if and only if you can syntactically transform using inference rules of the system.

In model theory, validity is just called validity. Consistency is called satisfiability. $ A \vDash p $ means that $p$
is true in the structure $ A $. 

Satisfiability: $ \set{p_1, \dots, p_n} $ is satisfiable if and only if for some $ A, A \vDash p_1, \dots, A \vDash p_n $.


The two branches of logic are systematically related. Most languages have syntactic 
and semantic concepts that coincide exactly ($\vdash$ corresponds to $\vDash$) — such an axiom system is said to be complete.

### 1.2 The ancients
Aristotle invented syllogistic or categorical logic. The building block of the logic is categorical propositions, which
is a subject-predicate sentence appearing in one of four forms: every $S$ is $P$, no $S$
is $P$, some $S$ is $P$, and some $S$ is not $P$.


<!-- Since Aristotle viewed language as expressing ideas that signified entities and the properties they instantiate in 
the "world", these *categorical* propositions are "evaluated" in some world $w$,
so this is the model theory and semantical part of the logic. -->
A syllogism has the syntax $(p, q) \to r$ where $p, q, r$ are all categorical propositions.
$p$ is the major premise, $q$ is the minor premise, and $r$ is the conclusion. 

To show the validity of a syllogism, we need to be able to derive $r$ from $(p, q)$, using the following rules:

$$
\begin{align*}
\text{Axiom 1:} & & (\text{every }X\text{ is }Y \land \text{every }Y\text{ is }Z) & \to \text{every }X\text{ is }Z \\
\text{Axiom 2:} & & (\text{every }X\text{ is }Y \land \text{no }Y\text{ is }Z) & \to \text{no }X\text{ is }Z \\
\end{align*}
$$

and

$$
\begin{align*}

\text{Rule 1:} & \text{ From } & (p \land q) \to r         & \text{ infer } & (\lnot r \land q) \to \lnot p \\
\text{Rule 2:} & \text{ From } & (p \land q) \to r         & \text{ infer } & (q \land p) \to r \\
\text{Rule 3:} & \text{ From } & \text{no }X\text{ is }Y    & \text{ infer } & \text{no }Y\text{ is }X \\
\text{Rule 4:} & \text{ From } & (p \land q) \to \text{ no }X\text{ is }Y & \text{ infer } & (p \land q) \to \text{ some }X\text{ is not }Y
\end{align*}
$$

The axioms are valid syllogisms. The inferenced rules derive one syllogism from another. A prototypical example of a derivation follows:

1. All men are mortal
2. Socrates is a man
3. Socrates is mortal

First, let's convert these to categorical propositions:
1. Every man (Y) is mortal (Z)
2. Every thing identical to Socrates (X) is men (Y)
3. Every thing identical to Socrates (X) is mortal (Z)

The categorical expressions are less natural, but this is what it takes to lift singular terms into categorical proposition form.
After conversion, our starting form almost matches Axiom 1 already. We have 
$$(\text{every }Y\text{ is }Z \land \text{every }X\text{ is }Y) \to \text{every }X\text{ is }Z$$
, applying Rule 2, we get

$$(\text{every }X\text{ is }Y \land \text{every }Y\text{ is }Z) \to \text{every }X\text{ is }Z$$

and hence our syllogism is valid by Axiom 2.


The syllogistic logic is intimately related with natural language — categorical propositions 
deal with subjects and predicates by default. This logic is less abstract than first-order logic
but aligns with our natural reasoning a bit more.

**More interestingly, we may use set theory to provide the semantics of syllogistic logic.
Then, due to the fact that syllogisms always come in 3 categorical propositions, we can use Venn diagrams to prove things (see [this](https://phil240.tamu.edu/LectureNotes/6.3.pdf))!**
Previously, we proved validity (derivability in proof theory) using axioms and inference rules, all of which were syntactic.
However, with set theory and Venn diagrams, the semantic validity is visual!

Usually, we don't get such nice visual semantics, but with syllogisms coming in precisely 3 terms, we can always use Venn diagrams.
In general, proving is done with inference rules (syntactic), as they are mechanized and efficient. The underlying, semantic model of things
contains the spirit of the logic. They give meaning to our logic and guide us in designing syntactic rules. Such semantics could be set-theorectical (as for
syllogistic logic), category theorectic (used in type theory), and operational and denotational (used in programming language).



## Resources
Handbook of satisfiability

[Syllogism and Venn Diagrams](https://phil240.tamu.edu/LectureNotes/6.3.pdf)