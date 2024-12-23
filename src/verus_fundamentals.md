# Verus Fundamentals

Verus is a tool for verifying the correctness of code written in Rust. Developers write specifications of what their code should do, and Verus statically checks that the executable Rust code will always satisfy the specifications for *all* possible executions of the code. Rather than adding run-time checks, Verus instead relies on powerful solvers to prove the code is correct.

Even if you do not plan to verify Rust, studying Verus
can help you understand the pitfalls of SMT solvers.
Further, I think the future of formal methods will
have more tools like Verus. You do everything in your 
production code's language and you do a seamless lift + spec + prove
when needed, in a formal language that looks familiar. 

Note, the seamless lifting dream is not yet achieved: Verus only supports a [subset](https://verus-lang.github.io/verus/guide/features.html) of Rust. (TODO: Are we just lacking engineering, or are there theorectical limitations?)

## Executable vs. Specification
Here is a common `Verus` workflow for verifying cryptography: 
1. Wrap production crypto code in verus block, 
2. Write a `Verus` spec for each of the components,
3. Write a `Verus` proof for the equivalence between 
the production crypto functions and the `Verus` spec
for them.

But this begs the question: What's so correct about the `Verus`
specs that we wish to verify our production code on them?
Doesn't `Verus` extend `Rust` syntax? And so how does it 
write any differently? 

(TODO: Write spec for `salsa20` to help answer this better)

Spec, proof, exec

int vs usize

Seq, Set, Map 


## Three(?) ways to deal with Recursion

### Induction


## Limitations: How to Fail Something True

The core reason for verification failures is undecidability: There is 
no algorithm that can prove general formulas true. For example, proving
theorems with quantifiers is generally undecidable. We rely on Z3's
pattern-based instantiations of quantifiers ("triggers") to use and prove
such formulas.
### Matching loop


## Strategies and Process of Proof Engineering
assert + assume

## Verified Binary Search


## Evaluation: What was unnatural?
As this is a tour through formal methods, we should critically evaluate our tool.
What felt unnatural? What did you thought was automatable and inferrable but is not?
What felt unergonomic? Perform this evaluation with total creativity: Don't 
reject your criticisms just because they can't be implemented
theory-wise. 

I now present my list. 

First, why can't everything just be automated?



Assert + assume is unergonomic.

Recursion and induction felt unergonomic.