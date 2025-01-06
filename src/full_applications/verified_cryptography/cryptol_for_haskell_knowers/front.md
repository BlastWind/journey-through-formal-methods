# Cryptol (For Haskell Knowers)

> The Cryptol language is a size-polymorphic dependently-typed programming language with support for polymorphic recursive functions. It has a small syntax tuned for applied cryptography, a lightweight module system, a Read–Eval– Print loop (REPL) top-level, and a rich set of built-in tools for performing high-assurance (literate) programming. Cryptol performs fairly advanced type inference, though as with most mainstream strongly typed functional languages, types can be manually specified as well. 

Cryptol is implemented in Haskell.
Cryptol is very similar to Haskell: From syntax to type system and language features to even the REPL. The key unique feature of Cryptol is size polymorphism.

## Size-polymorphism
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
[[(y,x) | x <- [1..3 : Integer]] | y <- [1..3 : Integer]] : [3][3](Integer, Integer)
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

## Size-polymorphism: Implementation
To encode size-polymorphism in Haskell is to wonder how to generally encode dependent types in Haskell. Dependent types allow us to variate (depend) types on values.
If you have experiences with modern dependently typed 
languages such as those in [Chapter 4](./idris.md), this will be smooth sailing. Though, you will see that dependent types are not ergonomic in Haskell.

If you have Haskell experience but no type-level Haskell experience, this could be a good intro to the topic! Just note, Haskell is not made for dependent types. Try [Idris](./idris.md) if you want the full Haskell + dependent types experience.

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


## More on Arithmetic Types and Constraints
Cryptol interfaces with bit sequences and numbers through words and integers and operations
defined on them. Words have `n`-bit types (i.e., `[n]`); integers have type `Integer`.
Cryptol is unsigned when operating on words and signed when operating on integers. For example,

```haskell
Cryptol> (-1: Integer) <= 0
True
Cryptol> (-1: [1]) <= 0
False
Cryptol> -1 == 0xff
True -- because -1 is inferred as [8]
Cryptol> 0xff <= 0
False -- Of course not
```

Overflow/underflow happens, but it is expected because Cryptol uses modular arithmetic. The types suggest this —
```haskell
(+): {a} (Ring a) => a -> a -> a
(-): {a} (Ring a) => a -> a -> a
(*): {a} (Ring a) => a -> a -> a
(/.) : {a} (Field a) => a -> a -> a -- division
```
How is it suggested? Well, `(+)` deals only with the same `a`. If we apply `0xabc : [12]` to `(+)`, then
we must apply another `[12]` to get a final `[12]`. The only reasonable way for the final `[12]`
to be a proper sum of some sort is if additions wrap around (overflow).

As a side note, `Ring` and `Field` are algebraic structures. The set of integers form a `Ring` because 
we can define `(+)` and `(*)` according to a set of rules. However, the set of integers do not form 
a `Field`, which requires every nonzero element to have a multiplicative inverse. The prototypical
example of a `Field` is the set of integers modulo \\( m \\) with \\( m \\) prime.

The last piece worth mentioning is arithmetic type inferencing. I illustrate these with examples instead of describing
rules.
```haskell
Cryptol> 1 + 1
2
Cryptol> 1 + 1 : [_]
-- Using '1' for type wildcard (_)
0
Cryptol> 1 + 1 : [2]
1
Cryptol> [1: [_] ...]
-- Using '1' for type wildcard (_)
[1, 0, 1, 0, 1, ...]
Cryptol> [0: [_] ...]
-- Using '0' for type wildcard (_)
[0, 0, 0, 0, 0, ...]
```

## Other Concepts
### Corecursion
Cryptol recommends on using corecursion (also known as recurrence, unfolds, or anamorphisms) as 
opposed to explicit recursion.

Let's understand through an example. The task: Create
a stream that is seeded with four initial values and whose subsequent
elements are appended into the stream and are computed by
 xor-ing the current stream element (1st)
with two additional elements further into the stream (the 2nd and the 4th)?

With explicit recursion, this looks something like:
```haskell
stream = helper [0x3F, 0xE2, 0x65, 0xCA]
  where
    helper xs =
      let n = length xs
          a = xs ! (n-4) -- three generations ago
          b = xs ! (n-3) -- two generations ago
          c = xs ! (n-1) -- current element
          next = a `xor` b `xor` c
      in helper (xs ++ [next])
```
Be hold. Corecursion.
```haskell
stream = [0x3F, 0xE2, 0x65, 0xCA] # new
  where new = [ a ^ b ^ c | a <- stream 
                          | b <- drop 1 stream 
                          | c <- drop 3 stream]
```
Soak in the beauty for a bit, then, think carefully about how laziness enables this code. Beware of showing this to your imperative friends.
> Those who were seen dancing were thought to be insane by those who could not hear the music.

More generally,
```haskell
ys = [i] # [ f (x, y) | x <- xs
                      | y <- ys
]
```
where `xs` is some input sequence, `i` is the result corresponding to the empty sequence, and `f` is a transformer to compute the next element, using the previous result and the next input.




### Using Type in Variable Context

The following code is legal:

```haskell
length : {n, a, b} (fin n, Literal n b) => [n]a -> b
length _ = `n
```

The surprising thing is that we can take a type variable, `n`, and use it in 
the value definition. Dependent types (precisely size-polymorphism) allow this.

### Arithmetic Type Constraint
Continuing the discussion on size-polymorphism, the following is also legal:

```haskell
myWidth : {bits,len,elem} (fin len, fin bits, bits >= width len) => [len] elem -> [bits]
```
Look at the arithmetic constraint `bits >= width len`. TODO: A better example?

`myWidth` has two typeclass constraints, `fin len` and `fin bits`. It also has an 
value-wise arithmetic constraint `bits >= width len`.

At last, a quick discussion on other typeclasses:
- Logic: Includes operators `&&`, `||`, `^`, and `~`. Cryptol types made of bits are instances of `Logic`.
- Zero: Includes special constant `zero` and shifting operators `<<` and `>>`. All Cryptol built-in types
are instances of class `Zero` (`zero` is the bottom value).
- Cmp: Includes `<`, `>`, `<=`, `>=`, `min`, `max`. Requirement for `Eq`.
- SignedCmp: `<$`, `>$`, $<=$`, and $>=$`. These interpret bitvectors as signed 2's complement numbers.

<!-- TODO: I don't actually understand what's going on with decidability. 
Why do we want to decide?
Then, more general arithmetic predicates are allowed. As we saw earlier, we can write a function from `[parts][each]a -> [parts * each]a`. We can write programs with arbitrarily complicated predicates. Now, type inference in the presence of arithmetic predicates is an undecidable problem. For example, one can write `{k} (2 >= k, k > 5) => [k]`. No algorithm can decide this problem in general. To aid with this, Cryptol uses a powerful SMT solver capable of reasoning about complex arithmetic theories within these algorithms. -->


## Classic Ciphers