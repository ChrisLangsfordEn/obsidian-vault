### Semantic Map of the Staff Savings Project

Following a comprehensive scan of the `staff-savings-sales-partner` project, I have mapped the codebase based on its current Clean Architecture implementation.

#### 1. Architectural Layers

The project is strictly partitioned into several Maven modules, each representing a layer in Clean Architecture:

*   **Presentation Layer (`pep`)**: This is the entry point for the "ocep-partner" front end. It contains Spring `@RestController`s (e.g., `TakeUpEndpoint`, `CustomiseEndpoint`, `ContractingEndpoint`) that receive web requests, map them to UseCase requests, and utilize Presenters to format the output.
*   **Application Layer (`usecases`)**: Contains the core business logic. It is structured around the `UseCase` interface. Each business action is encapsulated in a dedicated UseCase class (e.g., `TakeUpEmployerApplication`, `CheckEligibility`). This layer also defines `Gateways` (interfaces) for data access and external integrations.
*   **Domain Layer (`entities`)**: Contains POJOs representing the business domain (e.g., `Application`, `Product`, `Customer`, `Employee`) and domain-specific enums and exceptions. It has no dependencies on other layers.
*   **Infrastructure Layer (Persistence - `data`)**: Implements the `Gateways` defined in the Application layer for database operations. It relies on Spring Data Repositories (referencing an external `ss-libraries` for actual JPA entities).
*   **Infrastructure Layer (External Integrations - `services`)**: Implements `Gateways` for communicating with external FNB systems such as Email, In-App Messaging, and Customer Enquiry services.

#### 2. Dependencies

The dependency flow follows the Clean Architecture "Dependency Rule", pointing inwards toward the Domain:

*   `pep` → `usecases`, `entities`
*   `usecases` → `entities`
*   `data` → `usecases` (to implement gateways), `entities`
*   `services` → `usecases` (to implement gateways), `entities`
*   `entities` → (No project dependencies)

#### 3. Business Functionalities (Slices)

The project’s business capabilities are primarily organized into three main functional areas within the `usecases` and `pep` modules:

**A. Eligibility & Application (Customise)**
*   `CheckEligibility`: Determines if a business is eligible for Staff Savings.
*   `SaveEmployeeCategories`: Manages which staff categories are included.
*   `SaveContributionType` & `SaveOptionDetails`: Configuration of the savings product.
*   `AcceptQuote` / `DeclineQuote`: Finalizing the application terms.
*   `CancelApplication`: Handling withdrawal from the process.

**B. Contracting**
*   `FetchContractTemplateInformation`: Retrieving the legal contract details.
*   `HandleXContractingResponse`: Processing the outcome of the digital signing process (via X-Contracting).

**C. Product Take-up**
*   `TakeUpEmployerApplication`: The final step where the product is actually created, an account is opened (pending), and welcome communications (Email/In-App) are triggered.

#### 4. Summary of "Over-Engineering" Observations
The current codebase exhibits a high degree of fragmentation. For example, a single logical feature like "Take Up" is spread across:
1.  `TakeUpEndpoint` (pep)
2.  `TakeUpEmployerApplication` (usecases)
3.  `ApplicationGateway` / `ProductGateway` (interfaces in usecases)
4.  `ApplicationGatewayImpl` / `ProductGatewayImpl` (data)
5.  `IndividualCustomerEnquiryGateway` / `MessageGateway` / `EmailGateway` (services)

Refactoring to a **Vertical Slice Architecture** would involve collapsing these layers into feature-based packages (e.g., `za.co.fnb.ss.features.takeup`), where the controller, logic, and data access for that specific feature reside together.