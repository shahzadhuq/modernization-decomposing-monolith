# Branch by Abstraction (i.e. Interface & Concrete Implementation)

Branch by abstraction is pattern for making large-scale changes to your application incrementally; its a development technique.

> Note: this pattern represents an alternative to using branching in version control. Like a Feature branch.

## Process

1. Create an abstraction for the functionality to be replaced.
    1. Also, have the existing concrete implementation, implement this new abstraction interface.
1. Refactor rest of the existing system - functionality's existing clients - to use the new abstraction layer.
1. Create new concrete implementation of the abstraction.
    1. While being developed and tested, this continues to live along the existing implementation.
1. When ready, switch over the abstraction to use new implementation.
1. Rinse and repeat the previous steps, shipping your system in the meantime if desired.
1. Once the old implementation has been completely replaced, you can remove old implementation.
    1. Also, you could look to remove the abstraction layer as well.

    ![Branch by Abstraction](../diagrams/branch-by-abstraction.png)

## Benefits

1. Your code is working at all times throughout the re-structuring, enabling continuous delivery.
1. Release schedule is completely decoupled from your architectural changes, and thus you can stop working on the restructuring at any point to do something else that is higher priority, such as putting out a release with an exciting new feature you just thought up.

## Comparison vs Other Patterns

- Strangler Fig Pattern
  - Strangler Fig pattern operates at a higher level of abstraction than branch by abstraction, which is for incrementally changing the implementation of a component of your system.
  - Branch by Abstraction work better when the functionality to be moved is deeper inside the existing system.

    ![Strangler Fig Pattern](../diagrams/strangler-fig-pattern-unfit.png)

- Feature Toggle
  - Both patterns that allow you to make changes to your system incrementally on mainline.
  - The difference is that feature toggles are intended to allow the development of new features, while keeping those features invisible to users when the system is running.
  - Branch by abstraction can of course be combined with feature toggles. For example, we can switch between old and new implementation.