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

' === External Systems ===
rectangle "External Services" as ext {
    IdentityandAccessManagement(entraId, "Microsoft Entra ID\nOAuth2/OIDC", "Corporate IdP")
    Internet(pep, "AdviceConsumer API\n(via PEP)", "Enterprise Gateway")
}

' === AWS Cloud ===
AWSCloudGroup(cloud) {

    ' === CDN & Static Hosting ===
    CloudFront(cdn, "CloudFront Distribution", "Routes /api/* to ALB\nServes static assets from S3")
    SimpleStorageService(s3, "S3 Bucket", "Angular 19+ SPA\nbuild artefacts")

    ' === VPC ===
    VPCGroup(vpc, "VPC (Private)") {

        ' === Public Subnet — Load Balancer ===
        PublicSubnetGroup(pubSubnet, "Public Subnet") {
            ElasticLoadBalancingApplicationLoadBalancer(alb, "Application Load Balancer", "TLS termination\npath-based routing")
        }

        ' === Private Subnet — EKS Cluster ===
        PrivateSubnetGroup(privSubnet, "Private Subnet") {

            ElasticKubernetesService(eks, "EKS Cluster", "Kubernetes 1.28+")

            rectangle "Namespace: air-production" as nsProd {
                EC2Instance(pod1, "AIR API Pod\n(replica 1)", "Java 21, Spring Boot 4.x\nAll bounded context modules\n+ Bob & Vera agents")
                EC2Instance(pod2, "AIR API Pod\n(replica 2)", "Horizontal scaling via HPA")
            }

            rectangle "Namespace: air-pr-env (x2-3)" as nsPR {
                EC2Instance(prPod, "AIR API Pod (PR)", "PR environment\nMock/staging data via Liquibase")
            }
        }

        ' === Data Layer ===
        AvailabilityZoneGroup(dataAz, "Data Layer — Multi-AZ") {
            RDS(rds, "PostgreSQL 15+", "Multi-AZ, encrypted at rest\nModule-owned schemas")
        }
    }

    ' === Container Registry ===
    ElasticContainerRegistry(ecr, "ECR", "Docker images\nbuilt via CI/CD")
}

' === Relationships ===
cdn -d-> s3 : Serves static assets\n(HTTPS)
cdn -d-> alb : Proxies /api/* requests\n(HTTPS)
alb -d-> pod1 : Routes traffic\n(HTTP/8080)
alb -d-> pod2 : Routes traffic\n(HTTP/8080)
pod1 -d-> rds : JDBC\n(PostgreSQL/5432)
pod2 -d-> rds : JDBC\n(PostgreSQL/5432)
prPod -d-> rds : Staging data profile\n(PostgreSQL/5432)
pod1 -r-> pep : REST via PEP\n+ Circuit Breaker (HTTPS)
pod1 -r-> entraId : JWKS validation\n(HTTPS)
ecr -u-> eks : Pulls images

@enduml
```