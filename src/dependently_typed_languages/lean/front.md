# Lean

## Dependent Types
Leans thinks of dependent types as types that depend on parameters (not necessarily values).
For example, in

```lean
inductive Vector (α : Type u) : Nat → Type u where
  | nil  : Vector α 0
  | cons : (a : α) → {n : Nat} → Vector α n → Vector α (n + 1)
```
, `Vector α n` depends on two parameters: the type of the elements in the vector (a type) and the length of the vector (a value).

The Lean books conceptualizes dependent products as dependent functions and adopts
the "indexed family of types" view of dependent products.
Dependent functions can be seen as `(a: α) -> β a`. `β` is a family of types over `α`, that is, there is a type `β a` for each value `a: α`. 

Indexed family

This is fundamentally different from type constructors, which have form `(α: Type) -> β α`. With type constructors, values aren't mentioned.
So "indexed family" is something 

 given `α: Type` and `β: α -> Type`, 


For example, consider
```lean
def cons (α : Type) (a : α) (as : List α) : List α :=
  List.cons a as
```




think of β as a family of types over α, that is, a type β a