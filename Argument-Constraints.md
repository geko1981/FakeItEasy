When configuring and [[asserting|Assertion]] calls in FakeItEasy, the arguments of the call can be constrained so that only calls to the configured method where the arguments matches the constraint are selected.

##The Syntax
    var foo = A.Fake<IFoo>();
    
    // Asserting on the method "Bar" that takes a string value and an int value as arguments
    A.CallTo(() => foo.Bar(A<string>.Ignored, A<int>.That.Matches(x => x < 10))).MustHaveHappened();
    
    // You can have constraints for only a subset of the arguments if you want.
    A.CallTo(() => foo.Bar("this exact string", A<int>.Ignored)).MustHaveHappened();

**[Comparing object instances with FakeItEasy][1]**

##Ignored
The "Ignored"-property has a shortcut: "_", the following are equivalent:

    A.CallTo(() => foo.Bar(A<string>.Ignored)).Throws(new Exception());
    A.CallTo(() => foo.Bar(A<string>._)).Throws(new Exception());

[1]: http://blog.jonathanchannon.com/2013/09/11/comparing-object-instances-with-fakeiteasy/