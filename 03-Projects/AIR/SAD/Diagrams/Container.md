

```plantuml
@startuml AIR_Container
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

LAYOUT_WITH_LEGEND()

skinparam linetype polyline

title AIR Container Diagram

' === Actor ===
Person(advisor, "Financial Advisor", "Primary user of the AIR platform")

' === External Identity (top — touches both SPA and API) ===
System_Ext(entraId, "Microsoft Entra ID", "Corporate identity — OAuth2/OIDC, JWKS")

System_Boundary(airSystem, "AIR Platform") {

    ' === Presentation ===
    Container(spa, "Angular SPA", "Angular 19+, TypeScript", "Advisor workbench, opportunity management, advice workspace, proposal builder")

    ' === API Layer ===
    Container(api, "AIR API (Modular Monolith)", "Java 21, Spring Boot 4.x", "REST API — all bounded context modules: workbench, opportunity, lifecycle, proposal, compliance, engagement, documents, productivity")

    ' === AI Agents ===
    Boundary(agentLayer, "AI Agents (In-Process)") {
        Container(bob,  "Bob",  "Spring AI",                  "Personal assistant — queue reasoning, proposal drafting, engagement prep, notebook interpretation")
        Container(vera, "Vera", "Spring AI + Rules Engine",   "Compliance agent — sync proposal validation, suitability checks, risk flagging")
        Container(gary, "Gary", "Spring AI (Deferred)",       "Professional coach — behavioural analysis and coaching insights")
    }

    ' === Event Bus ===
    Container(eventBus, "Event Bus", "Spring Application Events", "In-process backbone — cross-module events and agent orchestration")

    ' === Data ===
    ContainerDb(db, "PostgreSQL", "AWS RDS PostgreSQL 15+", "Opportunities, advice cases, proposals, compliance artefacts, engagement records, notebook, audit trail")
}

' === External Integrations ===
System_Ext(pep, "AdviceConsumer (via PEP)", "Enterprise API Gateway — lead sync & deal execution")

' ── User → Presentation ───────────────────────────────────────────────────
Rel(advisor, spa,     "Accesses application",       "HTTPS")

' ── Presentation → API & Identity ────────────────────────────────────────
Rel(spa, api,     "Calls REST endpoints",        "HTTPS/JSON + Bearer JWT")
Rel(spa, entraId, "Authenticates via MSAL",      "OAuth2/PKCE")

' ── API → Identity & External ────────────────────────────────────────────
Rel(api, entraId, "Validates JWT tokens",         "JWKS")
Rel(api, pep,     "Syncs leads, executes deals",  "REST/JSON + Circuit Breaker")

' ── API ↔ Infrastructure ──────────────────────────────────────────────────
Rel(api, eventBus, "Publishes/subscribes domain events", "In-process")
Rel(api, db,       "Reads/writes domain data",           "JDBC/SQL")

' ── Event Bus → Agents ────────────────────────────────────────────────────
Rel_Down(eventBus, bob,  "AdviceCaseCreated, ProposalSectionEdited,\nLeadSignalDetected, EngagementSummaryAvailable")
Rel_Down(eventBus, vera, "ProposalCompleted (sync validation gate)")
Rel_Down(eventBus, gary, "TBC (deferred)")

' ── Agents → API ──────────────────────────────────────────────────────────
Rel_Up(bob,  api, "Produces suggestions & drafts, reads all modules",  "In-process")
Rel_Up(vera, api, "Returns validations & violations",                  "In-process")
Rel_Up(gary, api, "Produces coaching suggestions (deferred)",          "In-process")

@enduml
```