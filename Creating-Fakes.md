##Natural fakes
The common way to create a fake object is by using the A.Fake-syntax, for example:

    var foo = A.Fake<IFoo>();

This will return a faked object that is an actual instance of the type specified (IFoo in this case).

##Options
When creating fakes you can, through a fluent interface, specify options for how the fake should be created.

* Specify arguments for the constructor of the faked type.
* Specify additional interfaces that the fake should implement.
* Causing a fake to have [[strict mocking semantics|Strict Fakes]]
* Create a fake that wraps another object.
  * Specify a recorder for wrapping fakes.

Examples:

    // Specifying additional interfaces to be implemented.
    var foo = A.Fake<FooClass>(x => x.Implements(typeof(IFoo));
    
    // Specifying arguments for constructor using IEnumerable<object>.
    var foo = A.Fake<FooClass>(x => x.WithArgumentsForConstructor(new object[] { "foo", "bar" }));
    
    // Specifying arguments for constructor using expression. This is refactoring friendly!
    var foo = A.Fake<FooClass>(x => x.WithArgumentsForConstructor(() => new FooClass("foo", "bar")));

    // Create wrapper
    var wrapped = new FooClass("foo", "bar");
    var foo = A.Fake<IFoo>(x => x.Wrapping(wrapped));

##Unnatural fakes
For those accustomed to [Moq](http://www.moqthis.com/) there is an alternative way of creating fakes through the "new Fake-syntax". The fake provides a fluent interface for configuring the faked object:

    var fake = new Fake<IFoo>();
    fake.CallsTo(x => x.Bar("some argument")).Returns("some return value");

    var foo = fake.FakeObject;

For an alternative look at migrating from Moq to FakeItEasy, see Daniel Marbach's blog post that talks about [Migration from Moq to FakeItEasy with Resharper Search Patterns](http://www.planetgeek.ch/2013/07/18/migration-from-moq-to-fakeiteasy-with-resharper-search-patterns/).