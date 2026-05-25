

```plantuml
@startuml AIR_AI_Agents
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

LAYOUT_WITH_LEGEND()

skinparam linetype polyline

title AI Agents & Orchestration — Component Diagram

' === Event Bus (top-level, acts as the bridge) ===
Component_Ext(eventBus, "Event Bus", "Spring Application Events", "Routes domain events to agent subscriptions")

Container_Boundary(api, "AIR API (Modular Monolith)") {

    ' === Core Domain Modules ===
    Boundary(core, "Core Domain") {
        Component(workbenchModule,   "Advisor Workbench",       "Spring Boot Module", "Queue presentation, aggregated read projection")
        Component(opportunityModule, "Opportunity & Portfolio", "Spring Boot Module", "Leads, scoring, prioritisation service")
        Component(clientModule,      "Client Context",          "Spring Boot Module", "Customer 360 read model")
        Component(lifecycleModule,   "Advice Case Lifecycle",   "Spring Boot Module", "Case stages, setup wizard, transitions")
        Component(proposalModule,    "Advice Construction",     "Spring Boot Module", "Proposals, RoA, versioning")
        Component(rulesModule,       "Compliance Rules",        "Spring Boot Module", "Deterministic rule enforcement")
    }

    ' === Supporting Domain Modules ===
    Boundary(supporting, "Supporting Domain") {
        Component(engagementModule,  "Client Engagement",       "Spring Boot Module", "Client interaction capture")
        Component(documentModule,    "Document & Acceptance",   "Spring Boot Module", "Document generation, client acceptance")
        Component(productivityModule,"Advisor Productivity",    "Spring Boot Module", "Notebook, dictation, notes library")
    }

    ' === AI Agent Layer ===
    Boundary(agents, "AI Agent Layer") {
        Component(orchestrator, "Advice Lifecycle Orchestrator", "Spring Service",             "Tracks lifecycle state, enforces policy gates, triggers validations")
        Component(bob,          "Bob — Personal Assistant",      "Spring AI Agent",            "Cross-context agent: proposal drafting, queue reasoning, engagement prep, notebook interpretation")
        Component(vera,         "Vera — Compliance Agent",       "Spring AI + Rules Engine",   "Sync proposal validation, suitability enforcement, risk flagging")
    }
}

' ── Event Publishing (Core Modules → Bus) ─────────────────────────────────
Rel_Up(opportunityModule, eventBus, "LeadSignalDetected, LeadPromotedToQueue,\nOpportunityReadyForEngagement, QueueRanked")
Rel_Up(proposalModule,    eventBus, "ProposalSectionEdited, ProposalCompleted")
Rel_Up(lifecycleModule,   eventBus, "AdviceCaseCreated, StageAdvanced, CaseCompleted")
Rel_Up(engagementModule,  eventBus, "EngagementCaptured, EngagementSummaryAvailable")
Rel_Up(documentModule,    eventBus, "ClientAccepted, DocumentGenerated")

' ── Event Consumption (Bus → Agents) ──────────────────────────────────────
Rel_Down(eventBus, bob,  "AdviceCaseCreated, ProposalSectionEdited,\nEngagementSummaryAvailable, LeadSignalDetected", "Async")

' ── Orchestrator (policy gate chain) ──────────────────────────────────────
Rel(orchestrator, lifecycleModule, "Manages state transitions, enforces gates")
Rel(orchestrator, vera,            "ValidateProposal, CheckSuitability", "Sync policy gate")
Rel(vera,         rulesModule,     "Executes rule validations")

' ── Bob Reads (grouped by boundary) ───────────────────────────────────────
Rel(bob, core,      "Reads leads, cases, stages, client profiles,\ncompliance requirements for context window")
Rel(bob, supporting,"Reads engagement history, templates,\nnotebook content for interpretation")

' ── Bob Writes (explicit — these are the meaningful ones) ─────────────────
Rel(bob, proposalModule,    "Produces proposal suggestions & drafts",  "Write")
Rel(bob, workbenchModule,   "Surfaces queue suggestions",              "Write")
Rel(bob, opportunityModule, "Surfaces new leads",                      "Write")

@enduml
```