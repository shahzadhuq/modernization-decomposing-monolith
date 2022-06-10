# Objectives

<todo>

## Core Problems to Solve

1. Commonly customers can figure out where to start, what to retire, or replace.

1. Where customer might struggle is figuring out the right approach to achieve enables balance and maximize their limited bandwidth. Example:
    1. How do I continue to ship features and bug fixes for the monolith?
    2. How do I maximize sharing codebase b/t monolith and modernized version?
    3. How do the monolith and modernized versions co-exist w/out breaking anything?

1. Also different modernization journeys:
    1. Decompose monolith → Modular services (e.g. microservices).
    2. Monolith → Modernized Monolith.

## [Journey]: Decompose Monolith → Modular Services

### Baseline for all Approaches

1. Against monolith, start with building an inventory - internal services and 3rd party libs - and their portability to .NET Core.
    1. For instance, use tools like [AWS .NET Extractor](https://aws.amazon.com/microservice-extractor/) or [AWS .NET Porting Assistant](https://aws.amazon.com/porting-assistant-dotnet/).
1. Visualizing monolith as a tree, start with leaves (i.e. edge nodes); components with minimal incoming dependencies.
1. For the first round of component selection, go after high business value. You want to showcase the benefits of the investment.
    1. You want the team to build a blueprint: 1/ confidence in modernization approach, 2/ stand-up the necessary infrastructure, establish observability approach for your distributed system, and 4/ build out the continuous delivery mechanism.
1. Ideally, ring-fence the initial set off components to modernize by unit of business function.
    1. Approaches like Domain Drive Design or Event Storming can help define these. But these do require time investment across SMEs working collaboratively: developers, architects, business SMEs, QA, and UX.
1. Overall, keep the modernization in small increments. The longer it takes to modernize, the more pressure/risk you may incur to allow behavioral changes (e.g. bug fixes  
1. Good to have:
    1. Strong unit/integration test coverage which an be used to validate the modernized components.

> FYI: .NET Framework 4.6.1 is the earliest .NET version to support .NET Standard 2.0.

### [Modernization Approach #1] - As we go

1. n place, have the dependent libraries  (internal or 3rd party) target .NET Standard 2.0.
    1. This will allow codebase to be shared among monolith (e.g .NET Framework) and modernized (e.g. .NET Core).
1. Then use Strangler Fig pattern (piece meal sub-component) to move modernized components to Linux (Graviton/x86).
    1. Use ALB or API Gateway to direct traffic across monolith and modernized components.
1. Continue to ship features and bug fixes for Monolith, build modernized version, and maximize code sharing.
1. Potential Hurdles to lookout for:
    1. Sharing security model: `Custom Forms Auth` (monolith) <--> `OAuth` approach (modernized).
    2. Monolith may be heavily dependent on the ORM Layer which may not have path to .NET Standard and requires re-work.

### [Modernization Approach #2] - In-place first

1. In place, have the dependent libraries (internal or 3rd party) target .NET Standard 2.0.
    1. This will allow codebase to be shared among monolith (e.g .NET Framework) and modernized (e.g. .NET Core).
1. In place, piece meal sub-components monolith (.NET Framework) --> modernize (.NET Core).
1. Then use Strangler Fig pattern to move modernized components to Linux (Graviton/x86).
    1. Use ALB or API Gateway to direct traffic across monolith and modernized components.
1. Continue to ship features and bug fixes for Monolith, build modernized version, and maximize code sharing.
1. Potential Hurdles to lookout for:
    1. Sharing security model: `Custom Forms Auth` (monolith) <--> `OAuth` approach (modernized).
    2. Monolith may be heavily dependent on the ORM Layer which may not have path to .NET Standard and requires re-work.

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

### Resources

- [Blog] - [Branch by abstraction pattern](https://continuousdelivery.com/2011/05/make-large-scale-changes-incrementally-with-branch-by-abstraction/)
- [Blog] - [Feature flag toggle approach - v1](https://martinfowler.com/articles/feature-toggles.html)
- [Blog] - [Feature flag toggle approach - v2](https://www.cloudbees.com/blog/future-of-feature-flags)
