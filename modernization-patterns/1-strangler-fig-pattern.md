# Strangler Fig Pattern

## Pattern Origins (optional read)
<details>
<summary>click to expand</summary>
First captured by Martin Fowler, who was inspired by a certain type of fig that seeds itself in the upper branches of trees. The existing tree becomes initially a support structure for the new fig. The fig then descends toward the ground to take root, gradually enveloping the original tree, and leaving only the new self-supporting fig in its place.
</details>
<br/>

## Overview

<details>
<summary>click to expand</summary>

This pattern is commonly used to incrementally replace a legacy system - component by component - with a modernized version. The idea is that the old and the new will coexist and new system initially be supported by, and wrapping, the existing system. This will give the new system time to grow and potentially entirely replace the old system. 

For the strangler to work well, you need to be able to easily redirect (e.g. API Gateway) the inbound calls - *from outside the system* - for functionality you care about to the asset that you want to move.

For example, Strangler Fig pattern will work well for Product, Order, Billing, and Invoicing components shown in the diagram below. You can redirect outside the system inbound HTTP calls to their modernized version. 

For Notification component, this pattern would not work well. Because notifications are fired as a result of multiple inbound calls to the existing monolith. Therefore, you can’t clearly redirect the calls from outside the system itself. Another pattern called [Branch by Abstraction](./2-branch-by-abstraction.md) would work well (discussed later).

![strangler fig pattern tldr](../diagrams/strangler-fig-pattern-tldr.png)

> The more work you have to do in the proxy layer to understand and potentially redirect the inbound call, the less viable Strangler Fig pattern option may become.

**How It Work (TL;DR)**
1. Identify component(s) to modernize first.
1. Parallel to the monolith, modernize component(s); as applicable, port or re-write.
1. Keep monolith around (for rollback) but redirect existing calls to the modernized version.

</details>

<br/>

## Pattern Benefits

<details>
<summary>click to expand</summary>

- This pattern allows you to move functionality over to your new services architecture without having to touch or make any changes to your existing system.

- Incremental modernization to a new system gives you the ability to pause/stop while still taking advantage of the new system delivered so far.

- Without switching live traffic, you can deploy the modernized components to production, perform tests in-parallel to the existing system, and validate functional behavior.

> **Deployment vs Release**: Important to keep the deployment & release concepts separate. `Deployment`: only push to Production and validate functional behavior. When modernized component is ready to be consumed by the end user, you can `Release` it (goes live).

**Be mindful:**

- To modernize components which exist deeper inside the existing system with upstream dependencies, [Branch by Abstraction](./2-branch-by-abstraction.md) is better pattern.

  Example: In sample application depicted below, you want to modernize Notification component.

  ![branch-by-abstraction-overview](../diagrams/branch-by-abstraction-overview.png)

- While modernizing component, try to freeze functional or behavioral changes (e.g. bug fixes or feature additions).
  - Allowing changes may make the rollback - switch from modernized to monolith version - scenario much harder.

</details>

<br/>

## Pattern Execution Process

<details>
<summary>click to expand</summary>

 Prior to steps below, ensure you've reviewed `Decomposed Monolith to Modular Services / Baseline` section in [index.md](../index.md).

1. Identify modernization candidate component(s). For the sample application depicted below, we will choose Invoicing component.

    ![strangler-fig-identify-component](../diagrams/strangler-fig-identify-component.png)

1. Incorporate HTTP Proxy (e.g API Gateway) to help control the incoming traffic (e.g. direct between monolith/modernized versions).

    1. If Proxy is new to the ecosystem, then integrate one; as simple pass through.
    1. Pass through approach will help you with: 1/ assess impact of adding network hop and acceptable latency and 2/ ensure the system continues to operate as before.

    ![strangler-fig-http-proxy](../diagrams/strangler-fig-http-proxy.png)

    > [Tip] - Follow the mantra of “smart endpoints, dumb pipes". Avoid injecting business logic into proxy pipes (e.g. translate among protocol JSON-->gRPC); thus, converting this Proxy into yet another shared smart middleware pipe.

1. Iteratively modernize component's functionality. Example:
    1. Port its codebase from .NET to .NET Core, targeting Linux (ARM or x86).
    1. Reference common libraries via NuGet packages.
    1. As applicable, containerize the component (e.g. Docker).
    1. Validate against your unit/integration tests.

    ![strangler-fig-modernize-component](../diagrams/strangler-fig-modernize-component.png)

    > [Tip] - While porting, ensure any functionality rewrites are small - doable over few days or weeks - and ship this reworked functionality to your customers regularly. If the timelines start to look more like several months, you should reexamine your approach.

1. Deploy modernized component (not release yet!)

    1. Deploy the component to production. For example, component shell w/out any functionality.
        a. You want to become comfortable deploying to Production through your CI/CD pipelines.
    1. As you continue to build out the modernized component, you can iteratively start pushing it to production and validate that its working as expected.

1. Redirect Calls

    1. Modernized component is fully built, tested, and deployed to production and ready to go live!
    1. Following are some common approaches to direct traffic to the modernized component:
        1. Configure the HTTP Proxy to redirect calls to the modernized component.
        1. Use Feature flags.
        1. Or run it alongside the monolith to ensure its outputs matches the existing functionality.
  
    ![strangler-fig-switch-traffic](../diagrams/strangler-fig-switch-traffic.png)

</details>