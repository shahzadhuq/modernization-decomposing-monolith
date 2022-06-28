# Objectives

Modernizing a Monolith application is a journey of consistent iterative takes and not a big bang. There is no silver bullet or perfect approach or perfect architecture. It's about moving forward with the trade-offs that work best for your organization.

This module helps you plan this journey in ways that you can continue to support the existing business (i.e. monolith app) and achieve value driven progress towards your modernization goals.

## Common Problems to Solve

Let's review some common hurdles that Enterprises face on their modernization journey.

1. Where to start chipping away at the monolith; identify specific component(s) to modernize first.

1. You may find yourself in conundrum: efficiently leverage in-house bandwidth to achieve balance across parallel tracks like:
    1. Continue to support your existing business and ship features/bug fixes for the monolith.
    2. Support both monolith and modernized code-bases: share common libraries, copy/fork the code, etc.
    3. Ensure the monolith and modernized version continues to co-exist with minimal to no interruptions.

Sections below will provide suggestions to help solve these common problems.

## Decomposed Monolith to Modular Services

### Baseline

Following steps should be the starting point for any modernization approach.

1. Start with the analysis of your monolith (yes, captain obvious here!)
    1. Build out a dependency graph that shows 1/ breakdown of monolith's components (your services and 3rd party libraries) and 2/ relationships across components.
    1. Analyze portability of these components to .NET Standard 2.0 and .NET Core. Targeting .NET Standard 2.0 will help you share common libraries between ASP.NET and .NET (aka .NET Core) codebase.
    1. Identify monolith component owners in your organization to 1/ validate your analysis and 2/ coordinate modernization timelines.

    You can leverage no-cost tools like [AWS .NET Extractor](https://aws.amazon.com/microservice-extractor/) or [AWS .NET Porting Assistant](https://aws.amazon.com/porting-assistant-dotnet/).

    > FYI: .NET Framework 4.6.1 is the earliest version to support .NET Standard 2.0.

1. From the analysis graph tree, identify leafs as the starting point; monolith component(s) with minimal incoming dependencies.
    1. You want the team to build a modernization blueprint first: 1/ gain confidence in porting approach (e.g. ASP.NET 4.7 -to- .NET LTS), 2/ stand-up the necessary infrastructure, 3/ establish your observability approach for the distributed system, and 4/ build out the DevOps mechanisms to enable Continuous Integration (CI) and Continuous Deployment (CD).

1. Also, for first round of components selection, go after high business value. Modernization value should help ensure your company continues to invest in it.

1. Where possible, ring-fence the components to modernize by business domain (unit of function).
    1. Approaches like Domain Drive Design or Event Storming can help define the boundaries. These approaches do require time investment across SMEs working collaboratively: developers, architects, business SMEs, QA, and UX.

1. Strongly recommended: Adequate unit/integration test coverage to help validate the modernized components and include in your automation (CI).
    1. Adequate test coverage is a common challenge. In absence of test coverage, ensure to incorporate ample manual functional validation time in your modernization planning.

1. Overall, perform the modernization in small increments.
    1. Ideally, while modernizing a component, you should freeze any functional or behavioral changes. Otherwise, roll-backs may become much harder.
    1. Also, longer it takes to modernize components, the more pressure/risk you may incur to allow behavioral changes into the monolith version of the component (e.g. bug fixes).

### Modernization Patterns

1. User [Strangler Fig](./modernization-patterns/1-strangler-fig-pattern.md) pattern to modernize monolith components with minimal to no incoming dependencies.
1. User [Branch by Abstraction](./modernization-patterns/2-branch-by-abstraction.md) pattern to modernize monolith components that are deeper in the call stack; do have incoming dependencies.

## Appendix

### Tips

1. Prior to component modernization, ensure to discuss and adapt a development strategy that works best for your organization. Common approaches:

    1. Trunk based development with feature flags.
    1. Short lived feature branch.

    End goal is to avoid keeping your changes on an island for a long time and then deal with reconciliation pains; merging back to master.

1. If you’re still maintaining the ASP.NET app, it may be helpful to avoid static references to ConfigurationManager and replace them with access through interfaces. This will ease the transition to ASP.NET Core’s configuration system.

1. Logging: You can reference the Microsoft.Extensions.Logging package from .NET Framework apps as long as they’re using NuGet 4.3 or later and are on .NET Framework 4.6.1 or later. Once your app has referenced this package, you can convert your logging statements to use the new extensions before migrating the app to .NET Core.

### Resources

- [Blog] - [Branch by abstraction pattern](https://continuousdelivery.com/2011/05/make-large-scale-changes-incrementally-with-branch-by-abstraction/)
- [Blog] - [Feature flag toggle approach - v1](https://martinfowler.com/articles/feature-toggles.html)
- [Blog] - [Feature flag toggle approach - v2](https://www.cloudbees.com/blog/future-of-feature-flags)
