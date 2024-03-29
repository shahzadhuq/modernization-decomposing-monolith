# Branch by Abstraction Pattern (i.e. Interface & Concrete Implementation)

Commonly used to modernize components that exist deeper in the legacy application stack, with upstream dependencies (i.e. clients). This pattern enables you to make changes to the existing codebase to allow the modernized version to safely coexist alongside the legacy version without causing much disruption.

In the example diagram below, Branch by Abstraction pattern will work well for Notification component.

![branch-by-abstraction-tldr](../diagrams/branch-by-abstraction-tldr.png)

On the contrary, using Strangler Fig pattern would require changes to upstream clients - changes to other parts of the codebase - which would be disruptive to other developers. 

**How It Works (TL;DR)**

1. Identify monolith components with upstream dependencies.
1. Create an abstraction layer for the component to be replaced and restructure the system to use this abstraction.
1. Along the existing implementation, incrementally create new implementation (port functionality over).
1. When ready, switch abstraction over to new implementation.

## Pattern Benefits

1. Allows making changes in small increments while keeping the system running at all times. Enables continuous delivery.
1. Since the system keeps on working, you could choose to release a working version of the system containing a half-completed modernization.
1. Release schedule is completely decoupled from your architectural changes. You can stop working on the restructuring at any point to do something else that is higher priority.

## Pattern Execution Walkthrough

1. Create an abstraction layer that represents the interactions between the code to be modernized and its clients (the callers of this code.). For example, in C#, create an Interface based on the existing functionality that you want to modernize.

    ![branch-by-abstraction-interface-introduced](../diagrams/branch-by-abstraction-interface-introduced.png)

1. With the abstraction in place, in small increments, you need to change the existing clients to use the new abstraction. This may require searching your codebase for calls being made to APIs related to the notification component.

    Diagram below shows incremental client update.

    ![branch-by-abstraction-interface-incorporated](../diagrams/branch-by-abstraction-interface-incorporated.png)

    Diagram below shows client updates completed.

    ![branch-by-abstraction-interface-completed](../diagrams/branch-by-abstraction-interface-completed.png)

1. Create new implementation (modernized version) of the abstraction.
    1. New implementation will likely exist outside your monolith, hosted under the modernized architecture. 
    1. Inside your monolith, you will have a client - acting as broker - calling out to the new implementation.
    1. In small increments, port Notification functionality to new implementation which will stay inactive until tested and ready.

    Diagram below shows the process.

    ![branch-by-abstraction-new-implementation-inprogress](../diagrams/branch-by-abstraction-new-implementation-inprogress.png)

1. When ready, switch your abstraction over to use the new implementation.
    1. You would want to use a switching mechanism that can be toggled easily (e.g. Feature Toggle). In case of any problems, this allows you to quickly switch back to the old functionality.

    ![branch-by-abstraction-new-implementation-switched](../diagrams/branch-by-abstraction-new-implementation-switched.png)

1. When new implementation start to provide all Notification functionality to the users and monolith is no longer in use, you can clean up the older implementation.
    1. Also, remove any switching feature flag that you may have implemented. 

    ![branch-by-abstraction-new-implementation-completed](../diagrams/branch-by-abstraction-new-implementation-completed.png)

1. Optionally, you could remove the Abstraction layer (Interface) as well.

1. Rinse and repeat the previous steps, shipping your system in the meantime if desired.

## Comparison vs Other Patterns

- Strangler Fig Pattern
  - Strangler Fig pattern operates at a higher level of abstraction than branch by abstraction, which is for incrementally changing the implementation of a component of your system.
  - Branch by Abstraction work better when the functionality to be moved is deeper inside the existing system.

    ![Strangler Fig Pattern](../diagrams/strangler-fig-pattern-unfit.png)

- Feature Toggle
  - Both patterns that allow you to make changes to your system incrementally on mainline.
  - The difference is that feature toggles are intended to allow the development of new features, while keeping those features invisible to users when the system is running.
  - Branch by abstraction can of course be combined with feature toggles. For example, we can switch between old and new implementation.