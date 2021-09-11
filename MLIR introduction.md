# EBNF Notation Using in MLIR
Code that expresses the syntax of a formal language
```
alternation ::= expr0 | expr1 | expr2  // Either expr0 or expr1 or expr2.
sequence    ::= expr0 expr1 expr2      // Sequence of expr0 expr1 expr2.
repetition0 ::= expr*  // 0 or more occurrences.
repetition1 ::= expr+  // 1 or more occurrences.
optionality ::= expr?  // 0 or 1 occurrence.
grouping    ::= (expr) // Everything inside parens is grouped together.
literal     ::= `abcd` // Matches the literal `abcd`.
```
# Common syntax
```
digit     ::= [0-9]
hex_digit ::= [0-9a-fA-F]
letter    ::= [a-zA-Z]
id-punct  ::= [$._-]

integer-literal ::= decimal-literal | hexadecimal-literal
decimal-literal ::= digit+
hexadecimal-literal ::= `0x` hex_digit+
float-literal ::= [-+]?[0-9]+[.][0-9]*([eE][-+]?[0-9]+)?
string-literal  ::= `"` [^"\n\f\v\r]* `"`   TODO: define escaping rules
```
# Identifiers and keywords
```
// Identifiers
bare-id ::= (letter|[_]) (letter|digit|[_$.])*
bare-id-list ::= bare-id (`,` bare-id)*
value-id ::= `%` suffix-id
suffix-id ::= (digit+ | ((letter|id-punct) (letter|id-punct|digit)*))

symbol-ref-id ::= `@` (suffix-id | string-literal)
value-id-list ::= value-id (`,` value-id)*

// Uses of value, e.g. in an operand list to an operation.
value-use ::= value-id
value-use-list ::= value-use (`,` value-use)*
```
Identifiers name entities such as values, types and functions, and are chosen by the writer of MLIR code. Identifiers may be descriptive (e.g. %batch_size, @matmul), or may be non-descriptive when they are auto-generated (e.g. %23, @func42). Identifier names for values may be used in an MLIR text file but are not persisted as part of the IR - the printer will give them anonymous names like %42.

MLIR guarantees identifiers never collide with keywords by prefixing identifiers with a sigil (e.g. %, #, @, ^, !). In certain unambiguous contexts (e.g. affine expressions), identifiers are not prefixed, for brevity. New keywords may be added to future versions of MLIR without danger of collision with existing identifiers.

Value identifiers are only in scope for the (nested) region in which they are defined and cannot be accessed or referenced outside of that region. Argument identifiers in mapping functions are in scope for the mapping body. Particular operations may further limit which identifiers are in scope in their regions. For instance, the scope of values in a region with SSA control flow semantics is constrained according to the standard definition of SSA dominance . Another example is the IsolatedFromAbove trait , which restricts directly accessing values defined in containing regions.

Function identifiers and mapping identifiers are associated with Symbols and have scoping rules dependent on symbol attributes.
# Operations
```
operation            ::= op-result-list? (generic-operation | custom-operation)
                         trailing-location?
generic-operation    ::= string-literal `(` value-use-list? `)`  successor-list?
                         region-list? dictionary-attribute? `:` function-type
custom-operation     ::= bare-id custom-operation-format
op-result-list       ::= op-result (`,` op-result)* `=`
op-result            ::= value-id (`:` integer-literal)
successor-list       ::= `[` successor (`,` successor)* `]`
successor            ::= caret-id (`:` bb-arg-list)?
region-list          ::= `(` region (`,` region)* `)`
dictionary-attribute ::= `{` (attribute-entry (`,` attribute-entry)*)? `}`
trailing-location    ::= (`loc` `(` location `)`)?
```
（1）No predefined set of instructions<br>
（2）Operations are like “opaque functions” to MLIR<br>
%res:2 = "mydialect.morph"(%input#3) { some.attribute = true, other_attribute = 1.5 }: (!mydialect<"custom_type">) -> (!mydialect<"other_type">, !mydialect<"other_type">) loc(callsite("foo" at "mysource.cc":10:8))
| $res:2      | 第一列     | 第二列     |
| ---------- | :-----------:  | :-----------: |
| 第一行     | 第一列     | 第二列     |

# Blocks
list of operations. In SSACFG regions , each block represents a compiler basic block where instructions inside the block are executed in order and terminator operations implement control flow branches between basic blocks.
```
block           ::= block-label operation+
block-label     ::= block-id block-arg-list? `:`
block-id        ::= caret-id
caret-id        ::= `^` suffix-id
value-id-and-type ::= value-id `:` type

// Non-empty list of names and types.
value-id-and-type-list ::= value-id-and-type (`,` value-id-and-type)*

block-arg-list ::= `(` value-id-and-type-list? `)`
```
The “block argument” representation eliminates a number of special cases from the IR compared to traditional “PHI nodes are operations” SSA IRs (like LLVM). For example, the parallel copy semantics of SSA is immediately apparent, and function arguments are no longer a special case: they become arguments to the entry block [ more rationale ]. Blocks are also a fundamental concept that cannot be represented by operations because values defined in an operation cannot be accessed outside the operation.
# Region
A region is an ordered list of MLIR Blocks .
```
region ::= `{` block* `}`
```
