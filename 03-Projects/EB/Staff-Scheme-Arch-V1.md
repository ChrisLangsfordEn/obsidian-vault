# Staff Scheme — Architecture Document V1

## Business Capabilities & Bounded Contexts

**System name:** Staff Scheme

**Primary business capability:** Ingest staff risk-benefit data and make employee/ employer-facing views and documents available via the OCEP front end and mobile application. Supported by the Employee Benefits team.

### Bounded contexts

1. **Data Ingestion / Staging (DataDev)**
   - *Responsibility:* Accepts pipe-delimited CSV files from Business, stages records, validates and performs ETL upsert into production tables using SSIS flows.
   - *Key entities:* `RiskRecord`, `CSVFile`, `StagingRecord`, `ValidationResult`.
   - *Triggers / Events:* Daily midnight cron job detects new CSV file → `FileDetected` → `StagingComplete` → `Validated` → `Upserted`.

2. **Staff Scheme Application (OCEP Service)**
   - *Responsibility:* User-facing service that retrieves consolidated data from production tables (via IPG Data API) and presents risk/benefit views to mobile app / dashboard.
   - *Key entities:* `PersonRiskView`, `Beneficiary` (temporary currently), `BenefitStatement`.
   - *Triggers / Events:* User request (API call) → `ViewRequested`; PDF generation request → `TemplateRequest` / `PDFGenerated`.

3. **Template & PDF Service**
   - *Responsibility:* Template rendering (TypeScript service) that receives data and returns generated PDF benefit statements.
   - *Key entities:* `Template`, `RenderedPDF`.
   - *Triggers / Events:* `RenderTemplate` request → `PDFReady`.

4. **Integration / Gateway (IPG)**
   - *Responsibility:* Provides data API backed by stored procedures that the OCEP service consumes to read production tables.
   - *Key entities:* `IPGDataAPI`, `StoredProcedure`.

### Key domain terms

- **OCEP** — Omnichannel Enablement Platform (front-end / framework used by mobile and web).
- **IPG** — Insurance Portfolio Gateway (data API to retrieve persisted risk/benefit data).
- **DataDev** — Data engineering team + MS SQL Server instance, SSIS flows and staging area.


---

## System Context Diagram (PlantUML / C4)

Below is the PlantUML source for a C4-style System Context diagram. It models actors and external systems that interact with Staff Scheme.

```plantuml
@startuml ContextDiagram
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

Person(employee, "Employee (mobile)", "Uses the company mobile app to view their risk & benefit info")

System_Boundary(ss, "Staff Scheme") {
  System(ocep, "OCEP Service", "User-facing service that provides risk/benefit views and triggers PDF generation")
  System(template, "Template Service", "TypeScript PDF/template renderer")
  System(dataGateway, "IPG Data API", "Data API (stored procedures) used to read production data")
  System(dataDev, "DataDev (MS SQL + SSIS)", "Staging area, ETL/SSIS flows and production tables")
  System(fileDrop, "Business File Drop (CSV)", "Pipe-delimited CSV dropped by Business on month end")
}

Rel(employee, ocep, "Uses / Views risk & benefits (mobile app)")

Rel(ocep, template, "Requests PDF generation (Template API)")
Rel(ocep, dataGateway, "Reads risk & benefit data (IPG Data API) via stored procedures")
Rel(dataGateway, dataDev, "Reads production tables / stored procedures")
Rel(fileDrop, dataDev, "Business drops CSV file (month-end) — DataDev picks up")
Rel_L(dataDev, ocep, "Production data read (indirect) / support")

@enduml
```

---

## Container Diagram (PlantUML / C4)

Below is the PlantUML source for the Container-level diagram that breaks down the Staff Scheme system into containers and interfaces.

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

Note right of template_service : Deployment: single container\ncurrently on old infra;\nmigration planned for September

@enduml
```

---

## Interface Summary

- **OCEP Service (API)**
  - Protocol: HTTPS, REST/JSON
  - Primary operations: `GET /person/{id}/risk`, `GET /person/{id}/beneficiaries`, `POST /beneficiary` (cosmetic), `POST /template/render`
  - Security: OAuth2 / JWT (assumption; align with OCEP framework)

- **Template Service**
  - Protocol: HTTPS, REST/JSON
  - Operations: `POST /templates/render` (payload: template id + data) → returns PDF binary or URL

- **IPG Data API**
  - Protocol: HTTPS / internal network
  - Operations: Stored-procedure-backed endpoints to return production risk records.
  - Access: OCEP Service calls IPG; DataDev owns the underlying tables.

- **DataDev (SSIS / ETL)**
  - Ingest: Picks up CSV file from file-share, stages to `Staging` table, validates, then SSIS upserts to production tables.
  - Trigger: Daily cron job / watcher or manual kick-off by infra.

- **File Drop (Business)**
  - Mechanism: Business drops pipe-delimited CSV into shared file location (day-of-month-end). Format contract must be versioned and validated.

---

## Data & Event Flows (step-by-step)

1. **Monthly data delivery & ingestion**
   - Business places pipe-delimited CSV into File Drop location (month-end).
   - CSV Watcher (cron) or DataDev picks up the file and stores it in `Staging`.
   - DataDev runs validations (SSIS) — invalid rows are logged and reported.
   - Valid rows are transformed and upserted into production tables.
   - Production tables become the source for IPG Data API.

2. **Normal user transaction (view)**
   - Employee / Employer requests risk/benefit data via OCEP front-end → OCEP Service.
   - OCEP Service calls IPG Data API, which reads production tables and returns consolidated data.
   - OCEP Service aggregates the data into `PersonRiskView` and returns JSON to the mobile UI.

3. **PDF generation**
   - User requests a benefit statement PDF.
   - OCEP Service composes the required data and calls Template Service `POST /templates/render`.
   - Template Service returns a binary PDF or URL for download; front-end initiates download.

4. **Beneficiary maintenance**
   - Employer updates beneficiaries via OCEP admin screens.
   - OCEP Service persists changes to the local `Staff Scheme DB` (cosmetic/temporary table).
   - Future: this will integrate with policy & admin systems for canonical beneficiary persistence.


---

## Assumptions

1. **File ingestion** — Business will drop a pipe-delimited CSV file on the agreed location on the last day of the month. The Cron / watcher will detect it daily at midnight.
2. **DataDev ownership** — DataDev team owns staging, SSIS flows and production tables; Staff Scheme reads production data via IPG only.
3. **OCEP framework** — OCEP Service is deployed inside the OCEP ecosystem and follows its authentication and routing patterns.
4. **Beneficiary persistence** — Currently cosmetic within Staff Scheme DB; canonical persistence will be provided by policy/admin systems in the future.
5. **Security & Compliance** — All API interactions occur over TLS; OAuth2/JWT is used for user authentication (confirm with security team).
6. **Migration** — One container still on old infra; migration is targeted for September (non-firm date — confirm schedule with infra).


---

## Next steps / Recommendations

- Validate URLs for the Interface Summary


