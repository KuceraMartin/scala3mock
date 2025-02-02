---
title: FAQ
---

This section answers some of the most common questions that have been asked over time. It also offers some −at times− helpful debug tips.


## Can I mock final / private methods or classes?
This is not supported, as mocks generated with macros are implemented as subclasses of the type to mock. So private and final methods cannot be overridden. You may want to try using an adapter or façade in your code to make it testable. It is better to test against a trait/interface instead of a concrete implementation. There are libraries that support this kind of mocking, such as [PowerMock](http://powermock.github.io/). Be aware that this kind of mocking involves Bytecode manipulation (to remove the final/private qualifier), which has the risk that your test double diverges from the actual implementation.

## Can I mock val / lazy val?
No, the Scala compiler will not allow overriding a `val` with a `def`, so with ScalaMock this is not possible (because mocks are "just" configurable functions). If you can, it is better to design a trait with a def and mock that instead. The concrete implementation can still override that def with a val to give invariant behaviour. Do note that an abstract `val` will be filled out with its `Default` typeclass when going through the `mock` macro.

## Can I mock objects?
No, macros cannot generate a subclass of a singleton object, sorry. A way to refactor your code may be to create a trait with the function(s) you want to mock and extend that with the object. Or use Dependency Injection (with classes or functions) to abstract your code and make it testable.

## Can I mock hashcode, equals, clone or toString ?
No - all methods from `java.lang.Object` are filtered out during Mock creation. `toString` is particularly problematic as it can cause an infinite loop blowing up the stack on test failure.

If your interface requires you to implement clone for example, you can create an abstract class that extends your interface with the `Object` methods you need. That should make those methods available to Scala3Mock.

## Can I mock static Java calls?

Scala3Mock works by creating a subclass and instantiating said subclasses. As static methods are defined on the original class itself, Scala3Mock cannot mock them. We recommend you use Dependency Injection, higher-order functions or OO patterns (interfaces, facades, etc) instead of depending directly on implementations.
