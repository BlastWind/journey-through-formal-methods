# Cryptol (For Haskell Knowers)

> The Cryptol language is a size-polymorphic dependently-typed programming language with support for polymorphic recursive functions. It has a small syntax tuned for applied cryptography, a lightweight module system, a Read–Eval– Print loop (REPL) top-level, and a rich set of built-in tools for performing high-assurance (literate) programming. Cryptol performs fairly advanced type inference, though as with most mainstream strongly typed functional languages, types can be manually specified as well. 

Cryptol is implemented in Haskell.

### Size-polymorphism

> In general, type predicates exclusively describe arithmetic constraints on type variables. Cryptol does not have a general-purpose dependent type system, but a size-polymorphic type system.
> There is a spectrum of type systems employed by programming languages, all the way from completely untyped to fancier dependently typed languages. There is no simple answer to the question, what type system is the best? It depends on the application domain. We have found that Cryptol’s size-polymorphic type system is a good fit for programming problems that arise in the domain of cryptography. The bit-precise type system makes sure that we never pass an argument that is 32 bits wide in a buffer that can only fit 16. The motto is: Well typed programs do not go wrong.
> In practical terms, this means that the type system catches most of the common mistakes that programmers tend to make. Size-polymorphism is a good fit for Cryptol, as it keeps track of the most important invariant in our application domain: making sure the sizes of data can be very precisely specified and the programs can be statically guaranteed to respect these sizes.

The following are types of selected Cryptol functions. You can use `:t` in the Cryptol REPL to find out for yourself.

```haskell
-- `:` is used instead of `::` in Cryptol, but I'll switch between these for whichever syntax highlights better
take :: {front, back, a} [front + back]a -> [front]a
drop :: {front, back, a} (fin front) => [front + back]a -> [back]a
split :: {parts, each, a} (fin each) => [parts * each]a -> [parts][each]a
groupBy :: {each, parts, a} (fin each) => [each * parts]a -> [parts][each]a
join :: {parts, each, a} (fin each) => [parts][each]a -> [parts * each]a
transpose :: {rows, cols, a} [rows][cols]a -> [cols][rows]a
head :: {n, a} [1 + n]a -> a
tail :: {n, a} [1 + n]a -> [n]a
```


Here's how we read these types. `[front + back]` can be thought of as a list
consisting of `front + back` number of elements. Though, we need to be careful with 
brackets in Cryptol. 

```haskell
0xabc : [12]
[1..12 : [Integer]] : [12]Integer
[1..12 : [4]] : [12][4]
[[(y,x) | x <- [1 .. 3 : Integer]] | y <- [1 .. 3 : Integer]] : [3][3](Integer, Integer)
```

When `[n]` stands alone, it describes the value to be an `n`-bit word. For example,
`0xabc` is a 12-bit word (since each hexadecimal requires 4-bits), we type `0xabc : [12]`.

However, when `[n]` precedes another type, it should be read as an `n`-lengthed list. 
For example,

```haskell
 -- 12-lengthed list of Integers
[1..12 : [Integer]] : [12]Integer

-- 12-lengthed list of 4-bit words
[1..12 : [4]] : [12][4]

-- 3-lengthed list of 3-lengthed list of (Integer, Integer)
[[(y,x) | x <- [1 .. 3 : Integer]] | y <- [1 .. 3 : Integer]] : [3][3](Integer, Integer)
```

With these in mind, let's read the type of `join` out loud:
```haskell
join :: {parts, each, a} (fin each) => [parts][each]a -> [parts * each]a
```
`join` takes in a 2D array of some `a`. This 2D array has row length of `parts` 
and column length of `each`. `parts` and `each` are inferrable length values.
`each` ought to be a finite length (I'm handwaving this).
`a` is some type.

`join` outputs a 1D array of some `a`. This 1D array has length `parts * each`.

Our takeaway from these paragraphs is that the semantic of brackets is overloaded in Cryptol. 
But, you may be wondering one more thing: how are concrete values (like `12`) allowed in types (like `[12], [12][4], [12]Integer)`)?
This is not something (typically) allowed and encodable in Haskell.
Also, how is computation (e.g., `each * parts` and `front + back`) allowed in types?


### Size-polymorphism: Implementation
The keyword is dependent types. Dependent types allow us to variate (depend) types on values.
If you have experiences with modern dependently typed 
languages you will find in (TODO: ref), you will see that this section
details how to emulate dependent type features in a more roundabout way in Haskell.

If you have no such experiences, this may or may not be the best intro.
The `-XDataKinds` extension that enables this isn't ergonomic, so
don't worry if you are having trouble interpreting stuff. This is not Haskell's fault,
Haskell is not made for dependent types. Just go to Idris (TODO: ref).

Here's how we encode a list that can depend on concrete length, in Haskell:

```haskell
{-# LANGUAGE DataKinds #-}

data Nat = Z | S Nat

-- List now uses the Nat' kind directly
data List (n :: Nat) a where
   Nil :: List 'Z a 
   Cons :: a -> List n a -> List ('S n) a

twoItems :: List ('S ('S 'Z)) String
twoItems = Cons "hi" (Cons "yo" Nil)
```

Usually, `data Nat = Z | S Nat` is read as "type constructor `Nat` 
introduces value constructors `Z` and `S`". But with `-XDataKinds` enabled,
we move into a new level — "kind constructor `Nat` introduces type constructors
`Z` and `S`". 

The type constructors `Z` and `S` are referrable as `'Z` and `'S`. And
since these are type constructors, declarations such as `List 'Z a` and
`List ('S n) a` are valid. And note, `n` is a type constructor whose 
type is the kind `Nat'`.

Note, our type constructors have no value constructors. This is fine, we
are in kind-type land and no longer in type-value land.

Finally, with `Nat` being defined inductively