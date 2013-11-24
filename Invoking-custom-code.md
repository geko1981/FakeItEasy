Sometimes a faked method's desired behaviour can't be satisfactorily defined just by [[specifying return values]], [[throwing exceptions]], [[assigning out and ref parameters]] or even [[doing nothing]]. Maybe you need to simulate some kind of side effect, either for the benefit of the System Under Test or to make writing a test easier (or possible). Let's see what that's like.

```csharp
A.CallTo(() => fakeShop.SellSmarties())
 .Returns(20)
 .Invokes(() => OrderMoreSmarties()); // simulate Smarties stock falling too low
```

Now when the System Under Test calls `SellSmarties`, the fake will call `OrderMoreSmarties`.
If the method being configured has a return value, you should use `Return` to specify the return value - `Invokes` doesn't do that. Of course, if the method is `void`, there's no need.

There are also more advanced variants that can invoke actions based on arguemnts supplied to the faked method. These act similarly to how you specify return values that are calculated at call time. For example
```csharp
// pass up to 4 original call argument values into the method that creates the exception
A.CallTo(()=>fakeShop.NumberOfSweetsSoldOn(A<DateTime>._))
 .Returns(17)
 .Invokes((DateTime when) => System.Console.Out.WriteLine("showing sweet sales for " + when);

// pass an IFakeObjectCall into the creation method for more advanced scenarios
A.CallTo(() => fakeShop.NumberOfSweetsSoldOn(A<DateTime>._))
 .Returns(0)
 .Invokes(callObject => System.Console.Out.WriteLine(callObject.FakedObject +
                                                     " is closed on " +
                                                     callObject.Arguments[0]));
```