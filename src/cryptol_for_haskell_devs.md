# Cryptol (For Haskell Knowers)

> The Cryptol language is a size-polymorphic dependently-typed programming language with support for polymorphic recursive functions. It has a small syntax tuned for applied cryptography, a lightweight module system, a Read–Eval– Print loop (REPL) top-level, and a rich set of built-in tools for performing high-assurance (literate) programming. Cryptol performs fairly advanced type inference, though as with most mainstream strongly typed functional languages, types can be manually specified as well. 

Cryptol is implemented in Haskell.

### Size-polymorphism
The following are types of selected Cryptol functions. You can use `:t` in the Cryptol REPL to find out for yourself.

```haskell
take : {front, back, a} [front + back]a -> [front]a
drop : {front, back, a} (fin front) => [front + back]a -> [back]a
split : {parts, each, a} (fin each) => [parts * each]a -> [parts][each]a
groupBy : {each, parts, a} (fin each) => [each * parts]a -> [parts][each]a
join : {parts, each, a} (fin each) => [parts][each]a -> [parts * each]a
transpose : {rows, cols, a} [rows][cols]a -> [cols][rows]a
head : {n, a} [1 + n]a -> a
tail : {n, a} [1 + n]a -> [n]a
```


Here's how we read these types. `[front + back]a` can be thought of as a list (Cryptol calls it a sequence)
consisting of `front + back` number of `a`s. Note that because the number of elements is directly encoded
within the brackets, the item type we contain, `a`, comes after the brackets. There is one implicit case:
If you just see a standalone `[n]`, it means `[n]Bit`. For example,

```haskell
0xabc : [12]
[1..12 : [Integer]] : [12]Integer
[1..12 : [4]] : [12][4]
[[(y,x) | x <- [1 .. 3 : Integer]] | y <- [1 .. 3 : Integer]] : [3][3](Integer, Integer)
```
`0xabc` is a 12-bit word (since each hexadecimal requires 4-bits), we type `0xabc : [12]Bit`,
but as a shorthand, this is just `0xabc : [12]`.

With this in mind, let's read the type of `join` out loud:
```haskell
join : {parts, each, a} (fin each) => [parts][each]a -> [parts * each]a
```
`join` takes in a 2D array of some `a`. This 2D array has row length of `parts` 
and column length of `each`. `parts` and `each` are inferrable length values.
`(fin each)` is a typeclass constraint to enforce `each` to be of finite length (we can ignore the why).

`join` outputs a 1D array of the same `a`. This 1D array has length `parts * each`.

Compare this to the typical `join` type in Haskell,
```haskell
-- typical Haskell join
join :: [[a]] -> [a]
```
Cryptol's `join` is a lot more powerful than Haskell's `join`! Though, in the next section, I show you how one might get Cryptol's `join` in Haskell.

The power to Cryptol's `join` is that arithmetic constraints can be imposed at the type level.
Cryptol calls this "size-polymorphism". But more generally, when types can depend on values, as is the case here,
a language is "dependently typed". Cryptol offers limited dependent types, allowing types
to only depend on arithmetic values. This seems to be the sweet spot for a Cryptography DSL.

### Size-polymorphism: Implementation
To encode size-polymorphism in Haskell is to wonder how to generally encode dependent types in Haskell. Dependent types allow us to variate (depend) types on values.
If you have experiences with modern dependently typed 
languages you will find in (TODO: ref), this will be smooth sailing. Though, you will see that dependent types are not ergonomic in Haskell.

If you have Haskell experience but no type-level Haskell experience, this could be a good intro to the topic! Just note, Haskell is not made for dependent types. Try Idris if you want the full experience (TODO: ref).

Here's how we encode a list that can depend on concrete length, in Haskell:

```haskell
{-# LANGUAGE DataKinds #-}

data Nat = Z | S Nat

data List (n :: Nat) a where
   Nil :: List 'Z a 
   Cons :: a -> List n a -> List ('S n) a

twoItems :: List ('S ('S 'Z)) String
twoItems = Cons "hi" (Cons "yo" Nil)
```

Usually, `data Nat = Z | S Nat` is read as "type constructor `Nat` 
introduces value constructors `Z` and `S`". But with `-XDataKinds` enabled,
this will instead be interpreted as — "kind constructor `Nat` introduces type constructors `Z` and `S`". 

The type constructors `Z` and `S` are only referrable as `'Z` and `'S`. This is to help the compiler to distinct kind-type land from type-value land.

The key: Since `'Z` and `'S` are type constructors, and `n` is a type constructor whose type is the kind `Nat`, `List 'Z a` and
`List ('S n) a` are valid expressions. Hence, we can now type `twoItem :: List ('S ('S 'Z)) String` — a concrete length is in this type!


Note, our type constructors have no value constructors. This is good, we
are in kind-type land and not type-value land.

Finally, with `Nat` being defined inductively, (TODO: type family plus and mult and define join).
TODO: `Nat` is defined inductively to account for all natural numbers.

