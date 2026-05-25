```plantuml
@startuml AIR_API_Components
' Include standard AWS library resources
!include <awslib/AWSCommon>
!include <awslib/Compute/EC2>
!include <awslib/Database/RDS>
!include <awslib/NetworkingContentDelivery/ElasticLoadBalancing>
'!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

LAYOUT_WITH_LEGEND()

skinparam linetype polyline

title AIR API — Modular Monolith Component Diagram

' === Cross-cutting (top, outside boundary) ===
Component_Ext(featureToggles, "Feature Toggle Service", "Spring Boot + Config", "Controls feature flags")
Component_Ext(eventBus, "Event Bus", "Spring Application Events", "In-process event backbone for cross-module communication and agent orchestration")

Container_Boundary(api, "AIR API (Modular Monolith)") {

    ' === Core Domain Modules ===
    Boundary(core, "Core Domain") {
        Component(workbenchModule,   "Advisor Workbench",         "Spring Boot Module", "Priority queue, scorecard, aggregated read projection")
        Component(opportunityModule, "Opportunity & Portfolio",   "Spring Boot Module", "Leads, scoring, pipeline, Next-Best-Action Prioritisation")
        Component(clientModule,      "Client Context (Cust. 360)","Spring Boot Module", "Read-only consolidated client profile, FNA inputs")
        Component(lifecycleModule,   "Advice Case Lifecycle",     "Spring Boot Module", "Case stages, Setup wizard, transitions, outcome anchors")
        Component(proposalModule,    "Advice Construction",       "Spring Boot Module", "Proposals, RoA, versioning, diffs, template progression")
        Component(rulesModule,       "Compliance Rules",          "Spring Boot Module", "Suitability checks, mandate validation, FICA gaps")
    }

    ' === Supporting Domain Modules ===
    Boundary(supporting, "Supporting Domain") {
        Component(engagementModule,  "Client Engagement",         "Spring Boot Module", "Structured client interactions — recordings, transcripts, consent")
        Component(documentModule,    "Document & Acceptance",     "Spring Boot Module", "PDF/RoA generation, client acceptance, artefact versioning")
        Component(productivityModule,"Advisor Productivity",      "Spring Boot Module", "Notebook, voice dictation, photo attachments, Bob requests")
    }
}

' === External Systems ===
ContainerDb(db,          "PostgreSQL",              "AWS RDS",  "Each module owns its schema via Liquibase")
System_Ext(pep,          "AdviceConsumer (via PEP)", "Enterprise API Gateway — lead sync & deal execution")
System_Ext(clientSystems,"Client Data Sources",     "Customer 360 data")

' ── Event Publishing (Modules → Bus) ──────────────────────────────────────
Rel_Up(opportunityModule, eventBus, "LeadSignalDetected, LeadPromotedToQueue,\nOpportunityReadyForEngagement, QueueRanked")
Rel_Up(lifecycleModule,   eventBus, "AdviceCaseCreated, StageAdvanced, CaseCompleted")
Rel_Up(proposalModule,    eventBus, "ProposalSectionEdited, ProposalCompleted")
Rel_Up(engagementModule,  eventBus, "EngagementCaptured, EngagementSummaryAvailable")
Rel_Up(documentModule,    eventBus, "ClientAccepted, DocumentGenerated")
Rel_Up(rulesModule,       eventBus, "ValidationCompleted")

' ── Event Consumption (Bus → Modules) ─────────────────────────────────────
Rel_Down(eventBus, workbenchModule, "Queue/lifecycle/ranking → projection updates")
Rel_Down(eventBus, lifecycleModule, "OpportunityReadyForEngagement,\nValidationCompleted, ClientAccepted")
Rel_Down(eventBus, rulesModule,     "ProposalCompleted (sync validation gate)")

' ── Direct Module Reads ────────────────────────────────────────────────────
Rel(workbenchModule,  opportunityModule, "Reads ranked queue")
Rel(workbenchModule,  lifecycleModule,   "Reads active cases & stage positions")
Rel(workbenchModule,  clientModule,      "Reads client metadata")
Rel(lifecycleModule,  clientModule,      "Reads FNA data (Setup wizard step 4)")
Rel(proposalModule,   clientModule,      "Reads client financials")
Rel(opportunityModule,clientModule,      "Receives behavioural signals")

' ── External Integrations ──────────────────────────────────────────────────
Rel(opportunityModule, pep,          "Syncs leads",             "REST/JSON via PEP")
Rel(lifecycleModule,   pep,          "Executes fulfilment steps","REST/JSON via PEP")
Rel(clientModule,      clientSystems,"Retrieves client data",   "REST/JSON")

' ── Database (one arrow per group, not per module) ────────────────────────
Rel_Down(core,      db, "All core modules read/write", "JDBC")
Rel_Down(supporting,db, "All supporting modules read/write", "JDBC")

@enduml
```
