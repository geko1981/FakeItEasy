## What types can be faked?

FakeItEasy uses [[Castle DynamicProxy|http://www.castleproject.org/projects/dynamicproxy/]] to create fakes. Thus, it can fake just about anything that could normally be overridden, extended, or implemented.
This means that the following entities can be faked:

* interfaces
* classes that
    * are not sealed,
    * are not static, and
    * have at least one public or protected constructor whose arguments FakeItEasy can construct or obtain

Note that special steps will need to be taken to [[fake internal interfaces and classes|How to fake internal types]].

### Where do the constructor arguments come from?

* they can be supplied via `WithArgumentsForConstructor` as shown in [[Creating Fakes]], or
* FakeItEasy will use [[Dummies]] as arguments

## What members can be overriden?
Once a fake has been constructed, its methods and properties can be overridden if they are:

* virtual,
* abstract, or
* defined on an interface
