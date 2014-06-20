Sometimes methods have `out` or `ref` parameters that need filling-in when called on a Fake. Use `AssignsOutAndRefParameters`:
```csharp
string theValue;
A.CallTo(()=>aFakeDictionary.TryGetValue(theKey, out theValue))
 .Returns(true) 
 .AssignsOutAndRefParameters(someCoolValue);
```

`AssignsOutAndRefParameters` takes a `params object[]`, with one element (in order) for each of the `out` and `ref` parameters in the call being faked - the other arguments to the method should be omitted.

While assigning out and ref parameters, the `Returns` method (or [[some variant|Specifying return values]]) should be used to specify the return value for the method - `AssignsOutAndRefParameters` does not do this on its own.

##Assigning Values Calculated at Call Time
When out or ref parameter values aren't known until the method is called, `AssignsOutAndRefParametersLazily` can be used.

```csharp
string theValue;
A.CallTo(() => aFakeDictionary.TryGetValue(theKey, out theValue))
 .Returns(true) 
 .AssignsOutAndRefParametersLazily((string aKey, string aValue) => new [] { aValue + aValue });
```

As shown above, the inputs to the method may be used to calculate the values to assign. Convenient overloads exist for methods of up to four parameters.

The type of the `Func` sent to `AssignsOutAndRefParametersLazily` isn't checked at compile time, but any _type_ mismatch should trigger a helpful error message.

If more advanced decision-making is required, or the method has more than 4 parameters, the convenience methods won't work. Use the variant that takes an `IFakeObjectCall` instead:

```csharp
string theValue;
A.CallTo(() => aFakeDictionary.TryGetValue(theKey, out theValue))
 .Returns(true) 
 .AssignsOutAndRefParametersLazily(objectCall => calculateValuesFrom(objectCall));
```
The `IFakeObjectCall` object provides access to
* information about the `Method` being called, as a `MethodInfo`,
* the `Arguments`, accessed by position or name, and
* the original `FakedObject`