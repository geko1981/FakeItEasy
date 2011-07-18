Assertion uses the exact same syntax as configuration to specify what call to assert on, then followed by the "MustHaveHappened"-method which takes a specification of the expected repeat in the form of a "Repeat"-object.

Two extension methods are provided for convenience, "MustHaveHappened" that takes no argument and "MustNotHaveHappened" that asserts that the specified call has not happened at all.

Arguments are constrained using argument constraints just like when configuring calls.

#Details
##Syntax

    // Asserting that a call has happened once.
    A.CallTo(() => foo.Bar()).MustHaveHappened(Repeated.AtLeast.Once);
    
    // The same assert using the extension method that takes no arguments
    A.CallTo(() => foo.Bar()).MustHaveHappened();
    
    // Asserting that a call has not happened.
    A.CallTo(() => foo.Bar()).MustNotHaveHappened();
    
    // Which is the same as.
    A.CallTo(() => foo.Bar()).MustHaveHappened(Repeated.Never);

#Specifying Repeat

    // Using the Repeated-class:
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

##VB.Net

    ' Function calls are the same as in c#
    A.CallTo(Function() foo.Bar()).MustHaveHappened()
    
    ' Sub calls uses the "FakeItEasy.VisualBasic.NextCall" class
    NextCall.To(foo).MustHaveHappened()
    foo.SomeSub()