# Otel-Email-Service
# Email Service from OpenTelemetry Demo Project
# Infrastructure Setup with Terraform
The infrastructure was provisioned using Terraform with the modules approach, which provides better reusability and modularization.

Created Components:
VPC (with public/private subnets, route tables, IGW, etc.)

EKS Cluster with worker nodes

Terraform Command Sequence:
```sh
terraform init
terraform plan
terraform apply
```
Each major component like vpc, eks, and node_groups was defined in separate modules and then invoked in the root main.tf.

# Continuous Integration (CI) Pipeline
A Git-based CI pipeline was implemented to ensure code security and quality before deployment.

# Tools Used:
# ✅ GitLeaks
Purpose: Detect secrets and sensitive data committed in code (e.g., API keys, tokens).

Integration: It runs during the CI stage to prevent any hardcoded secrets from being pushed to the repo.


# ✅ Trivy
Purpose: Scan Docker images for known vulnerabilities (CVEs), misconfigurations, and exposed secrets.

Integration: It scans the built container image before pushing it to the registry.

# Argo CD Installation with Helm
Argo CD was installed via Helm for GitOps-based continuous delivery.

Helm Installation Commands:
```sh
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
```
Then install Argo CD in the argocd namespace:

```sh
helm install argocd argo/argo-cd --namespace argocd --create-namespace --set server.service.type=loadbalancer
```

Get Argo CD Admin Password:
To retrieve the initial admin password for the Argo CD UI:

```sh
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode
```
The Email service "sends" an email to the customer with their order details by
rendering it as a log message. It expects a JSON payload like:

```json
{
  "email": "some.address@website.com",
  "order": "<serialized order protobuf>"
}
```

## Local Build

We use `bundler` to manage dependencies. To get started, simply `bundle install`.

## Running locally

You may run this service locally with `bundle exec ruby email_server.rb`.

## Docker Build

From `src/email`, run `docker build .`