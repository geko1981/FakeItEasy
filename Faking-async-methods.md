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

A call to a non-configured method on a fake will return a `Task` or `Task<T>` which completes immediately<sup>1</sup>, the latter supplying a [[Dummy|Dummies#how-are-the-dummies-made]] of type `T`:

```C#
var foo = A.Fake<Foo>();
var bar = await foo.Bar(); // will return immediately and return string.Empty
```

Of course, you can still configure calls to `async` methods as you would normally:

```C#
A.CallTo(() => foo.Bar()).Returns(Task.FromResult("bar"));
```

<sup>1</sup> In FakeItEasy 1.12 or earlier, the `Task` returned from a non-configured fake method would never be completed and the `await` would never be satisfied. If you are using 1.12 or earlier, [upgrade now](https://nuget.org/packages/FakeItEasy/).