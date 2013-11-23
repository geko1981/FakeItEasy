Although you can explicitly [[specify the return value for a called property getter|Specifying Return Values]], there's an easier, more intuitive way to work with read/write properties.
By default, any [[fakeable|What can be faked#what-members-can-be-overriden]] property that has both a `set` and `get` accessor behaves like you might expect. Setting a value and then getting the value returns the value that was set.
```csharp
var fakeShop = A.Fake<ICandyShop>();

fakeShop.Address = "123 Fake Street";

System.Console.Out.Write(fakeShop.Address); 
// prints "123 Fake Street"
```

This behaviour can be used to 
* supply values for the system under test to use (via the getter) or to
* verify that the system under test performed the `set` action on the Fake