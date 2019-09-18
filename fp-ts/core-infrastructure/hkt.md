# Core Infrastructure - fp-ts/lib/HKT

The `HKT` module is linked closely with all others in fp-ts.

## Background

The module references a [paper](https://www.cl.cam.ac.uk/~jdy22/papers/lightweight-higher-kinded-polymorphism.pdf) describing a subject called "lightweight higher-kinded polymorphism". This paper is extremely academic and dense, and focuses on OCaml as opposed to a practical language (note: when I say "practical" I mean a language that I understand and use on a daily basis; I'm sure that OCaml has been used for practical purposes by many *distinguished academics*) like TypeScript.

While I don't understand the method suggested by the paper, I understand the purpose. Pure functional programming languages have the concept of "**kinds**", which are used to mirror the idea of function order to the concept of types (function : order :: type : kind). Just as a higher-order function is a function that operates on or returns other functions, a higher-kinded type is a type that "operates on" or "returns" other types.

In pure functional languages like Haskell, you can define generic types just like you can in TypeScript, and these types can take an arbitrary number of parameters, just like TypeScript:

```haskell
data Map k v = ...
```

```ts
type Map<K, V> = ...
```

However, there is a subtle difference. Just like Haskell functions are automatically curried (they can be partially applied right out of the box), parameterized types are also curried. What this means is that you can "partially apply" a type:

```haskell
type MapOfString = Map String -- MapOfString has one type parameter now: v
```

Haskell has a notation for this using asterisks (`*`) and arrows (`->`). For example, the "kind" of `Map` would be `* -> * -> *` because two type arguments need to be "applied" before you have a concrete type.

TypeScript can support this kind of thing, but it does not come out of the box, and it is impossible for it to work the same way as Haskell. TypeScript has support for two kinds of types:
* types with no parameters (`*`): `number`, `Object`, `undefined`
* types with parameters (`* -> *`): `T[]`, `Set<T>`, `Map<K, V>`

Even though TypeScript types can take more than one parameter, the language makes no special distinction between types that have different numbers of parameters. We can think of the list of type parameters as a single tuple type. For example, with `Map<K, V>`, the parameter of the kind can be thought of as `(K, V)`.

To produce `MapOfString` like Haskell does, TypeScript requires the creation of a new type:

```ts
type MapOfString<V> = Map<string, V>
```

This annoyingly requires duplication of the second parameter, but it works as advertised.

The problem here is that TypeScript doesn't support these "higher-kinded types" like Haskell does. Haskell can abstract over type kinds and say "this function takes values whose type has at least one parameter" as an abstraction over types of a particular kind. TypeScript doesn't support this abstraction natively, so fp-ts needed a way to define functions that restrict types based on kind. Lightweight higher-kinded polymorphism provides this.

## HKT

The `HKT` module provides four "classes" of types:
* "HKT" types: types that abstract over types of a particular kind
* "URItoKind" types: types that act as dictionaries for types to register themselves as types of a particular kind
* "URIS" types: key types of corresponding "URItoKind" types
* "Kind" types: types that "look up" types of a particular kind using their URI

There are four of each that go together, one for each kind supported by fp-ts:
* `* -> *` types: `HKT<URI, A>`, `URItoKind<A>`, `URIS`, `Kind<URI extends URIS, A>`
* `* -> * -> *` types: `HKT2<URI, E, A>`, `URItoKind2<E, A>`, `URIS2`, `Kind2<URI extends URIS2, E, A>`
* `* -> * -> * -> *` types: `HKT3<URI, R, E, A>`, `URItoKind3<R, E, A>`, `URIS3`, `Kind3<URI extends URIS3, R, E, A>`
* `* -> * -> * -> * -> *` types: `HKT4<URI, S, R, E, A>`, `URItoKind4<S, R, E, A>`, `URIS4`, `Kind4<URI extends URIS4, S, R, E, A>`

fp-ts stops after 4-kinded types because it becomes extremely unlikely for practical types to have that many parameters by that point. Most fp-ts types are `HKT` or `HKT2`, a few are `HKT3`, and only one is `HKT4`. If a user needs a higher kind, it is clear enough how to implement it (but an extra implementation of each type class would need to be added for the new kind, which would likely be extremely unweildly).
