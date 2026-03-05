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

## Technology Stack

- **Backend Framework**: Java 21, Spring Boot 3.4.1
- **Security Strategy**: Spring Security, IAM OIDC, Secrets Manager
- **Persistence Layer**: Amazon RDS for MySQL 8.0 (Dev/Test Tier)
- **AI Integration**: Ollama (TinyLlama)
- **DevOps Tooling**: Docker, Docker Compose, DockerHub, GitHub Actions, AWS CLI, jq
- **Infrastructure**: Amazon EC2, Amazon VPC

## Implementation Phases

### Phase 1: AWS Infrastructure Initialization
1. **Application Server (EC2)**:

   - Deploy an Ubuntu 22.04 instance with below `User Data`.

      ```bash
      #!/bin/bash

      sudo apt update 
      sudo apt install -y docker.io docker-compose-v2 jq mysql-client
      sudo usermod -aG docker ubuntu
      sudo newgrp docker
      sudo snap install aws-cli --classic
      ```
