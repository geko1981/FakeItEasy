Version 2.0 will include a number of breaking changes relative the 1.x versions, as well as some bug fixes and new functionality. Here are some highlights.

Until 2.0 is released, everything here is subject to change. The rest of the documentation has not been updated and reflects the latest 1.x release.

Note that all fixed and planned issues in 2.0 can be found on the [2.0.0 milestone](https://github.com/FakeItEasy/FakeItEasy/issues?q=milestone%3A2.0.0). Pre-release packages are available on [NuGet](https://www.nuget.org/packages/FakeItEasy).

### Changed
* Raising custom event handler events now require a typeparam (but neither `Now` nor `Go`). ([#30](https://github.com/FakeItEasy/FakeItEasy/issues/30)) For example:

  ```C#
  public delegate void CustomEventHandler(object sender, CustomEventArgs e);
  …
  event CustomEventHandler CustomEvent;
  …
  fake.CustomEvent += Raise.With<CustomEventHandler>(fake, sampleCustomEventArgs);
  ```

  To avoid this, make `CustomEvent` an `EventHandler<CustomEventArgs>`.
* Passing a null sender to `Raise.With` now raises an event with a null sender. Use `Raise.With(TEventArgs)` to raise with the Fake as the sender. ([#395](https://github.com/FakeItEasy/FakeItEasy/issues/395))
* `IDummyDefinition` and `DummyDefinition<T>` have been renamed to `IDummyFactory` and `DummyFactory<T>`. The factories are now more powerful, able to create more Dummy types from a single factory type, as the interface has changed ([#402](https://github.com/FakeItEasy/FakeItEasy/issues/402), [#441](https://github.com/FakeItEasy/FakeItEasy/issues/441)):

  ```c#
  bool CanCreate(Type);
  object Create(Type);
  int Priority {get}; 
  ```
* `IFakeConfigurator` is more flexible, and can configure more Fake types from a single factory type. The interface has changed as well ([#402](https://github.com/FakeItEasy/FakeItEasy/issues/402)):

  ```c#
  int Priority { get; }
  bool CanConfigureFakeOfType(Type type);
  void ConfigureFake(object fakeObject);
  ```   

* Fakes' methods act the same during fake creation as after. They return the same results, calls them show up in `MustHaveHappened`, and they are subject to configuration during fake construction via [[fake creation options|Creating-Fakes#options]] or user-supplied fake configurators implementing `IFakeConfigurator`. ([#371](https://github.com/FakeItEasy/FakeItEasy/issues/371))

* Fake creation option `OnFakeCreated` has been renamed to `ConfigureFake` to reflect that its effects are active during fake creation ([#454](https://github.com/FakeItEasy/FakeItEasy/issues/454))

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
