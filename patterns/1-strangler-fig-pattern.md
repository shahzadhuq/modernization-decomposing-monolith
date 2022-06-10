# Strangler Fig Pattern

## Concept

Commonly, this pattern is used to to migrate from one monolithic system to another. You use it to migrate from a monolith to modular service architecture.

There are three primary steps:

1. First, identify sub-components of the monolith to modernize.
1. Second, modernize these components; either port as is or re-write the functionality.
1. Third, reroute traffic for these components from monolith to the modernized versions.

![Strangler Fig Pattern](./diagrams/strangler-fig-pattern.png)

## Benefits

- Without directing any traffic, we can build the modernized version along side the monolith.
- Deploy modernized component to Production, perform test parallel to monolith, and validate functional behavior.
- Finally, release the modernized component; cut the traffic over - via `HTTP Proxy` - from monolith to the modernized component.

> **Deploy vs Release**: Deploy: only push to Production and validate its functionality. When ready to be consumed by the end user, we can release it (goes live).

Be mindful:

- "The strangler Fig pattern doesn’t work too well when the functionality to be moved is deeper inside the existing system." For these use cases, we can leverage `Branch by Abstraction` pattern.
- Example:
![Strangler Fig Pattern](./diagrams/strangler-fig-pattern-unfit.png)
- While modernizing component, try to freeze changes in the behavior being moved. Example, delay bug fixes or feature additions.
  - Allowing changes may reduce the rollback scenario - from modernized version back to monolith - much harder.

### Steps

[1]: Incorporate `HTTP Proxy`

1. If HTTP Proxy is new to the ecosystem, then integrate one with the existing system.
1. This helps 1/ assess impact of adding network hop and acceptable latency and 2/ ensure the system continues to operate as before.

> [Tip] - Follow the mantra of “smart endpoints, dumb pipes". Avoid inject business logic into proxy pipes (e.g. translate among protocol JSON-->gRPC); thus, converting it into a shared smart middleware pipe.

[2]: Deploy Modernized Component (not release yet!)

1. Get the basic component deployed to production. For example, component shell w/out any functionality.
    a. You want to become comfortable deploying to Production through your CI/CD pipelines.
1. Now, push component's functionality to production and validate that its working as expected.

[3]: Redirect Calls

1. Following are some common approaches to direct traffic to the modernized component:
    1. Configure the HTTP Proxy to redirect calls to the modernized component.
    1. Use Feature flags.
    1. Or run it alongside the monolith to ensure its outputs matches the existing functionality.