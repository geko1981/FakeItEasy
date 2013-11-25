Sometimes you want a call to be ignored. That can be configured like so:
```csharp
A.CallTo(()=>aFake.SomeMethodThatShouldDoNothing())
 .DoesNothing();
```

This is quite close to what a default Fake's unconfigured method will do, but there a few situations where you may need to make the `DoesNothing` call explicitly.

If the [[Fake is strict|Strict Fakes]], an unconfigured call will throw an exception, so `DoesNothing` can be used to allow an exception.

Or, `DoesNothing` can be used to change the behaviour that an already-configured call is supposed to have. For example, if a call is [[set to throw an exception|Throwing Exceptions]], that can be overridden. For more on this kind kind of thing, see how to use [[call repetition limits to change Fakes' behavior|Limited-Call-Specifications#changing-behavior-between-calls]].