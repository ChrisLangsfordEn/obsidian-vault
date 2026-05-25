```plantuml
@startuml AIR_Container
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

LAYOUT_WITH_LEGEND()

skinparam linetype polyline

title AIR Container Diagram

' === Tier 1: Actors (leftmost) ===
Person(advisor, "Financial Advisor", "Primary user of the AIR platform")

' === Tier 2: Presentation Layer ===

System_Boundary(airSystem, "AIR Platform") {

    ' === Tier 2: Presentation ===
    Container(spa, "Angular SPA", "Angular 19+, TypeScript", "Single-page application providing advisor workbench, opportunity management, advice case workspace, proposal builder, and dashboards")

    ' === Tier 3/4: API + Service Layer ===
    Container(api, "AIR API (Modular Monolith)", "Java 21, Spring Boot 4.x", "REST API hosting all bounded context modules — workbench, opportunity, advice lifecycle, proposal builder, compliance rules, engagement, document & acceptance, advisor productivity")

    ' === Tier 4: AI Agent Services ===
    Container(bob, "Bob Agent", "Spring AI", "Advisor's personal assistant — cross-context agent providing queue reasoning, proposal drafting, engagement prep, notebook interpretation, and general advisory support")
    Container(vera, "Vera Agent", "Spring AI + Rules Engine", "Compliance agent — validates proposals (sync), enforces suitability checks, flags risks")
    Container(gary, "Gary Agent", "Spring AI", "AI professional coach agent — behavioural analysis and coaching insights")

    ' === Tier 5: Data Layer (rightmost) ===
    Container(eventBus, "Event Bus", "Queue", "Event backbone for cross-module communication, agent orchestration, and workbench projection updates")
    ContainerDb(db, "PostgreSQL Database", "AWS RDS PostgreSQL 15+", "Stores opportunities, advice cases, proposals, compliance artefacts, engagement records, notebook, audit trail")
}

' === Tier 5: External Data/Services (rightmost) ===
System_Ext(entraId, "Microsoft Entra ID", "Identity & access management")
System_Ext(pep, "AdviceConsumer (via PEP)", "Enterprise API Gateway — lead sync & deal execution")

' === Relationships: Tier 1 -> Tier 2 ===
Rel(advisor, spa, "Accesses application", "HTTPS")

' === Relationships: Tier 2 -> Tier 3/4 ===
Rel(spa, api, "Calls REST endpoints", "HTTPS/JSON + Bearer JWT")
Rel(spa, entraId, "Authenticates via MSAL", "OAuth2/PKCE")

' === Relationships: Tier 3/4 -> Tier 5 ===
Rel(api, eventBus, "Publishes/subscribes domain events", "In-process")
Rel(api, db, "Reads/writes domain data", "JDBC/SQL")
Rel(api, pep, "Syncs leads, executes deals", "REST/JSON via PEP + Circuit Breaker")
Rel(api, entraId, "Validates JWT tokens", "JWKS")

' === Relationships: Event Bus -> Agents ===
Rel(eventBus, bob, "Routes events", "AdviceCaseCreated, ProposalSectionEdited, LeadSignalDetected, EngagementSummaryAvailable")
Rel(eventBus, vera, "Routes events", "ProposalCompleted (sync validation gate)")
Rel(eventBus, gary, "Routes events", "TBC")

' === Relationships: Agents -> API ===
Rel(bob, api, "Produces suggestions/drafts, reads from all context modules", "In-process")
Rel(vera, api, "Returns validations/violations", "In-process")
Rel(gary, api, "Produces professional coaching suggestions", "In-process")

@enduml
```