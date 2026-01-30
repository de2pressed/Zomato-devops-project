# Zomato DevOps CI/CD Pipeline

## Overview

This project demonstrates the design and implementation of an end-to-end **DevSecOps CI/CD pipeline** for a containerized Node.js-based web application (Zomato clone). The primary objective of the project is to showcase practical experience in **CI/CD automation, containerization, security integration, and infrastructure monitoring** using industry-standard tools on AWS.

The implementation emphasizes **reproducibility and disposable infrastructure**, aligning with real-world DevOps best practices rather than long-lived, manually managed environments.

---

## High-Level Architecture

**CI/CD Layer**

* Jenkins installed on AWS EC2 (Ubuntu 24.04)
* Declarative pipeline defined as code (Jenkinsfile)
* Integrated quality, security, build, and deployment stages

**Application Layer**

* Node.js web application
* Dockerized for consistent build and runtime environments
* Images pushed to Docker Hub

**Security & Quality Layer (DevSecOps)**

* SonarQube for static code analysis and quality gates
* OWASP Dependency-Check for dependency vulnerability scanning
* Trivy for filesystem and container vulnerability scanning
* Docker Scout for image vulnerability insights and best-practice recommendations

**Monitoring & Observability Layer**

* Prometheus for metrics collection
* Node Exporter for OS-level metrics
* Jenkins metrics scraped by Prometheus
* Grafana for visualization and dashboards
* Dedicated monitoring EC2 instance

---

## CI/CD Pipeline Workflow

The Jenkins pipeline is implemented using a **declarative Jenkinsfile** and follows the stages below:

1. **Workspace Cleanup**
   Ensures a clean build environment for every pipeline execution.

2. **Source Code Checkout**
   Retrieves the application source code from GitHub.

3. **Static Code Analysis (SonarQube)**

   * Executes SonarQube analysis using Sonar Scanner
   * Publishes results to SonarQube server
   * Enforces Quality Gates to prevent low-quality code from progressing

4. **Dependency Installation**
   Installs Node.js dependencies using npm.

5. **OWASP Dependency-Check Scan**
   Identifies known vulnerabilities in third-party dependencies and generates security reports.

6. **Trivy Filesystem Scan**
   Scans the project filesystem for vulnerabilities and outputs results as pipeline artifacts.

7. **Docker Image Build**
   Builds the application Docker image using a Dockerfile.

8. **Docker Image Tagging and Push**

   * Tags the image according to Docker Hub naming conventions
   * Pushes the image to Docker Hub using Jenkins-managed credentials

9. **Docker Scout Image Analysis**
   Performs container image analysis for:

   * Known CVEs
   * Security recommendations
   * Image best practices

10. **Container Deployment**
    Deploys the application as a Docker container on AWS EC2.

11. **Post-Build Notifications**
    Sends automated email notifications with:

    * Build status
    * Logs
    * Security scan attachments

---

## Monitoring and Observability

To ensure visibility into both infrastructure and CI/CD performance, a separate monitoring server was provisioned.

### Prometheus

* Installed and configured as a systemd-managed service
* Collects metrics from:

  * Prometheus itself
  * Node Exporter
  * Jenkins (via /prometheus endpoint)
* Configuration reloaded dynamically using lifecycle APIs

### Node Exporter

* Runs as a dedicated Linux service
* Exposes system-level metrics such as CPU, memory, disk, and network usage

### Grafana

* Installed on the monitoring EC2 instance
* Configured with Prometheus as the data source
* Dashboards created to visualize:

  * Server resource utilization
  * Jenkins pipeline metrics
  * Overall system health

---

## Tooling and Versions

Unlike the original tutorial baseline, newer runtime versions were intentionally used to demonstrate adaptability to evolving toolchains:

* Operating System: Ubuntu 24.04
* Java: 21
* Node.js: 25
* Jenkins: Installed via official Debian repository
* Docker: Latest stable release
* SonarQube: Docker-based (LTS Community Edition)
* Prometheus: Standalone binary with systemd service
* Grafana: OSS edition

---

## Project Structure

```
Zomato-devops-project/
├── Monitoring Backup
├── public
├── src
├── .gitignore
├── Dockerfile
├── README.md
├── Recreation.md
├── jenkinsfile
├── package-lock.json
├── package.json
└── tool-versions

```

---

## DevOps Principles Demonstrated

* Pipeline as Code (Jenkinsfile)
* Infrastructure immutability and disposability
* Shift-left security (DevSecOps)
* Separation of CI/CD and monitoring concerns
* Reproducible builds and deployments
* Centralized observability

---

## Disclaimer

This project was inspired by a guided tutorial; however, all steps were **independently executed, customized, debugged, and documented**, including tool version upgrades and infrastructure configuration decisions.
