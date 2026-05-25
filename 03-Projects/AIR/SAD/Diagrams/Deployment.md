```plantuml
@startuml AIR_Deployment
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Deployment.puml

LAYOUT_WITH_LEGEND()
LAYOUT_LEFT_RIGHT()

title AIR Deployment Diagram — AWS EKS

' === External Services (horizontally aligned) ===
together {
    Deployment_Node(entra, "Microsoft Entra ID", "Identity Provider") {
        Container(entraId, "Entra ID", "OAuth2/OIDC", "Corporate authentication, JWKS endpoint for token validation")
    }

    Deployment_Node(pep_node, "PEP (Policy Enforcement Point)", "Enterprise API Gateway — Same VPC / Peering") {
        Container(pepService, "AdviceConsumer API", "REST Service via PEP", "Lead sync source, deal execution target — accessed through enterprise API gateway")
    }
}

' === Tier 2: Presentation Layer ===
Deployment_Node(aws, "AWS Cloud", "Amazon Web Services") {

    Deployment_Node(cloudfront_node, "CloudFront", "CDN") {
        Container(cdn, "CloudFront Distribution", "AWS CloudFront", "Routes /api/* to ALB, serves static assets from S3")
    }

    Deployment_Node(s3_node, "S3 Bucket", "Static Hosting") {
        Container(angular, "Angular SPA", "Angular 19+ build artefacts", "Advisor workbench, dashboards, proposal builder UI")
    }

    ' === Tier 3: API / Ingress Layer ===
    Deployment_Node(vpc, "VPC (Private)", "AWS VPC") {

        Deployment_Node(eks, "EKS Cluster", "Kubernetes 1.28+") {

            Deployment_Node(alb_node, "ALB", "AWS Application Load Balancer") {
                Container(alb, "Ingress Controller", "AWS ALB Ingress", "TLS termination, path-based routing to pods")
            }

            ' === Tier 4: Service Layer (vertically aligned namespaces) ===
            together {
                Deployment_Node(ns_main, "Namespace: air-production", "Kubernetes Namespace") {
                    Container(pod1, "AIR API Pod (replica 1)", "Java 21, Spring Boot 4.x", "Modular monolith with all bounded context modules + Bob & Vera agents")
                    Container(pod2, "AIR API Pod (replica 2)", "Java 21, Spring Boot 4.x", "Horizontal scaling via HPA")
                }

                Deployment_Node(ns_pr, "Namespace: air-pr-env (x2-3)", "PR Environment") {
                    Container(prPod, "AIR API Pod (PR)", "Java 21, Spring Boot 4.x", "PR environment with mock/staging data loaded via Liquibase profiles")
                }
            }
        }

        ' === Tier 5: Data Layer (rightmost) ===
        Deployment_Node(rds_node, "RDS", "AWS RDS Multi-AZ") {
            ContainerDb(rds, "PostgreSQL 15+", "AWS RDS", "Multi-AZ, encrypted at rest, module-owned schemas")
        }

        Deployment_Node(ecr_node, "ECR", "Container Registry") {
            Container(ecr, "Container Images", "AWS ECR", "Docker images built via CI/CD pipeline")
        }
    }
}

' === Relationships: left-to-right flow ===
Rel(cdn, angular, "Serves static assets", "HTTPS")
Rel(cdn, alb, "Proxies /api/* requests", "HTTPS")
Rel(alb, pod1, "Routes traffic", "HTTP/8080")
Rel(alb, pod2, "Routes traffic", "HTTP/8080")
Rel(pod1, rds, "JDBC connections", "PostgreSQL/5432")
Rel(pod2, rds, "JDBC connections", "PostgreSQL/5432")
Rel(pod1, pepService, "REST calls via PEP with circuit breaker", "HTTPS")
Rel(pod1, entraId, "JWKS validation", "HTTPS")
Rel(prPod, rds, "Uses staging data profile", "PostgreSQL/5432")

@enduml
```