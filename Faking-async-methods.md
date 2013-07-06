The faking of `async` methods is fully supported in FakeItEasy.

```C#
public class Foo
{
    public virtual async Task<string> Bar()
    {
        // await something...
    }
}
```

A call to an non-configured method on a fake will return a `Task` or `Task<T>` which completes immediately*, the latter supplying the default value of `T`:

```C#
var foo = A.Fake<Foo>();
var bar = await foo.Bar(); // will return immediately and return null (default(string))
```

Of course, you can still configure calls to `async` method as you would normally:

```C#
A.CallTo(() => foo.Bar()).Returns(Task.FromResult("bar));
```

\* In FakeItEasy 1.12 or earlier, the task would return uncompleted and as a result the `await` would never be satisified. If you are using 1.12 or earlier, [upgrade now](https://nuget.org/packages/FakeItEasy/).
