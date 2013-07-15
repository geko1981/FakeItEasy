FakeItEasy tries to provide helpful error messages when an [[Assertion]] isn't met. For example, when an expected call to a fake method isn't made, or when an unexpected call _is_ made. Often these messages are adequate, but sometimes there's a need to improve upon them.

## Out of the box experience
Suppose we have a `Book` class, whose instances we keep in some class that implements `ILibrary`:
```C#
public class Book
{
    public string Title { get; set; }
    public DateTime PublishedOn { get; set; }
}

public interface ILibrary
{
    bool Checkout(Book book);
}
```

Let's write a test that shows what happens when a method call that we didn't want to occur does:
```C#
public void Checkout_ShouldNotBeCalled()
{
    var fakeLibrary = A.Fake<ILibrary>();

    fakeLibrary.Checkout(new Book
                         {
                           Title = "The Ocean at the End of the Lane",
                           PublishedOn = new DateTime(2013, 6, 18),
                         });

    A.CallTo(() => fakeLibrary.Checkout(A<Book>._)).MustNotHaveHappened();
}
```

Here's what FakeItEasy says:
<pre>
Assertion failed for the following call:
  SampleTests.ILibrary.Checkout(<Ignored>)
Expected to find it never but found it #1 times among the calls:
  1: SampleTests.ILibrary.Checkout(<b>book: SampleTests.Book</b>)
</pre>
The message gives a good amount of information. (There's also a stack trace, omitted because it doesn't add anything to this discussion.) The message says that the `ILibrary.Checkout` method was never supposed to be called, but it was called once. Unfortunately, if there were multiple books involved in the test, this message would not indicate which one was used in the call. That's because by default, FakeItEasy just uses an argument's `ToString` method to format the message.

## Writing a custom formatter
However, it's possible to improve on the error message that FakeItEasy emits. Just define a class that implements `FakeItEasy.IArgumentValueFormatter` and include it in your test project, another assembly in the AppDomain, or even an assembly in the test project's working directory. FakeItEasy will find the class and use it when it formats the error message.  
`IArgumentValueFormatter` has this signature:
```C#
public interface IArgumentValueFormatter
{
    Type ForType { get; }
    int Priority { get; }
    string GetArgumentValueAsString(object argumentValue);
}
```
`GetArgumentValueAsString` does the work, transforming an argument into its formatted representation.   `ForType` indicates what type of argument a formatter can format.  
`Priority` is discussed below.

It's possible write an implementation of `IArgumentValueFormatter` by hand, but the recommended (and easiest) way to write a formatter is to extend the abstract class `FakeItEasy.ArgumentValueFormatter`. Here's a sample implementation:
```C#
class BookArgumentValueFormatter : ArgumentValueFormatter<Book>
{
    protected override string GetStringValue(Book argumentValue)
    {
        return string.Format("'{0}' published on {1:yyyy-MM-dd}",
            argumentValue.Title, argumentValue.PublishedOn);
    }
}
```

The `GetArgumentValueAsString` method is implemented by `ArgumentValueFormatter` and returns the result of `GetStringValue((Book) argumentValue)`. `ForType` is likewise provided by the base and returns `Book`. `Priority` returns `int.MinValue`, but may be overridden.

When this formatter is included in the solution, the error message becomes
<pre>
Assertion failed for the following call:
  SampleTests.ILibrary.Checkout(<Ignored>)
Expected to find it never but found it #1 times among the calls:
  1: SampleTests.ILibrary.Checkout(<b>book: 'The Ocean at the End of the Lane', published on 2013-06-18</b>)
</pre>
which could make tracking down the reason for the failure a little easier.

In this way, it's possible to create formatters for any type, including concrete types, abstract types, and interfaces. Formatters defined for base types and interfaces will be used when formatting values whose types extend or implement the formatter's type.

## FakeItEasy's default formatter behaviour
Earlier we said that FakeItEasy uses `ToString` to format an argument value. That's a bit of a simplification. The default behaviour does different things, depending on the actual value:

- the `null` value is formatted as `<null>`,
- the empty `string` is formatted as `string.Empty`,
- other `string` values are formatted as `"the string value"`, including the quotation marks, and
- any other value is formatted as its `ToString()` result

There is no way to change FakeItEasy's behaviour when formatting `null`, but the other behaviour can be overridden using user-defined.

## Resolving formatter collisions
It's possible for a solution to contain multiple formatters that would apply to the same types of arguments. In fact, it's guaranteed to happen, since FakeItEasy itself defines a formatter that applies to `object`s and one that applies to `string`s. Any user-defined formatter will conflict with at least the built-in object formatter, and maybe others. When there is more than one candidate for formatting an argument, FakeItEasy picks the best one based on two factors:

- the distance between the argument's type (hereafter _ArgType_) and the type each formatter knows about (hereafter _ForType_), and
- the value of each formatter's `Priority` property

### Lowest distance
When an argument value needs to be formatted, FakeItEasy examines all known formatters whose ForType is in ArgType's inheritance tree, or whose ForType is an interface that ArgType implements. The _distance_ between ForType and ArgType is calculated as follows:

- 0 if the ForType and ArgType are the same **or** if ForType is an interface that ArgType implements
- 1 if `ForType == ArgType.BaseType`, 
- 2 if `ForType == ArgType.BaseType.BaseType`, and so on, adding one for every step in the inheritance chain

The the formatter whose ForType has the smallest distance to ArgType is used to format the argument.

### Highest priority
Sometimes more than one formatter is found the same distance from ArgType. Maybe two formatters actually specify the same `ForType` property value, or there's a formatter defined for ArgType as well as for an interface that ArgType implements.

When multiple formatters have the same distance from the argument, FakeItEasy will select the one with the highest `Priority` property value. If multiple formatters have the same distance _and_ the same priority, the behavior is undefined.

The formatters that FakeItEasy includes have `Priority` equal to `int.MinValue`, as do all classes that extend `ArgumentValueFormatter<T>`, unless they explicitly override the property. So, for example, a user-provided alternate formatter for `string`s should override `Priority`, having it return a higher value. Otherwise, there's no guarantee which formatter will be used.