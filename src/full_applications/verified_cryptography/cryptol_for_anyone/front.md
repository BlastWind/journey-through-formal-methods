# Cryptol and SAW

Cryptol is very Haskell-like, from the syntax to the type system, and even to the REPL.
Since Cryptol is a crypto DSL, it accustoms to the low-level, providing types such as bit-width. Let's play with the REPL:

```bash
# prints with base=16 by default
Cryptol> 0b101
0x5
Cryptol> :t 0b101 # :t or :type
0xabc : [3]
Cryptol> :t 0xabc
0xabc : [12]
Cryptol> :t 100
100 : {a} (Literal 100 a) => a
Cryptol> 100 : [6]

[error] at <interactive>:31:1--31:4:
  • Unsolvable constraint:
      6 >= 7
        arising from
        use of literal or demoted expression
        at <interactive>:31:1--31:4
Cryptol> 100 : [7]
0x64
```

The `[n]` is a type that informs us the number of bits a word is represented with.
Numbers such as `0b101`, `0xabc`, and `100` are all words.
`0xabc : [12]` because each hexadecimal requires 4 bits to store. 
Prompting the REPL with the value `100 : [6]` (explicitly annotated type)
gives an unsolvable constraint because the decimal 100 can't be represented in 6 bits.

The only unexpected output is `:t 100`. What the hell is `100 : {a} (Literal 100 a) => a`?
Essentially, this is an uninferred type. The reason Cryptol cannot infer `100 : [7]` is
that Cryptol does not perform real arithmetic during type inference. Thus, since decimal 
digit require anywhere from 1 to 4 bits to represent, Cryptol does not give 100 a concrete type.
Conversely, the number of bits used to represented binaries and hexadecimals are evident at
the syntax level.




0101

1010