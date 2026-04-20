d# Staff Savings – Architecture (Draft v3)

> Updated diagrams converted from Mermaid to **PlantUML C4 syntax** (Context & Container levels).

---

## Business Capabilities & Bounded Contexts

- **Staff Savings Product (Core Domain)**
  - Employer-driven savings benefit; employer configures percentage savings rules per category.
- **Onboarding & Contracting (External)**
  - *Sales Partner* starts the journey and invokes **xContracting** for contract signing (no direct Staff Savings integration).
- **Employer Configuration**
  - Dashboard for employer to configure employees, categories, collections, and payments.
- **Collections Orchestration**
  - Daily batch process generates collection files and drops them to **MFT** for Payment Service.
- **Payments Orchestration**
  - Batch process generates payout files to **MFT** for Payment Service.
- **Notifications**
  - Trigger communications via **Comms Engine**.

**Key Entities:** Employer, Employee, EmployeeCategory, Collection, Payment.

**Triggers/Events:** Daily scheduler triggers collections and payments (offset by T+1/T+2). Fire-and-forget semantics for Payment Service; no error handling once file is dropped.

---

## C4 Level 1 – System Context (PlantUML)

```plantuml
@startuml ContextDiagram
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

Person(employer, "Employer", "Employer uses dashboard to configure employees, categories, collections, and payments")
System(sp, "Sales Partner", "Starts sales journey")
System(xc, "xContracting", "Contract signing via deep links")
System(comms, "Comms Engine", "Sends push notifications and emails")
System(pay, "Payment Service", "Processes collections and payments via MFT")

System_Boundary(ss, "Staff Savings System") {
  System(fe, "Employer Dashboard (Front-end)", "Web UI for employer configuration")
  System(api, "Staff Savings API", "Main backend service")
  System(acct, "Account Service", "Unknown details")
  SystemDb(db, "Relational Database", "Stores domain data")
  SystemDb(cache, "Hazelcast Cache", "Session caching")
}

Rel(employer, fe, "Uses")
Rel(sp, api, "HTTPS/JSON")
Rel(sp, xc, "Deep link")
Rel(fe, api, "HTTPS/JSON")
Rel(api, db, "Reads/Writes")
Rel(api, cache, "Session data")
Rel(api, comms, "REST")
Rel(api, acct, "REST (assumed)")
Rel(api, pay, "MFT file drop")

@enduml
```

---

## C4 Level 2 – Container Diagram (PlantUML)

```plantuml
@startuml ContainerDiagram
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

LAYOUT_WITH_LEGEND()

Person(employee, "Employee", "Mobile user")
Person(employer, "Employer", "Admin user")

System_Boundary(ss, "Staff Scheme") {
  Container(ocep_service, "OCEP Service", "Java / Spring Boot", "User-facing API and aggregation layer. Retrieves data via IPG Data API and exposes endpoints to OCEP front end/mobile.")
  Container(template_service, "Template Service", "TypeScript (Node)", "Generates PDFs and templates on request. Exposes a Template REST API.")
  Container(db, "Staff Scheme DB", "Microsoft SQL Server", "Stores beneficiary cosmetic data (temporary) and other local app state. Production risk data is read from DataDev via IPG.")
  Container(worker, "CSV Watcher", "Shell / Scheduled job", "Checks file-store daily at midnight for new CSV files and kicks off ingestion flow (signals DataDev ingestion process). Not part of DataDev — scheduled by infra.")
}

System(dataGateway, "IPG Data API", "Gateway to production risk/benefit tables (hosted by DataDev)")
System(dataDev, "DataDev", "MS SQL + SSIS", "Staging area, ETL flows and production tables; picks up CSVs and performs validations & upserts.")
System(fileDrop, "Business File Drop", "SFTP or file share (pipe-delimited CSV)")

Rel(employee, ocep_service, "View requests", "HTTPS/JWT")
Rel(employer, ocep_service, "Admin dashboard", "HTTPS")
Rel(ocep_service, dataGateway, "Data requests", "HTTPS/REST")
Rel(ocep_service, template_service, "Generate templates", "HTTPS/REST")
Rel(ocep_service, db, "Read/Write data", "JDBC/SQL")
Rel(worker, fileDrop, "Check for files", "Daily")
Rel(worker, dataDev, "Notify ingestion", "File placement")
Rel(dataDev, dataGateway, "Provides data", "")

Note right of ocep_service : Deployment: single container\ncurrently on old infra;\nmigration planned for September

@enduml
```

---

## Interface Summary (from API perspective)

- **Exposed (HTTPS/JSON):**
  - Employer, Employee, and Category management
  - Configure collection and payment rules
  - Trigger collections/payments runs (manual)
  - Query run status

- **Consumed:**
  - **Payment Service** via **MFT** — batch files for *Collections* and *Payments* (fire-and-forget)
  - **Comms Engine** via REST — notifications/emails
  - **Account Service** — unknown details (presence acknowledged, assumed REST)

---

## Data & Event Flow

1. **Onboarding:** Sales Partner initiates employer onboarding; contracting via xContracting (no direct Staff Savings integration).
2. **Configure:** Employer uses Dashboard to set up employees, categories, and savings rules.
3. **Daily Scheduler:** Picks up due employers for collection run.
4. **Collections:** API generates *Collections* file → dropped to **MFT** → Payment Service collects.
5. **Payments (T+1/T+2):** API generates *Payments* file → dropped to **MFT** → Payment Service pays to savings accounts.
6. **Notify:** API calls Comms Engine for notifications.

---

## Confirmed Assumptions

1. **Front-end App user = Employer**; provides dashboard for configuration and monitoring.
2. **Account Service** present, interface unknown.
3. **xContracting** used only by Sales Partner.
4. **Payments offset** correct as described.
5. **No error handling** for MFT drops; failures post-drop undetectable.

