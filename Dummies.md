A Dummy is an object that FakeItEasy can provide when an object of a certain type is required, but the actual behavior of the object is not important.

## How to use them in your tests
Consider this example. Say that you want to test the following class:

```C#
public class Library
{
    public bool Checkout(Card patronCard, Book someBook);
}
```

Maybe in one of your tests you want to invoke `Checkout` with an expired library card. The checkout should fail, regardless of the book being checked out&mdash;only the status of the card matters. Instead of writing

```C#
library.Checkout(MakeExpiredCard(), new Book { Title = "The Ocean at the End of the Lane" } );
```

You can write:

```C#
library.Checkout(MakeExpiredCard(), A.Dummy<Book>());
```

This signals that the actual value of the `Book` is really not important. The code is intention-revealing.

## How FakeItEasy uses them

When [[Creating Fakes]] or Dummies of class types, FakeItEasy needs to invoke the classes' constructors. If the constructors take arguments, FakeItEasy needs to generate appropriate argument values. It uses Dummies.

## How are the Dummies made?

When FakeItEasy needs to access a Dummy of type `T`, it tries a number of approaches in turn, until one succeeds:

1. see if there's a custom Dummy definition for `T` (more on this below)
1. if `T` is `Task`, the returned Dummy will be an actual `Task`
1. if `T` is `Task<TResult>`, the returned Dummy will be an actual `Task<TResult>` whose `Result` property returns a Dummy
1. if `T` is fakeable, the Dummy will be a Fake `T`
1. if `T` is a value type, the Dummy will be a `T` created via `Activator.CreateInstance`
1. if nothing above matched, then `T` is a class. Loop over all its constructors in _descending order of argument list length_.  
For each constructor, attempt to get Dummies to satisfy the argument list. If the Dummies can be found, use `Activator.CreateInstance` to create the Dummy, supplying the Dummies argument list. If the argument list can't be satisfied, then try the next constructor.

If none of these strategies yield a viable Dummy, then FakeItEasy can't make a Dummy of type `T`.

## Custom Dummy creation

If FakeItEasy's default dummy creation behavior isn't adequate, you can provide your own. Here's an example:

```C#
class DummyBookDefinition : DummyDefinition<Book>
{
    protected override Book CreateDummy()
    {
        return new Book { Title = "Some Book", PublishedOn = new DateTime(2000, 1, 1) };
    }
}
```

### How it works
On startup, FakeItEasy searches its own assembly, assemblies in the current AppDomain, and assemblies in the process's current directory for classes that implement `FakeItEasy.IDummyDefinition`. Any such classes found will be used when creating Dummies. 

`IDummyDefinition` has this signature:

```C#
public interface IDummyDefinition
{
    Type ForType { get; }
    object CreateDummy();
}
```

When FakeItEasy tries to create a Dummy, it looks at all known `IDummyDefinition` implementations and if one of them has a `ForType` that matches the desired type, `CreateDummy` is used.

Although it's possible to implement `IDummyDefinition` explicitly, the preferred approach is to extend `abstract class DummyDefinition<T>: IDummyDefinition`, where `T` is the type of dummy to produce, as in the example above. `DummyDefinition<T>` implements `ForType` (returning `T`), so all that's needed is to implement the abstract `CreateDummy` method, having it return the Dummy.
