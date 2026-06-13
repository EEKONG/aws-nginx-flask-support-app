# AWS EC2 + NGINX + Flask Production Deployment

## Project Overview

This project demonstrates the deployment, monitoring, and troubleshooting of a production-style Flask application hosted on AWS EC2 using NGINX as a reverse proxy, Gunicorn as the application server, Let's Encrypt SSL certificates, systemd service management, and Amazon CloudWatch monitoring.

The objective of this project was to illustrate hands-on experience with Linux administration, web application deployment, reverse proxy configuration, HTTPS implementation, service management, monitoring, alerting, and production troubleshooting.

---

## Architecture

```text
Internet
    │
    ▼
AWS Security Group
(Ports 80, 443)
    │
    ▼
NGINX Reverse Proxy
    │
    ▼
Gunicorn
(127.0.0.1:5000)
    │
    ▼
Flask Application
    │
    ▼
CloudWatch Monitoring & Alerts
```

---

## Technologies Used

### Cloud

* AWS EC2
* Amazon CloudWatch
* IAM

### Web & Application

* Python
* Flask
* Gunicorn
* NGINX

### Linux & Operations

* Amazon Linux
* systemd
* SSH
* journalctl
* curl

### Security

* Let's Encrypt
* Certbot
* HTTPS/TLS

---

## Features

* Flask web application deployment
* NGINX reverse proxy configuration
* HTTPS enabled using Let's Encrypt
* Automatic HTTP to HTTPS redirection
* Gunicorn application server
* systemd service management
* CloudWatch monitoring and alarms
* Health check endpoint
* Log analysis and troubleshooting
* SSL certificate auto-renewal validation

---

## Application Endpoints

| Endpoint  | Description              |
| --------- | ------------------------ |
| `/`       | Application landing page |
| `/health` | Health check endpoint    |
| `/skill`  | Sample API endpoint      |

---

## Deployment Process

### 1. Launch EC2 Instance

* Amazon Linux EC2 instance
* Configure Security Groups

  * Port 22 (SSH)
  * Port 80 (HTTP)
  * Port 443 (HTTPS)

### 2. Install Dependencies

```bash
sudo dnf update -y
sudo dnf install nginx python3 python3-pip git -y
```

### 3. Create Python Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate
pip install flask gunicorn
```

### 4. Deploy Flask Application

```bash
gunicorn --workers 3 --bind 127.0.0.1:5000 app:app
```

### 5. Configure NGINX

Configured NGINX to:

* Listen on ports 80 and 443
* Reverse proxy requests to Gunicorn
* Redirect HTTP traffic to HTTPS
* Serve SSL certificates

### 6. Configure systemd

Created a systemd service to ensure:

* Automatic startup after reboot
* Service monitoring
* Simplified management

```bash
sudo systemctl enable flaskapp
sudo systemctl start flaskapp
```

### 7. Enable HTTPS

Used Certbot and Let's Encrypt:

```bash
sudo certbot --nginx
```

Verified certificate renewal:

```bash
sudo certbot renew --dry-run
```

---

## Monitoring

### CloudWatch Metrics

Monitored:

* CPU Utilization
* Memory Utilization
* Disk Utilization

### CloudWatch Alarms

Created alarms for:

* High CPU Usage
* High Memory Usage
* High Disk Usage

---

## Troubleshooting Scenarios

### 502 Bad Gateway

Issue:

* NGINX returned 502 errors.

Investigation:

* Verified Gunicorn service status.
* Confirmed Gunicorn binding.
* Reviewed NGINX error logs.

Resolution:

* Corrected Gunicorn startup configuration and upstream settings.

---

### HTTPS Validation

Issue:

* SSL certificate validation errors.

Investigation:

* Verified DNS records.
* Confirmed Certbot configuration.
* Tested certificate renewal.

Resolution:

* Successfully configured Let's Encrypt certificates and automated renewal.

---

### Log Analysis

Reviewed:

```bash
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
sudo journalctl -u flaskapp -f
```

Observed:

* User traffic
* Health checks
* Automated internet scans
* HTTP redirects
* Application responses

---

### Resource Monitoring

Generated controlled CPU and memory load using:

```bash
stress --cpu 2 --timeout 600
```

Used CloudWatch metrics and alarms to validate monitoring and alerting behavior.

---

## Security Measures

* Gunicorn bound to localhost only
* HTTPS enforced
* SSL certificate auto-renewal configured
* Reverse proxy architecture
* Security Group restrictions
* No sensitive credentials stored in repository

---

## Validation Results

### Service Status

```bash
systemctl status nginx
systemctl status flaskapp
```

Result:

* NGINX running
* Flask application running
* Services enabled on startup

### Endpoint Validation

```bash
curl -I https://edikanekong.online
curl -I https://edikanekong.online/health
curl -I https://edikanekong.online/skill
```

Results:

* HTTPS working
* Health endpoint responding
* Application endpoints accessible

### SSL Renewal Validation

```bash
sudo certbot renew --dry-run
```

Result:

* Successful simulated certificate renewal

---

## Skills Demonstrated

* Linux Administration
* NGINX Configuration
* Reverse Proxy Architecture
* AWS EC2
* CloudWatch Monitoring
* SSL/TLS Management
* Application Deployment
* Service Management
* Log Analysis
* Incident Troubleshooting
* Root Cause Analysis
* Production Support

---

## Future Enhancements

* Infrastructure as Code using Terraform
* Route 53 DNS Management
* CI/CD with GitHub Actions
* Docker Containerization
* Load Balancer Integration
* Auto Scaling Groups
* Centralized Logging
* Monitoring Dashboards

---

## Author

**Edikan Ekong**

AWS Certified Cloud Practitioner

Cloud Support | Technical Support Engineering | Platform Support
