- Feature Name: `type_questions`
- Start Date: 2018-03-04
- RFC PR: (leave this empty)
- Rust Issue: (leave this empty)

# Summary
[summary]: #summary

Introduces the special pseudo-type `?` which the compiler will understand as
a question from the user about the expected or inferred type of an expression
or binding. The result will be an error message at type checking where the type
information is shown. This is meant to facilitate type driven development.

This will allow you to write:

+ `expr : ?` for asking about the type of `expr` with type ascription,
+ `let x: ? = expr;` for asking about the type of `expr` with a let binding,
+ `iter.collect::<?>()` for asking what the compiler infers the type of the
  result to be. This is equivalent to `iter.collect() : ?` in this case.

The question (`?`) type can also be nested in type constructors as usual:

+ `let x: Vec<?> = expr;`

# Motivation
[motivation]: #motivation

Currently, there is no reliable way to ask the compiler what it thinks the
type of an expression should be or what it is. In many cases a user can get
away with using the unit type `()` as a stand in for "tell me the type" since
`()` is 

# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

# Drawbacks
[drawbacks]: #drawbacks

# Rationale and alternatives
[alternatives]: #alternatives

# Unresolved questions
[unresolved]: #unresolved-questions
