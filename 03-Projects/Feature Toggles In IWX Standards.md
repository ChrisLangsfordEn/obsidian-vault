
### Toggle Types
- Boolean
- More types TBC as adoption matures

### Activation Strategies
- [Username](https://www.togglz.org/documentation/activation-strategies#username)
- [Gradual rollout](https://www.togglz.org/documentation/activation-strategies#gradual-rollout)
- [Release date](https://www.togglz.org/documentation/activation-strategies#release-date)
- [Client IP](https://www.togglz.org/documentation/activation-strategies#client-ip)
- [Server IP](https://www.togglz.org/documentation/activation-strategies#server-ip)
- [ScriptEngine](https://www.togglz.org/documentation/activation-strategies#script-engine)
- [System Properties](https://www.togglz.org/documentation/activation-strategies#property)

For now in Insureworx we will only be using the [System Properties](https://www.togglz.org/documentation/activation-strategies#property)
toggle strategy as we build up release discipline and familiarity with the feature toggle approach.
The other strategies are still available and can be adopted when certain use cases present themselves
### When to introduce a feature toggle?

### Naming Conventions
<Type>-<FeatureArea>-<Description>
e.g.
- RELEASE_SERVICING_ENABLE_SMARTWORX
- FIX_HCP_DUPLICATE_CHECK_ENHANCEMENT_25-08-12

### Ownership 
- Track who is responsible for each toggle to ensure accountability and facilitate proper management and cleanup.
- In the case of IWX, each pod is responsible for the maintenance of their own feature toggles

### Lifecycle
- When defining a new feature toggle, it is important to consider the lifecycle of the toggle. The following questions should be asked when defining a feature toggle:
	- Is the toggle present to revert a bug fix if issues arise during PIT?
	- Is the toggle preventing new code from being activated before its official release (for continuous integration purposes)
	- Is this toggle for a new feature set?
### Clean up
- Regular chore tickets are required per sprint to remove older feature toggles as we go. e.g. toggles to remain in the code base for no more than 2 release cycles before being cleaned up.


## Implementation
Togglz Library included as its own module via a spring-boot-starter
- imported into all other modules
Togglz console enabled 
- allowing for real-time control
- TODO: flesh out security controls
Feature toggles are defined in an enum, controlled in application.yaml

#### Documentation
The `IWXFeatures` enum will be used to track existing toggles.
The standard we want to adopt on this file is to include a `@Label` annotation for each defined feature toggle

This label must include:
- the feature toggle type (New Feature, CI, Fix, Experiment)
- the pod implementing the feature toggle
- intended clean up date of the toggle & jira number of clean up ticket


## Resources
- [Togglz Documentation](https://www.togglz.org/)