# Cloud-Security-Labs


A collection of hands-on cloud infrastructure labs covering deployment automation, server hardening, and static site hosting across AWS and Azure. Each lab includes the steps I followed and a security review of the configuration, since several of these labs intentionally expose insecure settings to demonstrate the underlying risk.

## Labs

| Lab | Platform | Focus |
|---|---|---|
| [Azure App Deployment](./azure-app-deployment) | Azure App Service, GitHub Actions | CI/CD pipeline for a web app, environment configuration |
| [Ubuntu MySQL Server](./ubuntu-mysql-server) | AWS/ECS, Ubuntu 20.04 | Server provisioning, MySQL install & remote access |
| [S3 Static Site](./s3-static-site) | AWS S3 | Public object hosting, bucket policy configuration |

## Why these are useful

Each lab walks through a real deployment task end to end, the configuration decisions behind them (ports, IAM/bucket policies, SSH settings, environment variables).

