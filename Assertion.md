Assertion uses exactly the same syntax as configuration to specify the call to be asserted, followed by `.MustHaveHappened(Repeated)` where `Repeated` specifies the number of expected repetitions.

Two extension methods are provided for convenience:
* `MustHaveHappened()` (no arguments) ignores the number of times the call was made, and 
* `MustNotHaveHappened()` asserts that the specified call did not happen at all.

Arguments are constrained using [[Argument Constraints]] just like when configuring calls.

#Details
##Syntax

```C#
// Asserting that a call has happened at least once.
// The following two lines are equivalent.
A.CallTo(() => foo.Bar()).MustHaveHappened(Repeated.AtLeast.Once);    // or
A.CallTo(() => foo.Bar()).MustHaveHappened();

// To contrast, assert that a call has happened exactly once.
A.CallTo(() => foo.Bar()).MustHaveHappened(Repeated.Exactly.Once);

// Asserting that a call has not happened.
// The following two lines are equivalent.
A.CallTo(() => foo.Bar()).MustNotHaveHappened();    // or
A.CallTo(() => foo.Bar()).MustHaveHappened(Repeated.Never);
```

#Specifying Repeat

```C#
// Using the Repeated class:
Repeated.AtLeast.Once // The call must have happened once or more.
Repeated.Exactly.Once // The call must have happened exaclty one time
    
Repeated.AtLeast.Twice // The call must have happened twice or more.
Repeated.Exactly.Twice // The call must have happened twice exactly.
Repeated.NoMoreThan.Twice // The call must have happened zero, one, or two times.

Repeated.AtLeast.Times(10) // The call must have happened ten times or more
Repeated.Exactly.Times(10) // The call must have happened ten times exactly
Repeated.NoMoreThan.Times(10) // The call must have happened any number of times between zero and ten.
    
// Using a predicate.
Repeated.Like(x => x % 2 == 0) // The call must have happened an even number of times.
```

##VB.Net

```vb.net
' Function calls are the same as in C#
A.CallTo(Function() foo.Bar()).MustHaveHappened()

' Sub calls uses the "FakeItEasy.VisualBasic.NextCall" class
NextCall.To(foo).MustHaveHappened()
foo.SomeSub()
```