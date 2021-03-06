Often when a fake object is created, the goal is to completely separate the production code being tested from the implementation of the type being faked. Sometimes it's not possible or desirable to do this, though. In these situations we may want some of the methods on a faked class act as if they were the original methods.

The `CallsBaseMethod` configuration method can be used to make a method execute the implementation of the faked class:

```csharp
A.CallTo(() => fakeShop.SellSmarties())
 .CallsBaseMethod();
```

Configuring a method to call its base method only makes sense if the method is actually implemented, so this technique cannot be used on an abstract class method or on any method from a faked interface—a faked abstract method told to call its base method will throw a `NotImplementedException`.

## Configuring all methods at once

Perhaps you want to have nearly of a fake's (fakeable) methods defer to the original implementation. Rather than using `CallsBaseMethod` a dozen times, the [[fake creation option|Creating Fakes#options]] `CallsBaseMethods` can do all the work at once:

```csharp
var fakeShop = A.Fake<CandyShop>(options => options.CallsBaseMethods());
```

And then [[selectively override some of them|Limited Call Specifications#changing-behavior-between-calls]]

```chsharp
A.CallTo(() => fakeShop.SellRockets()).Throws<Exception>();
```
