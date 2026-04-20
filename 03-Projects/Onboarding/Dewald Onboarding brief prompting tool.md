### 🧠 Prompt Skeleton (Conceptual)

> You are an expert software engineering mentor designing a pedagogically sound onboarding project.
> 
> **Goal:** Create a single, cohesive mock project brief that accelerates a developer’s ability to work productively in a real codebase, reason about workflows, and ask high-quality questions.
> 
> ### Developer Profile
> 
> - Years of experience: 3
>     
> - Role expectation: Intermediate
>     
> - Experience with this tech stack: Basic experience at university
>     
> - Experience with adjacent stacks: 3 years experience in .NET
>     
> 
> ### Technical Context
> 
> - Tech stack: Java 17, Spring Boot 3 and Camunda 7. H2 in-memory database.
>     
> - Architectural style: Standalone Spring boot application communicating with other services via REST. The application is structured as a multi-module maven project with the following structure:
>   - core: Runnable Spring boot application with Camunda Engine 
>   - common: Spring components shared across multiple modules
>   - x-api: feature specific module for feature 'x'
>   - util: Utility classes used across modules. Typically not Spring beans
> - Camunda Config: configurations are stored in a separate source code repository and are deployed to the camunda engine using Camunda modeler or the REST API
>     
> 
> ### Domain Context
> 
> - Industry: Life Insurance - business process automation and orchestration
>     
> - Typical workflows and jargon: Sales, Leads, Policy Servicing/Maintenance, Claims, Underwriting
>     
> 
> ### Project Constraints
> 
> - Duration: 4 Weeks
>     
> - Primary onboarding focus
>     
> 
> ---
> 
> ### Instructions
> 
> - Produce a **single narrative project brief**
>     
> - Split it into **time-boxed milestones**
>     
> - For each milestone:
>     
>     - Explicitly state the skills and concepts being developed
>         
>     - Include **intentional gaps** that require clarification
>         
>     - Define **clear deliverables**
>         
>     - Provide **review/checkpoint criteria**
>         
>     - Add reflection prompts
>  
>         
> - Adjust depth and scaffolding based on the developer profile
>     
> - Use domain-accurate language
>     
> - Optimize for autonomy, not step-by-step instructions
>     
> 
> ### Output
> 
> Produce the full mock project brief in a professional internal-document style.
