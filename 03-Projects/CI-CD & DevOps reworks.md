## Monitoring
- Graylog TTL
- Pre-prod TTL

## Artifact Management
- Developers pull dependencies from Nexus
- Publish Jars to Nexus


## Dev Team
- CI/CD
- BUY IN! (idea POD4 CI - release branch always in a releasable state)
	- Automated tests (unit, integration & e2e)
	- Decouple merges from releasing changes
		- Feature toggles
		- Abstraction
		- Keystone
	- Workshop with Pod 2?

What does a new feature look like in IWX?
- New process / version
- New dmn / version
- New java delegates

Challenges: old processes will call the same class, so if a class changes, so does the old process.

New process: Merge queue & develop branch.

Promote to INT - merge to INT branch and deploy
Promote to QA - merge to QA branch and deploy
Promote to PROD - merge to PROD/release branch and deploy

CI Proposal for Yash:
- Branching strategy (Gitflow or similar)
- Merge queue & review checklist for change type
	- feature
	- bug
	- hotfix
	- chore
- Toggle standards
	- naming convention standards
	- lifespan standards
	- clean up practices
- Change Management: IWX pod senior buy in (Yash, Sri, Vishal, Satish)

Picton -41.28497620514816, 174.0053168567684
Wellington -41.27938032281053, 174.78320198551316