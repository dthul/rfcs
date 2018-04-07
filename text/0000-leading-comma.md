- Feature Name: `leading_comma`
- Start Date: 2018-04-<TODO>
- RFC PR: (leave this empty)
- Rust Issue: (leave this empty)

# Summary
[summary]: #summary

Permit leading commas everywhere trailing commas are permitted.

An example:

```rust
let array = [
    , 1
    , 2
    , 3
];
```

# Motivation
[motivation]: #motivation

This RFC is purely about style and accomodating the tastes and formatting habbis
of more people.

## What this RFC is **not** for

This RFC does not suggest that everyone should adopt leading commas as their
new one-true-style. In particular, this RFC **does _absolutely_ not** change
the current `rustfmt` style.

This RFC does also not proscribe how one should style leading commas either
when used and leaves up to people or a style RFC.

## It is in line with Rust's philosophy to have a tolerant grammar

[RFC 1925]: https://github.com/rust-lang/rfcs/pull/1925

In particular, [RFC 1925] was accepted to allow a leading `|` in `match` and
then it was said that:

> We tend to support a flexible grammar when possible, including things we don't consider idiomatic at all. Philosophically, this is an RFC we are inclined to accept, and there'd probably have to be a pretty strong, practical reason for us not to accept it.

\- **@withoutboats**

## We accept leading semicolons

In other words, the following is a legal Rust program:

```rust
fn foo() {}

fn main() {
    ; foo()
    ; foo()
}
```

This style of formatting is probably quite uncommon though.

## Leading commas read like bullet points

To answer the question *"Why would anyone want leading commas?"*

Consider the following type:

```rust
struct Foo {
    , alpha: A
    , beta: B
    , gamma: G
}
```

and compare that to:

> + alpha: Alpha conversion is the process of renaming variables.
> + beta: Beta reduction is a fancy term for reducing function application to a value.
> + gamma: An uppercase gamma (`Г`) is often the typing environment.

A similarity here is that `,` acts like the spine of a list as in the bullet
point list above.

The commas can also be, *for some people*,
more visually clear compared to commas at the end.
To some, this is however a drawback as they would see the list spine as noise.

## Clean diffs

Just as with trailing commas on every "bullet",
leading commas also lead to clean diffs.

## Easier multi-cursor support

In many editors such as Sublime Text it is relatively easy to add a comma to
the start of every line. In the editior mentioned, you only need to press
[`<cmd>` + `shift` + `L`] + `home` (modulo the `<cmd>` key) to do that.

[`<cmd>` + `shift` + `L`]: https://stackoverflow.com/questions/22099813/sublime-text-2-multiple-line-edit
[`shunt`]: https://marketplace.visualstudio.com/items?itemName=mycelo.shunt

In VSCode (with the [`shunt`] extension), you can do the same with
`ctrl` + `shift` + `O`.

It is often equally easy to multi-select select the end of every line in a
selection, so this is an argument both in favor of leading and trailing commas
(the latter of which we already support).

# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

In places where trailing commas are permitted,
leading commas are now also permitted.

Let's go through a few examples of such places.
Note that some of these uses can look quite strange
but are included for consistency and generality.
We also illustrate some different possible styles even tho they may not be optimal
for leading commas.

## Arrays and `vec![..]`

```rust
let arr = [
    , 1
    , 2
    , 3
];

let vec = vec![
    , "Alan Turing"
    , "Joan Clarke"
    , "Hugh Alexander"
];
```

## Structs

As seen above in the [motivation], we can use leading commas in `struct`s:

```rust
struct Thing {
    , velocity: (f64, f64)
    , color: Color
    , autonomous: bool
}
```

and the associated pattern:

```rust
let Thing {
    , velocity
    , color
    , autonomous
} = foo;
```

## Unions

```rust
union Foo {
    , as_i32: i32
    , as_u32: u32
}
```

## Enums

```rust
enum Foo<T> {
    , Recv(
        , usize
        , T
      )
    , Send {
        , id: usize
        , msg: T
    }
}
```

## Match

and the associated patterns:

```rust
match foo {
    , Foo::Recv(
        , a
        , b
    ) => recv(a, b)
    , Foo::Send
        {
        , id
        , msg
        } => send(id, msg)
}
```

## Tuples

```rust
let (, b): (, usize) = (, 2);
// equivalent to:
let (b,): (usize,) = (2,);

let (, a, b, c): (, usize, usize, usize) =
    (
    , 42
    , 1337
    , 42
    );
// equivalent to:
let (a, b, c): (usize, usize, usize) = (1, 2, 3);
```

## Function arguments, universal quantification sites, and `where` clauses

```rust
fn foo<
    ,'a
    , Bar
    , Quux
   >(
    , bar: Bar,
    , quux: Quux
) -> Foo<
    , Bar
    , Quux
> where
    , T: Clone
    , U: Copy
{
}
```

## Turbofish

```rust
let x = alpha::<, Foo, Bar>();
```

## `#[derive]` and `#[attribute]` in general

```rust
#[derive(
    , Copy
    , Clone
)]
struct Foo(
    , u8
    , u8
);
```

# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

Anywhere in the grammar where a list of commas are accepted,
a leading comma is also accepted.

Note in particular that this means that the following is accepted:

```rust
struct Foo { , a: T,  b: T, }  // Both leading and trailing comma.
```

Since the change is technically trivial, a full grammar diff is not given.

This also applies to macros in the standard library.

# Drawbacks
[drawbacks]: #drawbacks

## Some people will not want to read this in the code of others

Simply put, the style introduced will not fit the taste buds of some rustaceans,
and they will not wish to see this style in code of other people.

To mitigate this concern, the `fmt` style guide will keep recommending
the current formatting.

## It complicates the grammar

It compilcates the grammar and therefore probably also `syn`.

## The benefits to macros are very tenuous at best

Given that trailing commas are already supported,
there does not seem to be an improvement to macros from allowing leading commas.

# Rationale and alternatives
[alternatives]: #alternatives

The only alternative is to not introduce leading commas.

The impact of that would be to not accomodate the punctuation style desires of
a minority of people.

# Prior art
[prior-art]: #prior-art

## JavaScript

JavaScript accepts leading commas, but it does not seem to be popular there.
In addition, we have that `[,x].length == 2` and not `== 1`.

## Haskell

[haskell_style]: https://github.com/tibbe/haskell-style-guide/blob/master/haskell-style.md

In Haskell, it is [quite common][haskell_style] to style things,
in particular lists, with a leading comma,
but Haskell does not actually accept leading commas:

```haskell
exceptions =
    [ InvalidStatusCode
    , MissingContentHeader
    , InternalServerError
    ]
```

however, @joshtriplett [said](https://internals.rust-lang.org/t/grammar-liberalizations/7170/12):

> I’ve heard multiple experienced Haskell programmers say
> “this is a thing we do because we can’t use trailing commas”.

but @ExpHP [replied](https://internals.rust-lang.org/t/grammar-liberalizations/7170/17):

> For me, that is certainly how my use of leading comma began.
> But to be entirely honest, if Haskell were to one day suddenly begin
> supporting both trailing and leading commas,
> I would start writing what @centril wrote.
> Leading punctuation makes structure more obvious, because it lines up.

## Other languages

In general, it seems uncommon for languages to support leading commas.

# Unresolved questions
[unresolved]: #unresolved-questions

+ Have any cases been missed / forgotten in the RFC?