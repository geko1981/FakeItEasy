#summary On how to create strict mocks in FakeItEasy.

= Details =

{{{
var foo = A.Fake<IFoo>(x => x.Strict());
}}}

After you have configured your fake in this fashion you can configure any "allowed" calls as usual, for example:

{{{
A.CallTo(() => foo.Bar()).Returns("bar");
}}}