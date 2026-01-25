# Rebuild & Reproducibility Guide

## Purpose of This Document

This document describes **how the complete CI/CD and monitoring setup can be recreated from scratch** using disposable infrastructure. It is intentionally written at a **high-level**, focusing on architecture and sequencing rather than copy-paste commands.

The goal is to demonstrate understanding of **reproducibility, infrastructure lifecycle, and DevOps design decisions**, not to preserve long-lived servers.

---

## Infrastructure Overview

The system is rebuilt using **three independent components**:

1. **CI/CD Server** – Jenkins on AWS EC2
2. **Application Runtime** – Docker containers on AWS EC2
3. **Monitoring Server** – Prometheus, Node Exporter, and Grafana on AWS EC2

All infrastructure components are **ephemeral** and can be terminated and recreated without data loss, as configuration is stored in version control.

---

## Step 1: Provision Base Infrastructure

* Launch AWS EC2 instances using Ubuntu 24.04
* Instance sizing:

  * Jenkins / CI Server: t2.large (or equivalent)
  * Monitoring Server: t2.large (or equivalent)
* Configure security groups to allow:

  * SSH (22)
  * Jenkins (8080)
  * Application (3000)
  * Prometheus (9090)
  * Node Exporter (9100)
  * Grafana (3000)

---

## Step 2: Configure CI/CD Server (Jenkins)

* Update OS packages and install required system dependencies

* Install Java 21 and Jenkins using official repositories

* Complete Jenkins initial setup via web UI

* Install required Jenkins plugins:

  * Pipeline
  * Git
  * Docker Pipeline
  * SonarQube Scanner
  * OWASP Dependency-Check
  * Email Extension Plugin

* Configure Jenkins global tools:

  * JDK
  * Node.js
  * Sonar Scanner

* Add credentials in Jenkins:

  * GitHub access (if required)
  * Docker Hub credentials
  * SonarQube authentication token

---

## Step 3: Configure Security & Quality Tools

### SonarQube

* Deploy SonarQube using Docker
* Create a project and generate an authentication token
* Configure SonarQube server details in Jenkins
* Enable Quality Gates and webhooks for pipeline integration

### Dependency & Vulnerability Scanning

* Configure OWASP Dependency-Check plugin in Jenkins
* Install Trivy on the CI server for filesystem scanning
* Authenticate Docker Scout for image-level analysis

---

## Step 4: Application Build & Deployment

* Clone the application repository from GitHub
* Install Node.js dependencies
* Build Docker image using Dockerfile
* Tag and push image to Docker Hub
* Deploy the container on EC2 using Docker runtime

Deployment is validated by accessing the application via the exposed port.

---

## Step 5: Monitoring Server Setup

### Prometheus

* Install Prometheus as a standalone binary
* Run Prometheus using a systemd-managed service
* Configure scrape jobs for:

  * Prometheus itself
  * Node Exporter
  * Jenkins metrics endpoint

### Node Exporter

* Install Node Exporter as a system service
* Expose metrics on port 9100

### Grafana

* Install Grafana OSS edition
* Configure Prometheus as a data source
* Import dashboards to visualize:

  * Server resource utilization
  * Jenkins pipeline metrics

---

## Step 6: Validation & Observability

* Verify Jenkins pipeline execution
* Confirm SonarQube quality gate enforcement
* Validate security scan reports
* Ensure Prometheus targets are in UP state
* Confirm Grafana dashboards display real-time metrics

---

## Rebuild Philosophy

* No manual configuration is considered permanent
* All critical configuration is stored in GitHub
* Infrastructure can be destroyed and rebuilt at any time
* Focus is on **automation, observability, and security integration**

---

## Known Limitations

* Kubernetes (EKS) deployment not yet implemented
* GitOps workflow using Argo CD not yet implemented

These are planned extensions and were intentionally left out to maintain scope clarity.

---

## Author

Jayant Dahiya
