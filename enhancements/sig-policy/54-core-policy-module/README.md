# Core Policy Module

## Release Signoff Checklist

- [ ] Enhancement is `implementable`
- [ ] Design details are appropriately documented from clear requirements
- [ ] Test plan is defined
- [ ] Graduation criteria for dev preview, tech preview, GA
- [ ] User-facing documentation is created in [website](https://github.com/open-cluster-management-io/open-cluster-management-io.github.io/)

## Summary

A common repository / module will contain common API validation, structs, and
methods that multiple policy controllers can use. This will simplify creating
new controllers, and assist in maintenance of existing ones. 

## Motivation

Current policy controllers rely on detailed knowledge from the policy framework 
implementation, that is not clear to new users. Most notably, the format needed
for events to be picked up by the policy framework is not well documented. Other
details will be more consistent if maintained in a single place.

### Goals

1. Common API validation: fields like `.spec.severity` which use enums will have
consistent validation through kubebuilder when custom policy types embed them.
1. Required API features: fields like `.spec.remediationAction` are required on
all policy types so they can be passed down from the parent policy. Putting
these in a struct that policy types embed will ensure they are implemented.
1. Common methods: behavior like selecting namespaces and emitting compliance
events for the policy framework will have a common implementation. This will
reduce bugs from inconsistency, and allow policy controllers to focus on their
specific behavior, not the details of interacting with the framework.

### Non-Goals

1. Custom Controller Tutorial: it will be easier to create custom controllers,
but it is not a requirement that we maintain a tutorial around this process.
1. Custom Controller Template: we will not maintain a template/sdk like the 
operator-sdk for new policy controllers.

## Proposal

Create a new repository in open-cluster-mangement-io to provide implementations
of common APIs and functions for controllers to work with the policy framework.

### User Stories

#### Story 1

As a developer of a policy controller, I can ensure consistent behavior with
other policies by incorporating the common API types.

#### Story 2

As a creator of a new policy controller, I can focus on my controller's 
specialized detection of violations, and rely on common implementations of
functions in order to interact with the policy framework.

#### Story 3

As a developer of multiple policy controllers, I can fix a bug or add a feature
to common policy behavior in one place, and not duplicate it in each controller.

### Implementation Details/Notes/Constraints 

Although kubebuilder will automatically pull API validation from embedded
structs (like when types embed `metav1.TypeMeta`), it will not automatically
pull RBAC permissions on methods into the project. So RBAC requirements on
common functions (eg `get namespaces` for selecting namespaces) will need to
be well-documented.

### Risks and Mitigation

(unknown)

## Design Details

### Open Questions

1. What policy features should be included?
1. Should new features proposed for policy controllers be implemented here, or 
in a specific controller first?

### Test Plan

**Note:** *Section not required until targeted at a release.*

<!--
Consider the following in developing a test plan for this enhancement:
- Will there be e2e and integration tests, in addition to unit tests?
- How will it be tested in isolation vs with other components?

No need to outline all of the test cases, just the general strategy. Anything
that would count as tricky in the implementation and anything particularly
challenging to test should be called out.

All code is expected to have adequate tests (eventually with coverage
expectations). 

All code is expected to have sufficient e2e tests.
-->

### Graduation Criteria

**Note:** *Section not required until targeted at a release.*

<!--
Define graduation milestones.

These may be defined in terms of API maturity, or as something else. Initial proposal
should keep this high-level with a focus on what signals will be looked at to
determine graduation.

Consider the following in developing the graduation criteria for this
enhancement:

- [Maturity levels][maturity-levels]
- [Deprecation policy][deprecation-policy]

Clearly define what graduation means by either linking to the [API doc definition](https://kubernetes.io/docs/concepts/overview/kubernetes-api/#api-versioning),
or by redefining what graduation means.

In general, we try to use the same stages (alpha, beta, stable), regardless how the functionality is accessed.

[maturity-levels]: https://git.k8s.io/community/contributors/devel/sig-architecture/api_changes.md#alpha-beta-and-stable-versions
[deprecation-policy]: https://kubernetes.io/docs/reference/using-api/deprecation-policy/
-->

### Upgrade / Downgrade Strategy

New features and adjustments to existing features should be made in a backwards
compatible way. In particular, this means that new items in common structs
to be embedded by policy types can not be required.

Since the intent is to have other policy controllers import this for common
functions, those functions should maintain their behavior, except for bugfixes.

## Implementation History

Major milestones in the life cycle of a proposal should be tracked in `Implementation
History`.

## Drawbacks

The idea is to find the best form of an argument why this enhancement should _not_ be implemented.

## Alternatives

Similar to the `Drawbacks` section the `Alternatives` section is used to
highlight and record other possible approaches to delivering the value proposed
by an enhancement.

## Infrastructure Needed

A new repository, name TBD.