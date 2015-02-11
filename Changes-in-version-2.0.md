Version 2.0 will include a number of breaking changes relative the 1.x versions, as well as some bug fixes and new functionality. Here are some highlights.

Until 2.0 is released, everything here is subject to change. The rest of the documentation has not been updated and reflects the latest 1.x release.

Note that all fixed and planned issues in 2.0 can be found on the [2.0.0 milestone](https://github.com/FakeItEasy/FakeItEasy/issues?q=milestone%3A2.0.0). Pre-release packages are available on [NuGet](https://www.nuget.org/packages/FakeItEasy).

### Changed
* Raising custom event handler events now require a typeparam. ([#30](https://github.com/FakeItEasy/FakeItEasy/issues/30))
* Passing a null sender to `Raise.With` now raises an event with a null sender. Use `Raise.With(TEventArgs)` to raise with the Fake as the sender. ([#395](https://github.com/FakeItEasy/FakeItEasy/issues/395))
* `IDummyDefinition` and `DummyDefinition<T>` have been renamed to `IDummyFactory` and `DummyFactory<T>`. The names of methods on the interface have also changed. ([#440](https://github.com/FakeItEasy/FakeItEasy/issues/440))
* `IDummyFactory` is more flexible, and can create more Dummy types from a single factory type. ([#402](https://github.com/FakeItEasy/FakeItEasy/issues/402))
* `IFakeConfigurator` is more flexible, and can configure more Fake types from a single factory type. ([#402](https://github.com/FakeItEasy/FakeItEasy/issues/402))
* Moved `ArgumentCollection`, `IRepeatSpecification`, `Raise` to `FakeItEasy.Configuration` namespace. There should be no need to access these except as return values from API methods. ([#432](https://github.com/FakeItEasy/FakeItEasy/issues/432))

### Removed from public API
* `Now` and `Go`, formerly used when raising events. ([#30](https://github.com/FakeItEasy/FakeItEasy/issues/30))
* `Any`, and `Configure` types. Also the `FakeItEasy.ExtensionSyntax` namespace, which provided `fake.Configure().CallsTo(…)`, `fake.CallsTo(…)`. Use `A.CallTo(…)` instead. ([#408](https://github.com/FakeItEasy/FakeItEasy/issues/408), [#410](https://github.com/FakeItEasy/FakeItEasy/issues/410))
* static methods `A.Equals`, `A.ReferenceEquals`, `Fake.Equals`, `Fake.ReferenceEquals`. Use corresponding methods on `object` instead. ([#425](https://github.com/FakeItEasy/FakeItEasy/issues/425))
* `ArgumentCollection.Empty` (and class's constructors), `ITypeCatalogue`, `TypeCatalogue`, `FakeManager.Factory` and `FakeManager`'s constructor, `ICallCollectionAndCallMatcherAccessor`, `ICallMatcher`, `ICallMatcherAccessor`, `ProxyGeneratorResult`. All known uses for these were internal to the library. ([#428](https://github.com/FakeItEasy/FakeItEasy/issues/428))
* `IFakeObjectCallRuleWithDescription`. No known uses. ([#410](https://github.com/FakeItEasy/FakeItEasy/issues/410)) 

### Fixed
* Improved exception thrown when fake's base's constructor fails. ([#367](https://github.com/FakeItEasy/FakeItEasy/issues/367))

### New
* Can now raise events of arbitrary delegate type. ([#30](https://github.com/FakeItEasy/FakeItEasy/issues/30))
* Dummy `Lazy<T>` values now default to having a value (which is a Dummy of type `T`). ([#358](https://github.com/FakeItEasy/FakeItEasy/issues/358))
