<div align="center">

# DevSecOps Banking Application

A high-performance, containerized financial platform built with Spring Boot 3, Java 21, and integrated Contextual AI. This project implements a secure "DevSecOps Pipeline" using GitHub Actions, OIDC authentication, and AWS managed services.

[![Java Version](https://img.shields.io/badge/Java-21-blue.svg)](https://www.oracle.com/java/technologies/javase/jdk21-archive-downloads.html)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.4.1-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![GitHub Actions](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-orange.svg)](.github/workflows/devsecops.yml)
[![AWS OIDC](https://img.shields.io/badge/Security-OIDC-red.svg)](#phase-3-security-and-identity-configuration)

</div>

![dashboard](screenshots/1.png)

---

## Technical Architecture

The application is deployed across a multi-tier, segmented AWS environment. The control plane leverages GitHub Actions with integrated security gates at every stage.

```mermaid
graph TD
    subgraph "External Control Plane"
        GH[GitHub Actions]
        User[User Browser]
    end

    subgraph "AWS Infrastructure (VPC)"
        subgraph "Application Tier"
            AppEC2[App EC2 - Ubuntu/Docker]
        end


        subgraph "Artificial Intelligence Tier"
            Ollama[Ollama EC2 - AI Engine]
        end

        subgraph "Identity & Secrets"
            Secrets[AWS Secrets Manager]
            OIDC[IAM OIDC Provider]
        end

        subgraph "Registry"
            [DockerHub]
        end
    end

    GH -->|1. OIDC Authentication| OIDC
    GH -->|2. Push Scanned Image| DockerHub
    GH -->|3. SSH Orchestration| AppEC2
    GH -->|4. DAST Scan| AppEC2
    
    User -->|Port 8080| AppEC2
    AppEC2 -->|JDBC Connection| RDS
    AppEC2 -->|REST Integration| Ollama
    AppEC2 -->|Runtime Secrets| Secrets
    AppEC2 -->|Pull Image| DockerHub
```

---

## Security Pipeline (DevSecOps Pipeline)

The CI/CD pipeline enforces **9 sequential security gates** before any code reaches production:

| Gate | Name | Tool | Purpose |
| :---: | :--- | :--- | :--- |
| 1 | Secret Scan | Gitleaks | Scans entire Git history for leaked secrets |
| 2 | Lint | Checkstyle | Enforces Java Google-Style coding standards |
| 3 | SAST | Semgrep | Scans Java source code for security flaws and OWASP Top 10 |
| 4 | SCA | OWASP Dependency Check (first time run can take more than 30+ minutes) | Scans Maven dependencies for known CVEs |
| 5 | Build | Maven | Compiles and packages the application |
| 6 | Container Scan | Trivy | Scans the Docker image for OS and library vulnerabilities |
| 7 | Push | DockerHub | Pushes the image only after Trivy passes |
| 8 | Deploy | SSH / Docker Compose | Automated deployment to AWS EC2 |
| 9 | DAST | OWASP ZAP | Dynamic attack surface scanning on live app |

---
