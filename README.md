# Cloud Support and Incident Intelligence Platform

An AWS-hosted cloud support engineering lab demonstrating infrastructure provisioning, Linux administration, application deployment, monitoring, incident troubleshooting and AI-assisted log analysis.

The platform is designed around the responsibilities of a Cloud Support Engineer or Technical Support Engineer supporting a customer-facing SaaS application. It demonstrates how application, web-server, operating-system and cloud-infrastructure failures can be detected, investigated, resolved and documented.

---

## Project Overview

The platform runs a Python Flask application on an Amazon EC2 instance behind Gunicorn and an NGINX reverse proxy.

Terraform provisions the core AWS infrastructure, while Amazon CloudWatch collects operational logs and metrics. A Python-based incident analysis workflow retrieves CloudWatch and NGINX logs using `boto3` and uses the OpenAI API to generate structured incident reports, root-cause hypotheses and recommended remediation steps.

The project focuses on operational support rather than application feature development.

---

## Project Objectives

This project demonstrates how to:

* Provision repeatable AWS infrastructure using Terraform
* Deploy a Flask application behind Gunicorn and NGINX
* Manage Linux services using systemd
* Configure HTTPS using Let’s Encrypt and Certbot
* Protect infrastructure using AWS security groups and IAM
* Collect application and web-server logs in Amazon CloudWatch
* Monitor CPU, memory, disk and instance health
* Generate alerts for infrastructure and service failures
* Investigate production-style incidents using logs and Linux diagnostics
* Produce structured root-cause analysis and customer-ready incident updates
* Use AI to accelerate log review while retaining human validation

---

## Architecture

```text
                            Users
                              │
                              ▼
                         DNS / HTTPS
                              │
                              ▼
                    AWS Security Group
                              │
                              ▼
                      NGINX Reverse Proxy
                              │
                              ▼
                           Gunicorn
                              │
                              ▼
                      Flask Application
                              │
             ┌────────────────┴────────────────┐
             ▼                                 ▼
      Linux/systemd Logs                NGINX Access and
                                             Error Logs
             │                                 │
             └────────────────┬────────────────┘
                              ▼
                    Amazon CloudWatch Logs
                              │
             ┌────────────────┴────────────────┐
             ▼                                 ▼
      CloudWatch Metrics                CloudWatch Alarms
             │                                 │
             ▼                                 ▼
       Health Monitoring                  SNS Notification
             │
             ▼
                Python AI Log Intelligence
                              │
                              ▼
               Incident Analysis and RCA Report
```

---

## Repository Structure

```text
cloud-support-platform/
├── app/                         # Flask application
├── terraform/                   # AWS infrastructure as code
├── nginx/                       # NGINX reverse-proxy configuration
├── systemd/                     # Flask/Gunicorn service definition
├── ai-log-intelligence/         # CloudWatch and OpenAI analysis scripts
├── cloudwatch_logs/             # Sample and exported log evidence
├── incident-reports/            # Incident reports and RCA documentation
├── docs/                        # Deployment and troubleshooting guides
├── scripts/                     # Operational and validation scripts
├── screenshots/                 # Project evidence
├── tests/                       # Automated tests under development
├── .github/workflows/           # CI workflow under development
├── .env.example                 # Environment-variable template
├── .gitignore
├── requirements.txt
└── README.md
```

Folders marked as under development may initially contain placeholder files until their corresponding sprint tasks are completed.

---

# Implemented Components

## Application Deployment

The application is deployed using:

* Python
* Flask
* Gunicorn
* NGINX
* systemd
* Amazon Linux

NGINX accepts inbound HTTP and HTTPS requests and forwards application traffic to Gunicorn, which serves the Flask application on an internal port.

Systemd manages the application as a Linux service and supports automatic startup, restart and service-status investigation.

---

## Infrastructure as Code

Terraform provisions and manages the core infrastructure, including:

* Amazon EC2 instance
* AWS security group
* Restricted SSH access
* Public HTTP and HTTPS access
* Elastic IP association
* EC2 key-pair reference
* Startup commands using EC2 user data
* Infrastructure outputs

Terraform retrieves the engineer’s current public IP and restricts SSH access to that address using a `/32` CIDR rule.

Additional IAM, monitoring and DNS resources are being moved into Terraform as the project progresses.

---

## Cloud Infrastructure and Security

The current environment includes:

* Amazon EC2
* AWS IAM
* AWS security groups
* Elastic IP
* DNS configuration
* HTTPS/TLS
* Let’s Encrypt
* Certbot
* HTTP-to-HTTPS redirection

The security group permits:

* SSH from the authorized administrator IP
* HTTP on port 80
* HTTPS on port 443

The Flask application is not exposed directly to the public internet. Application traffic passes through NGINX.

---

## Monitoring and Alerting

Amazon CloudWatch is used to collect and monitor:

* EC2 CPU utilization
* Memory usage
* Disk usage
* Instance health
* NGINX access logs
* NGINX error logs
* Application and service events

CloudWatch alarms provide visibility into infrastructure and application-health conditions. Amazon SNS is used for alarm notifications.

---

## AI-Assisted Log Intelligence

The platform includes a Python workflow that retrieves CloudWatch logs using `boto3` and analyzes operational events using the OpenAI API.

### Current capabilities

* Total request analysis
* Successful versus failed request counts
* HTTP status-code breakdown
* Peak traffic identification
* Error and anomaly detection
* Likely root-cause identification
* Severity classification
* Recommended remediation
* Suggested AWS and Linux diagnostic commands
* Structured incident reports
* Customer-facing incident updates

AI-generated findings are treated as investigation assistance, not as a replacement for log validation or engineering judgment.

---

# Production-Style Incidents Investigated

## SSH timeout using a private IP

**Root cause:** An EC2 private IP address was used from outside the VPC.

**Resolution:** Connected using the public or Elastic IP and validated the security-group rule.

---

## NGINX permission failure

**Root cause:** Administrative commands were run without sufficient privileges.

**Resolution:** Used the appropriate `sudo` and systemd commands.

---

## Flask application outage

**Root cause:** The Flask/Gunicorn process or systemd service was stopped.

**Resolution:** Inspected the service, reviewed journal logs and restored the application.

---

## 502 Bad Gateway

**Root cause:** NGINX was healthy but could not reach the Gunicorn backend.

**Resolution:** Verified the upstream port, inspected NGINX errors and restarted the backend service.

---

## HTTP connection refusal

**Root cause:** NGINX was stopped or not listening on port 80.

**Resolution:** Inspected the service and listening ports, restarted NGINX and validated an HTTP 200 response.

---

## IAM permission failure

**Root cause:** The EC2 instance role did not have permission to retrieve CloudWatch logs.

**Resolution:** Updated the IAM policy and validated log access through the AWS SDK.

---

## Public scanning activity

**Finding:** NGINX logs contained automated requests for nonexistent files, administrative endpoints and common vulnerable paths.

**Response:** Classified the requests, confirmed they did not represent successful access and documented recommended hardening controls.

---

# Troubleshooting Methodology

The investigation process moves through each technical layer:

```text
1. Confirm customer impact
2. Validate DNS resolution
3. Test network reachability
4. Inspect AWS security-group rules
5. Validate listening ports
6. Check NGINX service health
7. Check Gunicorn and Flask service health
8. Test the backend directly with curl
9. Inspect systemd and journalctl logs
10. Inspect NGINX access and error logs
11. Review CloudWatch metrics and alarms
12. Isolate the failure domain
13. Apply and validate remediation
14. Document root cause and corrective actions
15. Prepare a customer-facing update
```

---

# Technology Stack

## Cloud and Infrastructure

* AWS EC2
* AWS IAM
* AWS Security Groups
* Elastic IP
* Amazon CloudWatch
* Amazon SNS
* Terraform

## Application

* Python
* Flask
* Gunicorn

## Web and Networking

* NGINX
* DNS
* TCP/IP
* HTTP/HTTPS
* SSL/TLS
* Let’s Encrypt
* Certbot

## Linux Operations

* Amazon Linux
* SSH
* systemd
* `systemctl`
* `journalctl`
* `curl`
* `grep`
* `tail`
* `ps`
* `ss`

## Automation and AI

* Python
* Bash
* `boto3`
* OpenAI API
* OpenAI Python SDK

---

# Skills Demonstrated

* AWS infrastructure provisioning
* Infrastructure as code
* Linux administration
* Production application support
* NGINX reverse-proxy troubleshooting
* Gunicorn and Flask service management
* DNS and HTTPS troubleshooting
* IAM permission investigation
* Log analysis
* Cloud monitoring and alerting
* Incident triage
* Root-cause analysis
* Service recovery
* Customer-impact assessment
* Technical documentation
* Customer-facing incident communication
* AI-assisted operational analysis

---

# Current Limitations

This project currently uses a single EC2 instance and is intended as a support-engineering lab.

It does not yet provide:

* Automatic horizontal scaling
* Load balancing
* Multi-AZ availability
* Automated failover
* Container orchestration
* Zero-downtime deployments
* Complete automated test coverage
* Fully automated CI/CD

These limitations are documented intentionally to distinguish the implemented environment from a highly available production architecture.

---

# Development Roadmap

## Application Packaging

* Dockerize Flask and Gunicorn
* Add NGINX and PostgreSQL through Docker Compose
* Add container health checks
* Run containers as non-root users

## Developer Support Scenarios

* REST API endpoints
* API-key authentication
* Webhook receiver
* HMAC signature validation
* PostgreSQL event persistence
* Postman collection
* Controlled HTTP failure simulations

## Testing and Delivery

* Pytest coverage
* Ruff linting
* Bandit security scanning
* Dependency vulnerability scanning
* GitHub Actions
* Terraform validation
* Docker image validation

## Observability

* Structured JSON logging
* Request-correlation IDs
* Application latency metrics
* 4xx and 5xx error metrics
* CloudWatch operational dashboard
* Additional service-level alarms

## Security Improvements

* AWS Systems Manager Session Manager
* Removal of public SSH access
* More restrictive IAM policies
* Automated configuration validation

## Future Architecture Exploration

* Application Load Balancer
* Auto Scaling Group
* ECS or Kubernetes
* Multi-AZ deployment

These are future improvements and are not represented as currently implemented features.

---

# Why This Project Matters

Technical Support and Cloud Support Engineers are expected to investigate issues across multiple layers, including customer requests, application behavior, Linux services, networking, permissions and cloud infrastructure.

This project demonstrates an end-to-end support workflow:

```text
Customer symptom
      ↓
Request reproduction
      ↓
Infrastructure and service validation
      ↓
Log and metric analysis
      ↓
Root-cause isolation
      ↓
Service recovery
      ↓
Corrective action
      ↓
Customer communication
```

The goal is not simply to deploy a Flask application. The goal is to demonstrate the ability to operate, troubleshoot and support a customer-facing cloud service.

---

# Author

**Edikan Ekong**

AWS Certified Cloud Practitioner

Technical Support Engineer | Cloud Support Engineer | Platform Support Engineer
