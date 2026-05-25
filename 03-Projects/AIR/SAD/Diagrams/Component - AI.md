

```plantuml
@startuml AIR_AI_Agents
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

LAYOUT_WITH_LEGEND()


skinparam linetype polyline

title AI Agents & Orchestration — Component Diagram

' === Tier 4: Domain Modules (leftmost — they produce events and serve data) ===
Container_Boundary(modules, "Domain Modules") {
    Component(workbenchModule, "Advisor Workbench Module", "Spring Boot Module", "Aggregated read projection, queue presentation")
    Component(opportunityModule, "Opportunity & Portfolio Module", "Spring Boot Module", "Leads, scoring, prioritisation service")
    Component(proposalModule, "Proposal Builder Module", "Spring Boot Module", "Builds proposals and RoA")
    Component(lifecycleModule, "Advice Lifecycle Module", "Spring Boot Module", "Case lifecycle, setup wizard, stage transitions")
    Component(engagementModule, "Engagement Module", "Spring Boot Module", "Client interaction capture")
    Component(clientModule, "Client Context Module", "Spring Boot Module", "Customer 360 read model")
    Component(rulesModule, "Compliance Rules Module", "Rules Engine Module", "Deterministic rule enforcement")
    Component(documentModule, "Document & Acceptance Module", "Spring Boot Module", "Document generation, client acceptance")
    Component(productivityModule, "Advisor Productivity Module", "Spring Boot Module", "Notebook, dictation, notes library")
}

Component_Ext(eventBus, "Event Bus", "Queue", "Routes domain events to agent subscriptions")

' === Agent Layer ===
Container_Boundary(agents, "AI Agent Layer") {

    Component(orchestrator, "Advice Lifecycle Orchestrator", "Spring Service", "Tracks lifecycle state, triggers validations, ensures required steps are completed, enforces policy gates")
    Component(bob, "Bob — Advisor's Personal Assistant", "Spring AI Agent", "Application-layer agent: drafts proposals, reasons about queue, preps engagements, interprets notes, provides general advisory support. Cross-context read access.")
    Component(vera, "Vera — Compliance Agent", "Spring AI Agent + Rules Engine", "Domain service: validates proposals (sync), enforces suitability, flags risks. Deterministic, guardrail-focused.")
}

' === Relationships: Domain Modules -> Event Bus ===
Rel(opportunityModule, eventBus, "LeadSignalDetected, LeadPromotedToQueue, QueueRanked, OpportunityReadyForEngagement")
Rel(proposalModule, eventBus, "ProposalSectionEdited, ProposalCompleted")
Rel(lifecycleModule, eventBus, "AdviceCaseCreated, StageAdvanced, CaseCompleted")
Rel(engagementModule, eventBus, "EngagementCaptured, EngagementSummaryAvailable")
Rel(documentModule, eventBus, "ClientAccepted, DocumentGenerated")

' === Relationships: Event Bus -> Agents ===
Rel(eventBus, bob, "AdviceCaseCreated, ProposalSectionEdited, EngagementSummaryAvailable, LeadSignalDetected", "Async")

' === Relationships: Orchestrator -> Services ===
Rel(orchestrator, vera, "ValidateProposal, CheckSuitability (policy gate)", "Sync")
Rel(orchestrator, lifecycleModule, "Manages state transitions, enforces gates")

' === Relationships: Bob -> Domain Modules (reads) ===
Rel(bob, workbenchModule, "Reads aggregated projection for context window")
Rel(bob, opportunityModule, "Reads leads, scores, signals")
Rel_Left(bob, lifecycleModule, "Reads active cases, current stages")
Rel(bob, clientModule, "Reads client profiles, financials")
Rel(bob, engagementModule, "Reads past engagement sessions")
Rel(bob, rulesModule, "Reads compliance requirements for pre-emptive suggestions")
Rel(bob, documentModule, "Reads template requirements")
Rel(bob, productivityModule, "Reads notebook content for interpretation")

' === Relationships: Bob -> Domain Modules (writes) ===
Rel(bob, proposalModule, "Produces suggestions & drafts", "Write")
Rel(bob, workbenchModule, "Produces queue suggestions", "Write")
Rel(bob, opportunityModule, "Surfaces new leads", "Write")

' === Relationships: Vera -> Domain Modules ===
Rel(vera, rulesModule, "Executes rule validations")

@enduml
```