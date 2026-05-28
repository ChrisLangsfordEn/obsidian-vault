```plantuml
@startuml AIR_Deployment

!define AWSPuml https://raw.githubusercontent.com/awslabs/aws-icons-for-plantuml/v23.0/dist
!include AWSPuml/AWSCommon.puml
!include AWSPuml/AWSSimplified.puml
!include AWSPuml/Groups/AWSCloud.puml
!include AWSPuml/Groups/VPC.puml
!include AWSPuml/Groups/AvailabilityZone.puml
!include AWSPuml/Groups/PublicSubnet.puml
!include AWSPuml/Groups/PrivateSubnet.puml
!include AWSPuml/NetworkingContentDelivery/CloudFront.puml
!include AWSPuml/NetworkingContentDelivery/ElasticLoadBalancingApplicationLoadBalancer.puml
!include AWSPuml/Storage/SimpleStorageService.puml
!include AWSPuml/Containers/ElasticKubernetesService.puml
!include AWSPuml/Containers/ElasticContainerRegistry.puml
!include AWSPuml/Database/RDS.puml
!include AWSPuml/SecurityIdentityCompliance/IdentityandAccessManagement.puml
!include AWSPuml/General/Internet.puml
!include AWSPuml/Compute/EC2Instance.puml

title AIR Deployment Diagram — AWS EKS

hide stereotype
skinparam linetype ortho
left to right direction

' ── External Systems (left edge — entry point for user traffic) ──────────────
rectangle "External Services" as ext {
    IdentityandAccessManagement(entraId, "Microsoft Entra ID\nOAuth2/OIDC", "Corporate IdP")
    Internet(pep, "AdviceConsumer API\n(via PEP)", "Enterprise Gateway")
}

' ── AWS Cloud ────────────────────────────────────────────────────────────────
AWSCloudGroup(cloud) {

    ' ── CDN & Static Hosting ─────────────────────────────────────────────────
    CloudFront(cdn, "CloudFront", "Routes /api/* → ALB\nServes static assets from S3")
    SimpleStorageService(s3, "S3 Bucket", "Angular 19+ SPA\nbuild artefacts")

    ' ── VPC ──────────────────────────────────────────────────────────────────
    VPCGroup(vpc, "VPC (Private)") {

        ' ── Public Subnet ────────────────────────────────────────────────────
        PublicSubnetGroup(pubSubnet, "Public Subnet") {
            ElasticLoadBalancingApplicationLoadBalancer(alb, "Application\nLoad Balancer", "TLS termination\npath-based routing")
        }

        ' ── Private Subnet ───────────────────────────────────────────────────
        PrivateSubnetGroup(privSubnet, "Private Subnet") {

            ElasticKubernetesService(eks, "EKS Cluster", "Kubernetes 1.28+")

            rectangle "Namespace: air-production" as nsProd {
                EC2Instance(pod1, "AIR API Pod (1)", "Java 21 · Spring Boot 4.x\nAll modules + Bob & Vera")
                EC2Instance(pod2, "AIR API Pod (2)", "HPA replica")
            }

            rectangle "Namespace: air-pr-env" as nsPR {
                EC2Instance(prPod, "AIR API Pod (PR)", "Ephemeral · Liquibase staging data")
            }
        }

        ' ── Data Layer ───────────────────────────────────────────────────────
        AvailabilityZoneGroup(dataAz, "Data Layer — Multi-AZ") {
            RDS(rds, "PostgreSQL 15+", "Multi-AZ · encrypted\nModule-owned schemas")
        }
    }

    ' ── ECR (bottom — infra concern, kept away from user-traffic path) ───────
    ElasticContainerRegistry(ecr, "ECR", "Docker images\nbuilt by CI/CD")
}

' ── User Traffic Flow (left → right) ─────────────────────────────────────────
cdn --> s3  : static assets (HTTPS)
cdn --> alb : /api/* (HTTPS)
alb --> pod1 : HTTP/8080
alb --> pod2 : HTTP/8080

' ── Pod → Data ────────────────────────────────────────────────────────────────
pod1 --> rds  : JDBC / 5432
pod2 --> rds  : JDBC / 5432
prPod --> rds : JDBC / 5432 (staging)

' ── Pod → External Services ───────────────────────────────────────────────────
pod1 -u-> pep     : REST + Circuit Breaker (HTTPS)
pod1 -u-> entraId : JWKS validation (HTTPS)

' ── Infra: ECR → EKS (bottom, isolated from traffic flow) ────────────────────
ecr -u-> eks : image pull

@enduml
```