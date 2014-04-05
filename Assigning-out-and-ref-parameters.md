Sometimes methods have `out` or `ref` parameters that need filling-in when called on a Fake. Use `AssignsOutAndRefParameters`:
```csharp
string theValue;
A.CallTo(()=>aFakeDictionary.TryGetValue(theKey, out theValue))
 .Returns(true) 
 .AssignsOutAndRefParameters(someCoolValue);
```

`AssignsOutAndRefParameters` takes a `params object[]`, with one element (in order) for each of the `out` and `ref` parameters in the call being faked - the other arguments to the method should be omitted.

While assigning out and ref parameters, the `Returns` method (or [[some variant|Specifying return values]]) should be used to specify the return value for the method - `AssignsOutAndRefParameters` does not do this on its own.
