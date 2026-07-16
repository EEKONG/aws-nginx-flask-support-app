# Terraform

This directory provisions the AWS infrastructure for the Cloud Support Platform lab:

- An EC2 security group for SSH, HTTP, and HTTPS access
- An EC2 instance using an existing AMI and existing EC2 key pair
- An Elastic IP associated with the EC2 instance
- Instance bootstrap through `user_data.sh`

The configuration automatically detects the operator's current public IP and restricts SSH ingress to that `/32` address. HTTP and HTTPS remain publicly reachable because the application is designed to run as a public web endpoint.

## Prerequisites

- Terraform 1.6 or later
- AWS credentials configured outside this repository
- An existing AWS EC2 key pair
- An existing AMI ID in the selected AWS region
- Permission to create EC2 instances, security groups, and Elastic IPs

Do not commit AWS credentials, private keys, `terraform.tfvars`, `.terraform/`, plan files, or Terraform state files.

## Variables

| Name | Required | Description | Example |
|---|---:|---|---|
| `aws_region` | No | AWS region where resources are created. | `ca-central-1` |
| `project_name` | No | Name used for resource names and tags. | `cloud-support-platform` |
| `ami_id` | Yes | Existing AMI ID for the EC2 instance. | `ami-0123456789abcdef0` |
| `instance_type` | No | EC2 instance type. | `t3.micro` |
| `key_name` | Yes | Existing EC2 key pair name. | `your-existing-key-pair` |

## Local Configuration

Create a local `terraform.tfvars` file from the safe example:

```bash
cp terraform.tfvars.example terraform.tfvars
```

Then edit `terraform.tfvars` with your local values. The real `terraform.tfvars` file is ignored by Git and must not be committed.

## Commands

Run Terraform commands from this directory:

```bash
terraform fmt -recursive
terraform init
terraform validate
terraform plan
```

Review the plan carefully before applying. `terraform apply` creates or changes real AWS resources and can incur cost.

## Expected Outputs

- `instance_id`: EC2 instance ID
- `elastic_ip`: Elastic IP attached to the instance
- `public_dns`: Public DNS name for the Elastic IP
- `application_url`: Primary HTTP application URL
- `website_http_url`: HTTP URL
- `website_https_url`: HTTPS URL
- `ssh_command`: Example SSH command with a placeholder private key path

## Cleanup

When the lab infrastructure is no longer needed, review the destroy plan first:

```bash
terraform plan -destroy
```

Only after confirming the resources should be removed, run:

```bash
terraform destroy
```

`terraform destroy` deletes real AWS resources. Do not run it against infrastructure you still need.
