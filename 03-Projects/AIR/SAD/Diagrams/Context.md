```plantuml
@startuml AIR_System_Context
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

LAYOUT_WITH_LEGEND()
LAYOUT_LEFT_RIGHT()

title AIR System Context Diagram

' === Tier 1: Actors (leftmost) ===
Person(advisor, "Financial Advisor", "Uses AIR to manage opportunities, build proposals, and deliver advice to clients")
Person(supervisor, "Supervisor", "Reviews and approves advice cases, monitors advisor activity")
Person(admin, "Administrator", "Manages system configuration, user access, and operations")

' === Tier 2/3/4: Core System ===
System(air, "AIR Platform", "Advice & Intelligence Relationship platform — modular monolith providing opportunity management, advice construction, compliance validation, and AI-assisted advisory support")

' === Tier 5: External Systems (rightmost) ===
System_Ext(entraId, "Microsoft Entra ID", "Corporate identity provider — OAuth2/OIDC authentication and role-based access")
System_Ext(pep, "AdviceConsumer (via PEP)", "Enterprise API Gateway (Policy Enforcement Point) — source of leads and fulfilment execution")
System_Ext(clientSystems, "Client Data Sources", "Upstream systems providing Customer 360 data (financial position, risk profile, relationships)")

' === Relationships: Actors -> System ===
Rel(advisor, air, "Manages opportunities, builds proposals, engages clients", "HTTPS/Browser")
Rel(supervisor, air, "Reviews cases, approves advice, monitors compliance", "HTTPS/Browser")
Rel(admin, air, "Configures system, manages users", "HTTPS/Browser")

' === Relationships: System -> External ===
Rel(air, entraId, "Authenticates users, validates JWT tokens", "OAuth2/PKCE")
Rel(air, pep, "Syncs leads, executes deals", "REST/JSON via PEP")
Rel(air, clientSystems, "Retrieves client profiles and financial data", "REST/JSON")

@enduml
```