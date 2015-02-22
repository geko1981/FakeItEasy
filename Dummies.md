A Dummy is an object that FakeItEasy can provide when an object of a certain type is required, but the actual behavior of the object is not important.

## How to use them in your tests
Consider this example. Say that you want to test the following class:

```csharp
public class Library
{
    public bool Checkout(Card patronCard, Book someBook);
}
```

Maybe in one of your tests you want to invoke `Checkout` with an expired library card. The checkout should fail, regardless of the book being checked out&mdash;only the status of the card matters. Instead of writing

```csharp
library.Checkout(MakeExpiredCard(),
                 new Book { Title = "The Ocean at the End of the Lane" } );
```

You can write:

```csharp
library.Checkout(MakeExpiredCard(), A.Dummy<Book>());
```

This signals that the actual value of the `Book` is really not important. The code is intention-revealing.

## How FakeItEasy uses them

When [[Creating Fakes]] or Dummies of class types, FakeItEasy needs to invoke the classes' constructors. If the constructors take arguments, FakeItEasy needs to generate appropriate argument values. It uses Dummies.

## How are the Dummies made?

When FakeItEasy needs to access a Dummy of type `T`, it tries a number of approaches in turn, until one succeeds:

1. see if there's a user-supplied [[custom Dummy creation]] mechanism for `T` (more on this below)
1. if `T` is `Task`, the returned Dummy will be an actual `Task` that completes immediately<sup>1</sup>
1. if `T` is `Task<TResult>`, the returned Dummy will be an actual `Task<TResult>` that completes immediately<sup>1</sup>. Its `Result` property returns a Dummy
1. starting in version 2.0, if `T` is a `Lazy<TValue>`, the returned Dummy will be an actual `Task<TValue>` whose `Value` is a Dummy of type `TValue`
1. if `T` is [[fakeable|What can be faked]], the Dummy will be a Fake `T`
1. if `T` is a value type, the Dummy will be a `T` created via `Activator.CreateInstance`
1. if nothing above matched, then `T` is a class. Loop over all its constructors in _descending order of argument list length_.  
For each constructor, attempt to get Dummies to satisfy the argument list. If the Dummies can be found, use `Activator.CreateInstance` to create the Dummy, supplying the Dummies argument list. If the argument list can't be satisfied, then try the next constructor.

If none of these strategies yield a viable Dummy, then FakeItEasy can't make a Dummy of type `T`.

----
1. In FakeItEasy 1.12 or earlier, the `Task` returned from a non-configured fake method would never be completed and (for example) an `await` would never be satisfied. If you are using 1.12 or earlier, [upgrade now](https://nuget.org/packages/FakeItEasy/).