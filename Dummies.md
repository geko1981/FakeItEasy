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

1. see if there's a user-supplied custom Dummy creation method for `T` (more on this below)
1. if `T` is `Task`, the returned Dummy will be an actual `Task` that completes immediately<sup>1</sup>
1. if `T` is `Task<TResult>`, the returned Dummy will be an actual `Task<TResult>` that completes immediately<sup>1</sup>. Its `Result` property returns a Dummy
1. starting in version 2.0, if `T` is a `Lazy<TValue>`, the returned Dummy will be an actual `Task<TValue>` whose `Value` is a Dummy of type `TValue`
1. if `T` is [[fakeable|What can be faked]], the Dummy will be a Fake `T`
1. if `T` is a value type, the Dummy will be a `T` created via `Activator.CreateInstance`
1. if nothing above matched, then `T` is a class. Loop over all its constructors in _descending order of argument list length_.  
For each constructor, attempt to get Dummies to satisfy the argument list. If the Dummies can be found, use `Activator.CreateInstance` to create the Dummy, supplying the Dummies argument list. If the argument list can't be satisfied, then try the next constructor.

If none of these strategies yield a viable Dummy, then FakeItEasy can't make a Dummy of type `T`.

## Custom Dummy creation in FakeItEasy 1.x

If FakeItEasy's default dummy creation behavior isn't adequate, you can provide your own. Here's an example:

```csharp
class DummyBookDefinition : DummyDefinition<Book>
{
    protected override Book CreateDummy()
    {
        return new Book { Title = "Some Book", PublishedOn = new DateTime(2000, 1, 1) };
    }
}
```

### How it works

FakeItEasy uses classes that implement the following interface to create Dummies:

```csharp
public interface IDummyDefinition
{
    Type ForType { get; }
    object CreateDummy();
}
```

When FakeItEasy tries to create a Dummy, it looks at all known `IDummyDefinition` implementations and if one of them has a `ForType` that matches the desired type, `CreateDummy` is used.

Although it's possible to implement `IDummyDefinition` explicitly, the preferred approach is to extend `abstract class DummyDefinition<T>: IDummyDefinition`, where `T` is the type of dummy to produce, as in the example above. `DummyDefinition<T>` implements `ForType` (returning `T`), so all that's needed is to implement the abstract `CreateDummy` method, having it return the Dummy.

### How does FakeItEasy find the Dummy Definitions?

On initialization, FakeItEasy [[looks for Discoverable Extension Points|Scanning for Extension Points]], including Dummy Definitions.

## Custom Dummy creation in FakeItEasy 2.x

If FakeItEasy's default dummy creation behavior isn't adequate, you can provide your own. Here's an example:

```csharp
class DummyBookFactory : DummyFactory<Book>
{
    protected override Book Create()
    {
        return new Book { Title = "Some Book", PublishedOn = new DateTime(2000, 1, 1) };
    }
}
```

### How it works

FakeItEasy uses classes that implement the following interface to create Dummies:

```csharp
public interface IDummyFactory
{
    bool CanCreate(Type type);
    object Create(Type type);
    int Priority { get; }
}
```

When FakeItEasy tries to create a Dummy, it looks at all known `IDummyFactory` implementations for which `CanCreate` returns `true`. If multiple impementations match, the one with the highest `Priority`is used.

If all that's needed is a Dummy Factory that creates a single, explicit type, extending `abstract class DummyFactory<T>: IDummyFactory` is preferred. It provides default implementations of `Priority` and `CanCreate` (although they can be overridden if needed). If you want to provide Dummies for a variety of types, you may prefer to extend `IDummyFactory` directly. For example, if you wanted all Dummy `IEnumerable<T>`s to be `SortedSet<T>`s, you might write something like this:

```csharp
class DummyEnumerableFactory: IDummyFactory
{
    public bool CanCreate(Type type)
    {
        if (type.IsGenericType)
        {
            var enumerableContentType = type.GetGenericArguments()[0];
            var enumerableTypeDefinition = typeof (IEnumerable<>).MakeGenericType(enumerableContentType);
            return enumerableTypeDefinition.IsAssignableFrom(type);
        }
        return false;
    }

    public object Create(Type type)
    {
        var enumerableContentType = type.GetGenericArguments()[0];
        var enumerableType = typeof (SortedSet<>).MakeGenericType(enumerableContentType);
        return enumerableType.GetConstructor(new Type[0]).Invoke(null);
    }

    public int Priority
    {
        get { return 0; }
    }
}
```

### How does FakeItEasy find the Dummy Factories?

On initialization, FakeItEasy [[looks for Discoverable Extension Points|Scanning for Extension Points]], including Dummy Factories.

----
1. In FakeItEasy 1.12 or earlier, the `Task` returned from a non-configured fake method would never be completed and (for example) an `await` would never be satisfied. If you are using 1.12 or earlier, [upgrade now](https://nuget.org/packages/FakeItEasy/).