# Onboarding Project Brief  
**Project Name:** New Business Policy Issuance – Workflow Enablement  
**Audience:** Intermediate Software Engineer (Onboarding Assignment)  
**Duration:** 4 Weeks  
**Primary Focus:** Productive contribution to a Camunda-based Spring Boot system

---

## 1. Background & Business Context

The organisation operates in the **Life Insurance** domain, with a strong emphasis on **business process automation and orchestration**. Core value streams include **Sales**, **Underwriting**, **Policy Issuance**, **Policy Servicing**, and **Claims**.

This onboarding project simulates a **real production change request**: enabling a simplified **New Business Policy Issuance** workflow that orchestrates interactions between sales intake, underwriting checks, and policy activation.

The objective is not to complete a “toy” exercise, but to immerse you in:
- How workflows are modeled, deployed, and evolved  
- How Spring Boot services interact with Camunda  
- How architectural boundaries are respected in a multi-module codebase  
- How engineers reason about ambiguity and clarify requirements early  

You are expected to work **independently**, raise questions proactively, and justify design decisions.

---

## 2. Technical & Architectural Context

### Technology Stack
- Java 17  
- Spring Boot 3  
- Camunda 7 (Embedded Engine)  
- H2 In-Memory Database  
- Maven (Multi-module)  

### Application Structure
- **core** – Runnable Spring Boot application. Hosts Camunda engine, process configuration, and application bootstrap.  
- **common** – Shared Spring components (e.g. clients, configuration, shared services).  
- **{feature}-api** – Feature-specific module for business functionality related to *New Business Policy Issuance*.  
- **util** – Non-Spring utility classes (pure helpers, mappers, constants).  

### Camunda Configuration
- BPMN models and related configs are **not stored in this repository**.  
- They live in a **separate Camunda configuration repository**.  
- Deployment occurs via **Camunda Modeler** or **Camunda REST API**.  

You will need to reason about **cross-repository ownership**, versioning, and deployment boundaries.

---

## 3. Project Narrative

A new digital sales channel is generating **life insurance applications**.  
Once an application is captured, a **New Business Policy Issuance** process must:

1. Register the sales application  
2. Perform underwriting checks  
3. Decide whether the policy can be issued automatically 
4. Activate the policy or route it for manual review  

This onboarding assignment focuses on **enabling the backend workflow integration**, not building UIs.

---

# Milestones

## Milestone 1 (Week 1): Domain & Workflow Orientation

### Objective
Build a mental model of the **business process**, **system boundaries**, and **Camunda’s role** in orchestration.

### Skills & Concepts Developed
- Life insurance domain fundamentals  
- BPMN literacy (events, service tasks, gateways)  
- Camunda engine responsibilities vs application logic  
- Reading and navigating a multi-module Spring Boot project  
- Mapping .NET workflow concepts to Camunda  

### Scope & Intentional Gaps
You are given a **high-level BPMN diagram** (conceptual, not executable).  
You are *not* given exact service boundaries, data contracts, or error-handling expectations.

### Deliverables
- Written workflow walkthrough (1–2 pages)  
- Module responsibility map  

### Review / Checkpoint Criteria
- Clear business explanation of workflow  
- Correct separation of concerns  
- Explicit assumptions  

### Reflection Prompts
- What feels business-owned vs engineering-owned?  
- Where does orchestration add value?  

---

## Milestone 2 (Week 2): Executable Workflow Integration

### Objective
Integrate an executable BPMN process with Spring Boot.

### Skills & Concepts Developed
- Java delegates  
- Process variables  
- REST integration assumptions  

### Deliverables
- Executable BPMN  
- Java delegate implementations  
- README explaining process triggering  

### Review / Checkpoint Criteria
- Executable, readable BPMN  
- Testable delegates  

### Reflection Prompts
- BPMN vs Java responsibility split?  


---

## Milestone 3 (Week 3): Error Handling, Evolution & Testing

### Objective
Handle failure scenarios and test process behaviour.

### Skills & Concepts Developed
- BPMN error events  
- Process testing  
- Designing for change  

### Deliverables
- Updated BPMN with error paths  
- Tests for happy and failure paths  

### Review / Checkpoint Criteria
- Explicit error modelling  
- Behaviour-focused tests  

### Reflection Prompts
- BPMN impact on error handling?  


---

## Milestone 4 (Week 4): Production Readiness & Knowledge Transfer

### Objective
Demonstrate production readiness and explain design decisions.

### Skills & Concepts Developed
- Deployment reasoning  
- Documentation  
- Knowledge transfer  

### Deliverables
- Final workflow and code  
- Handover document  
- Self-review  

### Review / Checkpoint Criteria
- Extendable by another engineer  
- Risks and assumptions visible  

### Reflection Prompts
- What would you do differently?

---

## Closing Note

This onboarding project is **not about perfection**.  
It is about demonstrating how you think, learn, and communicate in a real delivery environment.
