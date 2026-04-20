# Mock Onboarding Project Brief
## Event-Driven Life Insurance Claims Platform

**Audience:** Technical Lead (Very Senior)

**Purpose of this Brief**  
This mock project simulates a realistic slice of an event-driven life insurance claims platform. It is designed to accelerate your ability to:
- Build accurate mental models of the domain and system
- Reason about event-driven workflows and failure modes
- Establish technical direction and quality standards
- Ask high-leverage questions of product, architecture, and peers

The brief is intentionally incomplete in places. Ambiguity is a feature, not a bug. Your effectiveness will be judged not only on what you build, but on the clarity of assumptions you surface and the questions you ask.

---

## Technical & Domain Context

**Architecture**  
- Event-driven microservices  
- Kafka as the primary integration mechanism  
- Asynchronous, eventually consistent workflows  

**Technology Stack**  
- Java 21  
- Spring Boot 3.x  
- Kafka (Java clients, Spring for Kafka)  

**Domain**  
- Industry: Life Insurance  
- Core concepts: Policy, Claim, Insured Life  
- Claims are initiated after a life event (e.g., death of the insured life) and progress through multiple validation and assessment stages before settlement or rejection.

---

## Narrative Scenario

The organization is modernizing its claims platform to improve scalability, auditability, and time-to-settlement. Historically, claims were processed synchronously via a monolithic system. The new target architecture decomposes claims handling into event-driven microservices, each owning a clearly bounded responsibility.

You have joined as a **Technical Lead** to help shape this new platform while delivering incremental business value.

Your initial mandate is to design and implement a thin but realistic vertical slice of the claims lifecycle that demonstrates:
- Correct domain modeling  
- Pragmatic event design  
- Operational awareness (observability, failure handling)  
- Clear technical leadership choices  

---

# Milestone 1 (Week 1): Domain & Event Landscape Definition

### Objective
Establish a shared understanding of the claims domain and define an initial event model that can support downstream services.

### Skills & Concepts Being Developed
- Event-driven domain modeling  
- Translating business processes into events  
- Identifying bounded contexts and service responsibilities  
- Making explicit assumptions under uncertainty  

### Scope
Focus on the **early lifecycle of a claim**, from notification of death to claim registration.

You are **not** expected to implement full business logic at this stage. This milestone is about shaping the problem space.

### Intentional Gaps
- The exact legal/regulatory requirements for claims registration are unspecified  
- The definition of what constitutes a “valid” policy is incomplete  
- The source of truth for insured life data is unclear  

### Deliverables
- A concise domain overview (1–2 pages) covering:
  - Key entities (Policy, Claim, Insured Life)
  - High-level lifecycle states for a Claim
- A proposed set of domain events (names, intent, key fields)
- A high-level service landscape diagram (textual or visual)

### Review / Checkpoint Criteria
- Are domain terms used consistently and accurately?
- Do events represent facts, not commands?
- Is ownership of data and behavior clear per service?
- Are assumptions explicitly documented?

### Reflection Prompts
- Where did I feel tempted to over-specify too early?
- Which ambiguities felt risky vs. acceptable?
- How would this model change under regulatory pressure?

### Questions to Clarify with the Product Owner
- What officially triggers a claim: notification, documentation, or internal validation?
- Can multiple claims exist for a single policy?
- Are claims ever registered retroactively?

---

# Milestone 2 (Week 2): Implement Claim Registration Flow

### Objective
Implement a minimal but production-shaped claim registration flow using Kafka and Spring Boot.

### Skills & Concepts Being Developed
- Kafka topic and event design  
- Idempotency and duplicate event handling  
- Transaction boundaries in event-driven systems  
- Pragmatic use of Spring Kafka  

### Scope
Implement a **Claim Registration Service** that:
- Consumes a `DeathNotified` (or equivalent) event
- Validates basic policy eligibility
- Emits a `ClaimRegistered` or `ClaimRejected` event

Downstream consumers may be mocked or stubbed.

### Intentional Gaps
- Policy validation rules are partially undefined  
- Error handling expectations are not specified  
- Event schema evolution strategy is unspecified  

### Deliverables
- A Spring Boot microservice with:
  - Kafka consumer(s) and producer(s)
  - Clear package/module structure
- Event schemas with versioning strategy documented
- A short README explaining design decisions

### Review / Checkpoint Criteria
- Are Kafka topics named and partitioned sensibly?
- Is the service resilient to duplicate or out-of-order events?
- Are failures observable and diagnosable?
- Does the code reflect leadership-level quality?

### Reflection Prompts
- What trade-offs did I make between correctness and speed?
- Where would I invest more if this went to production?
- How easy would this be for another team to integrate with?

### Questions to Clarify with the Product Owner
- What are acceptable reasons to reject a claim at registration?
- Should reprocessing be possible after rejection?
- How quickly must downstream systems react to registration?

---

# Milestone 3 (Weeks 3–4): Operational & Leadership Concerns

### Objective
Evolve the solution to reflect real-world operational and leadership responsibilities.

### Skills & Concepts Being Developed
- Observability in event-driven systems  
- Failure modes and recovery strategies  
- Technical decision documentation  
- Setting patterns and standards for teams  

### Scope
Enhance the existing flow to address non-functional concerns:
- Logging, metrics, and tracing
- Retry and dead-letter handling
- Basic security or data privacy considerations

### Intentional Gaps
- No explicit SLOs are provided  
- Compliance requirements are high-level only  
- Team ownership boundaries are implied, not stated  

### Deliverables
- Enhancements to the service addressing operational readiness
- A short **Technical Decision Record (TDR)** covering:
  - Event design choices
  - Error handling strategy
  - Observability approach
- A brief note on recommended team standards or patterns

### Review / Checkpoint Criteria
- Can failures be detected and explained quickly?
- Are decisions documented in a way others can follow?
- Does the solution scale conceptually across teams?
- Does this reflect the mindset of a technical lead?

### Reflection Prompts
- What would break first under load or change?
- Where does human process matter more than technology?
- How would I onboard another senior engineer into this system?

### Questions to Clarify with the Product Owner
- What operational incidents are most costly to the business?
- Which events are legally or financially sensitive?
- What audit capabilities are required for claims?

---

## Final Outcome

At the end of this project, you should have:
- A credible vertical slice of an event-driven claims platform
- A documented set of assumptions and decisions
- A clear picture of domain risks and technical trade-offs
- Demonstrated leadership in shaping both code and conversation

Success is measured less by feature completeness and more by the quality of reasoning, questions, and technical direction established.
