# How It Works: fp-ts

[fp-ts](https://github.com/gcanti/fp-ts) is a TypeScript library that provides common pure functional programming APIs in a TypeScript-idiomatic way (as much as possible).

JS, being a language that provides first-class functions, is well-suited to functional programming, and it is quite possible to write code in a purely functional way. However, while it was certainly designed to support functional programming, it was *definitely not* designed for pure functional programming. It is possible to mirror the constructs that are first-class-supported in languages designed for pure functional programming, but as JS was not designed for these things, the result typically ends up being quite verbose and, in my opinion, very unsatisfying to work with.

Having said all that, libraries like Ramda exist and are widely used, meaning that there are likely many people who are perfectly fine with the state of pure functional programming in JS.

TypeScript goes a long way toward making it easier to support pure functional programming in JS. Pure functional languages tend to be very dependent on their strong type systems to support many of their constructs, so having a strong type system makes it a bit more realistic. But under the surface, TypeScript is still just a superset of JS, and is severely limited by it. True "pure functional languages" like Haskell support proper dynamic dispatch for types that implement type classes. Since "members" of type classes are just standalone functions, you can call one of these functions on any type that implements the type class, and the strong type system knows which implementation to call. But in JS, this kind of dynamic dispatch requires actual member functions, so extra abstractions are required to emulate the behavior provided in pure functional languages.

But I digress. fp-ts is a quite popular library that seems to push TypeScript to its limits in order to implement pure functional features in a way that is as ergonomic as possible. Let's see how it holds up.

## What fp-ts Provides

fp-ts provides a wide variety of types and functions that implement pure functional constructs. Our friends Functor, Monad, IO, and many others are provided.

I've looked many times into these concepts and still haven't hit an "epiphany" about them, so let's see if we can explore that as well.

There seem to be four categories of modules provided by fp-ts:
* Data Types (akin to classes/structs)
    * Array
    * Const
    * Either
    * IO
    * IOEither
    * Identity
    * Map
    * NonEmptyArray
    * Option
    * Reader
    * ReaderEither
    * ReaderTaskEither
    * Record
    * State
    * StateReaderTaskEither
    * Store
    * Task
    * TaskEither
    * These
    * Traced
    * Tree
    * Tuple
    * Writer
* Type Classes (akin to interfaces)
    * Alt
    * Alternative
    * Applicative
    * Apply
    * Bifunctor
    * BooleanAlgebra
    * Bounded
    * BoundedDistributiveLattice
    * BoundedJoinSemilattice
    * BoundedLattice
    * BoundedMeetSemilattice
    * Category
    * Chain
    * ChainRec
    * Choice
    * Comonad
    * Compactable
    * Contravariant
    * DistributiveLattice
    * Eq (? - it provides a URI, which lines up with data types, but it is a type class)
    * Extend
    * Field
    * Filterable
    * FilterableWithIndex
    * Foldable
    * FoldableWithIndex
    * Functor
    * FunctorWithIndex
    * Group
    * HeytingAlgebra
    * Invariant
    * JoinSemilattice
    * Lattice
    * Magma
    * MeetSemilattice
    * Monad
    * MonadIO
    * MonadTask
    * MonadThrow
    * Monoid
    * Ord (? - see Eq)
    * Profunctor
    * Ring
    * Semigroup
    * Semigroupoid
    * Semiring
    * Show
    * Strong
    * Traversable
    * TraversableWithIndex
    * Unfoldable
    * Witherable
* Core Infrastructure
    * HKT
* Utility Functions/Types
    * Console
    * Date
    * IORef
    * Ordering
    * Random
    * Set (?)
    * function
    * pipeable
* Other? (things I don't understand)
    * EitherT
    * OptionT
    * ReaderT
    * StateT
    * ValidationT

