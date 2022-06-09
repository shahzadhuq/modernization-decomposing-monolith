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

### Strangler Fig Pattern

#### Concept
Commonly, this pattern is used to to migrate from one monolithic system to another. You use it to migrate from a monolith to modular service architecture.

There are three primary steps:

1. First, identify sub-components of the monolith to modernize.
1. Second, modernize these components; either port as is or re-write the functionality.
1. Third, reroute traffic for these components from monolith to the modernized versions.

![Strangler Fig Pattern](./diagrams/strangler-fig-pattern.png)

#### Benefits
- Without directing any traffic, we can build the modernized version along side the monolith.
- Deploy modernized component to Production, perform test parallel to monolith, and validate functional behavior.
- Finally, release the modernized component; cut the traffic over - via `HTTP Proxy` - from monolith to the modernized component.

> **Deploy vs Release**: Deploy: only push to Production and validate its functionality. When ready to be consumed by the end user, we can release it (goes live).

Be mindful:
- "The strangler Fig pattern doesn’t work too well when the functionality to be moved is deeper inside the existing system."
- Example:
![Strangler Fig Pattern](./diagrams/strangler-fig-pattern-unfit.png)

#### Steps

[1]: Incorporate `HTTP Proxy`

1. If HTTP Proxy is new to the ecosystem, then simply integrate with the existing system.
1. This helps 1/ assessing impact of adding network hop and acceptable latency and 2/ ensuring the system still works as expected.

[2]: Deploy Modernized Component (not release!)

1. Get the basic component deployed to production. For example, component shell w/out any functionality.
    a. You want to become comfortable deploying to Production through your CI/CD pipelines.
1. Now, push functionality over
1. 