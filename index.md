# Objectives

Modernizing a Monolith application is a journey. This module helps you plan this journey such that you can continue to support the existing business (i.e. monolith app) and achieve meaningful and value driven progress towards modernization.

Remember, there is no silver bullet. Use the information below and apply your own knowledge to form the right approach that works best for your organization.

## Common Problems to Solve

Following are common hurdles that Enterprises face on their modernization journey.

1. Where to start chipping away - which sub-component(s) to modernize first - at the monolith.

1. You may find yourself in conundrum to maximize your in-house bandwidth and achieve balance for parallel tracks like:
    1. How do I continue to support my existing business: ship features and bug fixes for the monolith?
    2. What is my code-sharing strategy, so the team is not overburden maintaining two separate codebase.
    3. How does the monolith and modernized version co-exist w/out breaking anything?

1. Also, different modernization paths:
    1. Monolith → Decomposed Modular services (e.g. microservices).
    2. Monolith → Modernized Monolith.

Sections below will provide guidance to help solve these common problems.

## [Modernization Path]: Monolith --> Decomposed Modular Services

### Baseline

Irrespective of the modernization approaches discussed later, following steps should be the starting point.

1. Against your monolith, 1/ build an inventory of internal services and 3rd party libs and 2/ their portability to .NET Standard 2.0 and .NET Core.
    1. For instance, use tools like [AWS .NET Extractor](https://aws.amazon.com/microservice-extractor/) or [AWS .NET Porting Assistant](https://aws.amazon.com/porting-assistant-dotnet/).

1. Visualizing monolith as a tree, identify leaves as the starting point; components with minimal incoming dependencies.
    1. You want the team to build a modernization blueprint first: 1/ confidence in porting approach (e.g. .NET -to- .NET Core), 2/ stand-up the necessary infrastructure, 3/ establish your observability approach for the distributed system, and 4/ build out the DevOps mechanisms to enable CI/CD.

1. As applicable, ring-fence the initial set off components to modernize by unit of business function.
    1. Approaches like Domain Drive Design or Event Storming can help define these. But these do require time investment across SMEs working collaboratively: developers, architects, business SMEs, QA, and UX.

1. Also, for first round of components selection, go after high business value. You want to showcase the value of modernization to ensure your company continues to invest in it.

1. Overall, perform the modernization in small increments. The longer it takes to modernize, the more pressure/risk you may incur to allow behavioral changes (e.g. bug fixes).  

1. Strongly recommended: good unit/integration test coverage to help validate the modernized components.

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
