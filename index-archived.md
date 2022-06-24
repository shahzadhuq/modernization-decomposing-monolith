# Objectives

Modernizing a Monolith application is a journey of iterative paths and not a big bang. This module helps you plan this journey in ways that you can continue to support the existing business (i.e. monolith app) and achieve value driven progress towards your modernization goals.

Remember, there is no silver bullet or perfect approach or perfect architecture; avoid the analysis paralysis. It's about moving forward with the trade-offs that work best for your organization.

TODO When finished, see if this applies? > Discussions in this module assumes that modernization will target .NET Core LTS, Linux with ARM/x86 architecture, and Cloud native services (assuming AWS here).

## Common Problems to Solve

Following are common hurdles that Enterprises face on their modernization journey.

1. Where to start chipping away at the monolith; identify specific component(s) to modernize first.

1. You may find yourself in conundrum: efficiently leverage in-house bandwidth to achieve balance across parallel tracks like:
    1. How do I continue to support my existing business: ship features and bug fixes for the monolith?
    2. Support both monolith and modernized code-bases: do I share common libraries, do I copy/fork the code, etc.
    3. How does the monolith and modernized version co-exist with minimal to no interruptions?

1. Also, different modernization paths:
    1. Monolith → Decomposed Modular services (e.g. microservices).
    2. Monolith → Modernized Monolith.

Sections below will provide guidance to help solve these common problems.

## [Modernization Path]: Monolith --> Decomposed Modular Services

### Baseline

Following steps should be the starting point for any modernization approach.

1. Start with the analysis of your monolith (yes, captain obvious here!)
    1. Build out a dependency graph that shows 1/ breakdown of its components (your services and 3rd party libraries) and 2/ relationships across components.
    1. Analyze portability of these components to .NET Standard 2.0 and .NET Core.
    1. Identify component owners in your organization to 1/ confirm your analysis and 2/ coordinate modernization timelines.

    You can leverage no-cost tools like [AWS .NET Extractor](https://aws.amazon.com/microservice-extractor/) or [AWS .NET Porting Assistant](https://aws.amazon.com/porting-assistant-dotnet/).

    > FYI: .NET Framework 4.6.1 is the earliest version to support .NET Standard 2.0.

1. From the analysis graph tree, identify leafs as the starting point; application components with minimal incoming dependencies.
    1. You want the team to build a modernization blueprint first: 1/ gain confidence in porting approach (e.g. .NET -to- .NET Core), 2/ stand-up the necessary infrastructure, 3/ establish your observability approach for the distributed system, and 4/ build out the DevOps mechanisms to enable Continuous Integration (CI) and Continuous Deployment (CD).

1. Also, for first round of components selection, go after high business value. Modernization value should help ensure your company continues to invest in it.

1. Where possible, ring-fence the components to modernize by business domain (unit of function).
    1. Approaches like Domain Drive Design or Event Storming can help define the boundaries. But these do require time investment across SMEs working collaboratively: developers, architects, business SMEs, QA, and UX.

1. Strongly recommended: good unit/integration test coverage to help validate the modernized components and include in your automation (CI).

1. Overall, perform the modernization in small increments.
    1. Ideally, while modernizing a component, you should freeze any functional or behavioral changes. Otherwise, roll-backs may become much harder.
    1. The longer it takes to modernize components, the more pressure/risk you may incur to allow behavioral changes into the monolith version of the component (e.g. bug fixes).

### Modernization Approach - For leaf Nodes

1. In place, try to get the shared class libraries - internal services and 3rd party - target .NET Standard 2.0.
    1. This will allow code-sharing among monolith app (e.g .NET Framework) and the modernized version (e.g. .NET Core).

1. Then use Strangler Fig pattern to modernize components iteratively.

1. Continue to ship features and bug fixes for Monolith, build modernized version, and maximize code sharing.

### [Modernization Approach #2] - In-place first

1. In place, try to get the shared class libraries - internal services and 3rd party - target .NET Standard 2.0.
    1. This will allow code-sharing among monolith app (e.g .NET Framework) and the modernized version (e.g. .NET Core).

1. In place, piece meal sub-components and port them over to .NET Core monolith (.NET Framework) --> modernize (.NET Core).

1. Then use Strangler Fig pattern to modernize components iteratively.

1. Continue to ship features and bug fixes for Monolith, build modernized version, and maximize code sharing.

### [Modernization Approach #3] - All In-place

1. In place, piece meal sub-components monolith (.NET Framework) --> modernize (.NET Core).
1. Once all monolith codebase is converted to modernized version then deploy, test, and finally cut over to Linux (Graviton/x86).
    1. Use ALB or API Gateway to direct traffic across monolith and modernized components.
1. Continue to ship features and bug fixes for Monolith, build modernized version, and maximize code sharing.

## Appendix

### Tips

1. Prior to component modernization, ensure to discuss and adapt a development strategy that works best for you. Common approaches:
    1. Trunk based development with feature flags.
    1. Short lived feature branch.

    End goal is to not keep the changes on an island and then dealing with reconciliation pains; merging back to master.

1. If you’re still maintaining the ASP.NET app, it may be helpful to avoid static references to ConfigurationManager and replace them with access through interfaces. This will ease the transition to ASP.NET Core’s configuration system.
1. Logging:

You can reference the Microsoft.Extensions.Logging package from .NET Framework apps as long as they’re using NuGet 4.3 or later and are on .NET Framework 4.6.1 or later. Once your app has referenced this package, you can convert your logging statements to use the new extensions before migrating the app to .NET Core.

### Resources

- [Blog] - [Branch by abstraction pattern](https://continuousdelivery.com/2011/05/make-large-scale-changes-incrementally-with-branch-by-abstraction/)
- [Blog] - [Feature flag toggle approach - v1](https://martinfowler.com/articles/feature-toggles.html)
- [Blog] - [Feature flag toggle approach - v2](https://www.cloudbees.com/blog/future-of-feature-flags)
