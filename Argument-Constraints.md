When configuring and [[asserting|Assertion]] calls in FakeItEasy, the arguments of the call can be constrained so that only calls to the configured method where the arguments matches the constraint are selected.

## Matching values exactly

Assume the following interface exists:
```csharp
public interface IFoo
{
    void Bar(string s, int i);
}
```

Then the arguments to Bar can be constrained used to limit call matching:
```csharp
var foo = A.Fake<IFoo>();

A.CallTo(() => foo.Bar("hello", 17).MustHaveHappened();
```

Then FakeItEasy will look _only_ for calls made with the arguments `"hello"` and `17` - no other calls will match the rule.

When checking for argument equality, FakeItEasy uses `object.Equals`. If the type to be checked does not provide an adequate `Equals` method, you may have to use the `That.Matches` method described in [Custom Matching](Argument Constraints#custom-matching).

## Ignoring arguments
Suppose the value of the integer in the `Bar` call wasn't important, but the string was. Then the following constraint could be used:
```csharp
A.CallTo(() => foo.Bar("hello", A<int>.Ignored)).MustHaveHappened();
```

Then any call will match, so long as the string value was `"hello"`. The `Ignored` property can be used on any type.

An underscore (`_`) can be used as a shorthand for `Ignored` as well:
```csharp
A.CallTo(() => foo.Bar("hello", A<int>._)).MustHaveHappened();
```

# Other matchers
If more complicated constraints are needed, the `That` method can be used. There are a few built-in matchers:

|Matcher|Tests for|
|:------|:--------|
|IsNull()|`null`|
|IsEqualTo(other)|object equality using `object.Equals`|
|IsSameAs(other)|object identity - like `object.ReferenceEquals`|
|IsInstanceOf(type)|an argument that can be assigned to a variable of type `T`|
|Contains(string)|substring match|
|StartsWith(string)|substring at beginning of string|
|EndsWith(string)|substring at end of string|
|IsNullOrEmpty()|`null` or `""`|
|IsEmpty()|empty enumerable|
|Contains(item)|item's presence in an enumerable|
|IsSameSequenceAs(enumerable)|sequence equality, like `System.Linq.Enumerable.SequenceEqual`|
|Not|inverts the sense of the matcher|

# Custom matching

If none of the canned matchers are sufficient, you can provide a predicate to perform custom matching using `That.Matches`. Like in this rather contrived example:

```csharp
A<string>.That.Matches(s => s.Length == 3 && s[1] == 'X');
``` 

FakeItEasy will evaluate the predicate against any supplied argument. The predicate can be supplied as an `Expression<Func<T, bool>>` or as a `Func<T, bool>`. FakeItEasy can generate a description of the matcher when an `Expression` is supplied (although you may supply your own as well), but you must supply a description when using a `Func`.

For another example of using `That.Matches`, see Jonathan Channon's [Comparing object instances with FakeItEasy](http://blog.jonathanchannon.com/2013/09/11/comparing-object-instances-with-fakeiteasy).

# Out parameters

The incoming argument value of out parameters is ignored when matching calls. The incoming value of an out parameter can't be seen by the method body anyhow, so there's no value in constraining by it.<sup>1</sup>

For example, this test passes:

```csharp
string configurationValue = "lollipop";
A.CallTo(()=>aFakeDictionary.TryGetValue(theKey, out configurationValue))
 .Returns(true); 

string fetchedValue = "licorice";
var success = aFakeDictionary.TryGetValue(theKey, out fetchedValue);

Assert.That(success, Is.True);
```

See [[Implicitly Assigning out Parameter Values|Assigning-out-and-ref-parameters#implicitly-assigning-out-parameter-values]] to learn how the initial `configurationValue` is used in this case.

----
1. This is new behaviour as of 1.23.0. Previously, the argument value would have to be equal to the value supplied in `A.CallTo` in order for the call to match.