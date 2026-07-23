# Deploying a Web Application to Azure App Services

This lab covers deploying a web application to Azure App Services with an automated CI/CD pipeline via GitHub Actions.

## What I did

### 1. Prepared the application
- Confirmed the app had a main entry file to start the server, and that it read its port from an environment variable rather than hardcoding it, since Azure assigns the port dynamically at runtime.
- Initialized a Git repository (`git init`, `git add .`, `git commit`) for the project so it could be connected to Azure's deployment pipeline.

### 2. Created the Azure App Service
- In the Azure Portal, created a new **Web App** resource.
- Configured:
  - **Resource group**: new group for the project
  - **Runtime stack**: matched to the app's local runtime (Node.js)
  - **OS**: Linux
  - **Region**: closest to expected users
  - **App Service Plan**: Free F1 tier for testing
- Reviewed and created the resource, and waited for provisioning to complete.

### 3. Set up GitHub Actions for deployment
- In the App Service's **Deployment Center**, connected the GitHub repository and branch (`main`).
- Selected **GitHub Actions** as the deployment method, which auto-generated a `deploy.yaml` workflow file under `.github/workflows/`.
- Reviewed the generated workflow and adjusted it to match the app's build/start commands.

### 4. Configured application settings
- Added required environment variables (e.g. `PORT`, `NODE_ENV`, database connection strings) under **Configuration > Application Settings** in the Azure Portal, rather than committing them to the repo.

### 5. Triggered and verified deployment
- Pushed a commit to `main`, which triggered the GitHub Actions workflow automatically.
- Monitored the workflow run under the repo's **Actions** tab to confirm a successful build and deploy.
- Verified the live app via the App Service's default domain, and checked **Monitoring > Log Stream** for runtime errors.

## Troubleshooting notes

- **Workflow fails**: usually an incorrect app name in `deploy.yaml` or an invalid publish profile secret.
- **App won't start**: check the `start` script in `package.json` and confirm the app reads `process.env.PORT` rather than a hardcoded port.
- **Connection issues**: check the App Service's outbound firewall rules if the app needs to reach an external service (e.g. a database).

## Security considerations

- Secrets (publish profile, connection strings) belong in Azure App Settings or GitHub Actions secrets — never committed to the repository.
- The Free F1 tier is fine for a lab, but has no SLA and shares compute with other tenants; production workloads should move to a dedicated tier with scaling and backup configured.
- I'd add branch protection on `main` and require a review before merge, so the deployment pipeline can't be triggered by an unreviewed push.
