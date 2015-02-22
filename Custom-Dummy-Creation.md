version 2.x | [[version 1.x|Custom Dummy Creation in version 1.x]]

----

FakeItEasy has built-in [[Dummy|Dummies]] creation rules that provide usable non-null values to be used in tests. However, if test-writers require specific types of values to be made, they can provide custom rules for FakeItEasy to use.

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

