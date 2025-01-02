# Vest: Verified Binary Parsers in Verus

Analyzing anything verified begins with one key question: What is verified?
In other words, what is the actual *trusted base* of your verified system?

In `Vest`, the exact thing that is verified is the round-trip property of 
parsers and serializers. That is, given bytes `b`, `b = (serialize . parse) b`,
and given structured data `m`, `m = (parse . serialize) m`.

All binary parsers and their corresponding serializers built by `Vest` enjoys 
round-trip. But what does that mean? Here's what it could mean: The user 
writes a pair of parser and serializer using `Vest` combinators
and then invokes some `Vest` proof function to see if round-trip works.
If not, maybe the user gets to see counterexamples.

`Vest` supports this. But, ideally, verification stays in the verification
repo as much as possible. If you write a verified crypto library, you expect the user
to be able to just use your verified cryptography. So, the user should not have to 
import `Verus` to invoke some `Vest` proof function.

Instead, in the more idiomatic workflow, you write a `Vest` protocol specification, 
and `Vest` will generate from it the round-tripped parser and serializers! 
This DSL (domain specific language) -> Generation approach is employed by many formal systems.

So let's explore how `Vest` internally verifies that for any correctly specified DSL,
the parser and serializer generated from it enjoys round-trip.

(TODO: Mention other parser properties like totality and completeness. TODO: Do those
pertain to Vest?)

## 