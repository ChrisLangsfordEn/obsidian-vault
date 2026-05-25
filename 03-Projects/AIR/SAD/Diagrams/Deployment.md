```plantuml
@startuml AIR_Deployment

!define AWSPuml https://raw.githubusercontent.com/awslabs/aws-icons-for-plantuml/v18.0/dist
!include AWSPuml/AWSCommon.puml
!include AWSPuml/AWSSimplified.puml
!include AWSPuml/Containers/ElasticKubernetesService.puml
!include AWSPuml/Containers/ElasticContainerRegistry.puml
!include AWSPuml/Database/RDSPostgreSQLinstance.puml
!include AWSPuml/NetworkingContentDelivery/CloudFront.puml
!include AWSPuml/NetworkingContentDelivery/ElasticLoadBalancing.puml
!include AWSPuml/Storage/SimpleStorageService.puml
!include AWSPuml/SecurityIdentityCompliance/MicrosoftAD.puml
!include AWSPuml/Groups/all.puml

skinparam linetype polyline
skinparam rectangle {
    RoundCorner 8
}

title AIR Deployment Diagram — AWS EKS

' ── External Services ──────────────────────────────────────────────────────────

rectangle "Microsoft Entra ID\n<i>Identity Provider (OAuth2/OIDC)</i>" as entra #DDEEFF {
    MicrosoftAD(entraId, "Entra ID", "JWKS endpoint for JWT validation\nCorporate authentication")
}

rectangle "Enterprise API Gateway (PEP)\n<i>Same VPC / Peering</i>" as pep #DDEEFF {
    rectangle pepService as "AdviceConsumer API\n<i>REST via Policy Enforcement Point</i>\nLead sync source, deal execution target"
}

' ── AWS Cloud ──────────────────────────────────────────────────────────────────

AWSGroupColour(AWSCloud, "AWS Cloud") {

    CloudFront(cdn, "CloudFront Distribution", "Routes /api/* to ALB\nServes static assets from S3")
    SimpleStorageService(angular, "S3 — Angular SPA", "Angular 19+ build artefacts\nAdvisor workbench & proposal builder UI")

    AWSGroupColour(VPC, "VPC (Private)") {

        ElasticLoadBalancing(alb, "ALB — Ingress Controller", "TLS termination\nPath-based routing to pods")

        AWSGroupColour(EKSCluster, "EKS Cluster — Kubernetes 1.28+") {

            AWSGroupColour(NSMain, "Namespace: air-production") {
                ElasticKubernetesService(pod1, "AIR API Pod (replica 1)", "Java 21 · Spring Boot 4.x\nModular monolith · Bob & Vera agents")
                ElasticKubernetesService(pod2, "AIR API Pod (replica 2)", "Java 21 · Spring Boot 4.x\nHorizontal scaling via HPA")
            }

            AWSGroupColour(NSPr, "Namespace: air-pr-env (×2–3)") {
                ElasticKubernetesService(prPod, "AIR API Pod (PR)", "Java 21 · Spring Boot 4.x\nEphemeral env · Liquibase staging profile")
            }
        }

        RDSPostgreSQLinstance(rds, "PostgreSQL 15+", "AWS RDS Multi-AZ\nEncrypted at rest · Module-owned schemas")
        ElasticContainerRegistry(ecr, "Container Images", "AWS ECR\nDocker images built via CI/CD pipeline")
    }
}

' ── Relationships ──────────────────────────────────────────────────────────────

cdn --> angular        : Serves static assets\n<i>HTTPS</i>
cdn --> alb            : Proxies /api/* requests\n<i>HTTPS</i>
alb --> pod1           : Routes traffic\n<i>HTTP :8080</i>
alb --> pod2           : Routes traffic\n<i>HTTP :8080</i>
pod1 --> rds           : JDBC connections\n<i>PostgreSQL :5432</i>
pod2 --> rds           : JDBC connections\n<i>PostgreSQL :5432</i>
prPod --> rds          : Staging data profile\n<i>PostgreSQL :5432</i>
pod1 --> pepService    : REST via PEP + circuit breaker\n<i>HTTPS</i>
pod1 --> entraId       : JWKS validation\n<i>HTTPS</i>
ecr --> pod1           : Image pull\n<i>Docker</i>
ecr --> pod2           : Image pull\n<i>Docker</i>
ecr --> prPod          : Image pull\n<i>Docker</i>

@enduml
```