Most of FakeItEasy's functionality is directly triggered by client code: [[creating a fake|Creating fakes]], [[configuring a call|Specifying a call to configure]] and [[making assertions about calls|assertion]] are all explicitly invoked and are controllable by various input parameters.

Some behavior is triggered implicitly. FakeItEasy initializes itself when its classes are first accessed. The Bootstrapper<sup>1</sup> allows users to customize the initialization process.

## What does the Bootstrapper do?

At present, the Bootstrapper provides only one service:

* `GetAssemblyFileNamesToScanForExtensions` provides a list of absolute paths to assemblies that should be [[scanned for extension points|Scanning for Extension Points]].  
The default behavior is to return a list of all DLLs in the current working directory.

## How can the behavior be changed?

Provide an alternative bootstrapper class and ensure that it is loaded in the current AppDomain before FakeItEasy is initialized (often this means just including it in your test assembly).

The best way to provide an alternative implementation is to **extend FakeItEasy.DefaultBootstrapper**. This class defines the default FakeItEasy setup behavior, so using it as a base allows clients to change only those aspects of the initialization that need to be customized.

### An example: disabling on-disk assembly scanning for extensions

One recurring complaint about FakeItEasy is that the startup time is slow, especially for solutions that have many assemblies. This is often caused by FakeItEasy examining all assemblies in the current directory in case they include extension points. If a test solution does not define any extension points in assemblies that are not currently loaded in the AppDomain, examining the on-disk assemblies is more annoying than helpful. The following bootstrapper can be used to keep those assemblies from being scanned.

```csharp
public class NoExternalScanningBootstrapper : FakeItEasy.DefaultBootstrapper
{
    public override IEnumerable<string> GetAssemblyFilenamesToScanForExtensions()
    {
        return Enumerable.Empty<string>();
    }
}
```

## How does FakeItEasy find alternative bootstrappers?

Just before the first Bootstrapper function needs to be accessed, FakeItEasy checks all the assemblies currently loaded in the AppDomain. Each assembly is examined for exported types that implement `FakeItEasy.IBootstrapper`. The first such type that is not `FakeItEasy.DefaultBootstrapper` is instantiated and used. If no such type is found, then `FakeItEasy.DefaultBootstrapper` is used. 

**Note that there is no warning provided if FakeItEasy finds more than one custom bootstrapper implementation. One will be chosen non-deterministically.**

----
1. The Bootstrapper was introduced in FakeItEasy 1.18.0. If you have need of its functionality and are using an older version of FakeItEasy, [upgrade now](https://nuget.org/packages/FakeItEasy/).