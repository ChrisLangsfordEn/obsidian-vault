```plantuml
@startuml AIR_API_Components
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

LAYOUT_WITH_LEGEND()
LAYOUT_LEFT_RIGHT()

skinparam linetype polyline

title AIR API — Modular Monolith Component Diagram

Container_Boundary(api, "AIR API (Modular Monolith)") {

    ' === Cross-cutting services ===
    together {
        Component(featureToggles, "Feature Toggle Service", "Spring Boot + Config", "Controls feature flags to decouple deployments from business releases")
    }

    ' === Core Domain Modules ===
    Component(workbenchModule, "Advisor Workbench Module", "Spring Boot Module", "Priority queue presentation, performance scorecard, aggregated read projection for Bob's context window, advisor day schedule")
    Component(opportunityModule, "Opportunity & Portfolio Module", "Spring Boot Module", "Manages leads, opportunities, scoring, pipeline classification, feedback, and the Next-Best-Action Prioritisation Service")
    Component(clientModule, "Client Context Module (Customer 360)", "Spring Boot Module", "Read-only consolidated view of client profiles, financial position, money flows, risk profiles, FNA inputs")
    Component(lifecycleModule, "Advice Case Lifecycle Module", "Spring Boot Module", "Orchestrates advice case stages, owns the Opportunity Setup wizard, manages stage transitions and outcome anchors")
    Component(proposalModule, "Advice Construction Module (Proposal Builder)", "Spring Boot Module", "Builds and versions proposals and Record of Advice — sections, calculations, diffs, template progression")
    Component(rulesModule, "Compliance Rules Module", "Spring Boot Module", "Synchronous validation — suitability checks, mandate validation, risk profile mismatch, FICA gaps, disclosure requirements")

    ' === Supporting Domain Modules ===
    Component(engagementModule, "Client Engagement Module", "Spring Boot Module", "Captures structured client interactions — recordings, transcripts, summaries, objectives, consent")
    Component(documentModule, "Document & Acceptance Module", "Spring Boot Module", "Generates final artefacts (PDFs, RoA), tracks client acceptance, manages compliance artefact versioning")
    Component(productivityModule, "Advisor Productivity Module", "Spring Boot Module", "Notebook, voice dictation, photo attachments, saved notes library, Bob interpretation requests")

    ' === Event Bus ===
    Component(eventBus, "Event Bus", "Spring Application Events", "In-process event backbone for cross-module communication and agent orchestration")
}

' === External Systems ===
together {
    ContainerDb(db, "PostgreSQL", "AWS RDS", "Each module owns its schema/tables via Liquibase")
    System_Ext(pep, "AdviceConsumer (via PEP)", "Enterprise API Gateway — lead sync & deal execution")
    System_Ext(clientSystems, "Client Data Sources", "Customer 360 data")
}

' === Relationships: Modules -> Event Bus ===
Rel(opportunityModule, eventBus, "Publishes LeadSignalDetected, LeadPromotedToQueue, LeadFeedbackRecorded, OpportunityReadyForEngagement, QueueRanked")
Rel(lifecycleModule, eventBus, "Publishes AdviceCaseCreated, StageAdvanced, CaseCompleted")
Rel(proposalModule, eventBus, "Publishes ProposalSectionEdited, ProposalCompleted")
Rel(engagementModule, eventBus, "Publishes EngagementCaptured, EngagementSummaryAvailable")
Rel(documentModule, eventBus, "Publishes ClientAccepted, DocumentGenerated")
Rel(rulesModule, eventBus, "Publishes ValidationCompleted")

' === Relationships: Event Bus -> Consuming Modules ===
Rel(eventBus, workbenchModule, "Subscribes to queue/lifecycle/ranking events for projection updates")
Rel(eventBus, lifecycleModule, "Subscribes to OpportunityReadyForEngagement, ValidationCompleted, ClientAccepted")
Rel(eventBus, rulesModule, "Subscribes to ProposalCompleted (sync validation gate)")

' === Relationships: Module -> Module (direct reads) ===
Rel(workbenchModule, opportunityModule, "Reads ranked queue from Prioritisation Service")
Rel(workbenchModule, lifecycleModule, "Reads active engagements and stage positions")
Rel(workbenchModule, clientModule, "Reads client metadata for display")
Rel(lifecycleModule, clientModule, "Reads FNA data for Setup wizard step 4")
Rel(proposalModule, clientModule, "Reads client financials for proposals")
Rel(opportunityModule, clientModule, "Receives behavioural signals")

' === Relationships: Modules -> External Services ===
Rel(opportunityModule, pep, "Syncs leads", "REST/JSON via PEP")
Rel(lifecycleModule, pep, "Executes fulfilment steps", "REST/JSON via PEP")
Rel(clientModule, clientSystems, "Retrieves client data", "REST/JSON")

' === Relationships: Modules -> Database ===
Rel(workbenchModule, db, "Reads/writes", "JDBC")
Rel(opportunityModule, db, "Reads/writes", "JDBC")
Rel(lifecycleModule, db, "Reads/writes", "JDBC")
Rel(proposalModule, db, "Reads/writes", "JDBC")
Rel(rulesModule, db, "Reads/writes", "JDBC")
Rel(engagementModule, db, "Reads/writes", "JDBC")
Rel(documentModule, db, "Reads/writes", "JDBC")
Rel(productivityModule, db, "Reads/writes", "JDBC")

@enduml
```
