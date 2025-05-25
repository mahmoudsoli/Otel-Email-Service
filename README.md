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

# Set Up the AWS Load Balancer Controller on an Amazon EKS cluster
1. To allow the cluster to use AWS Identity and Access Management (IAM) for service accounts, run the following command:
```sh
eksctl utils associate-iam-oidc-provider \
  --cluster YOUR_CLUSTER_NAME \
  --approve
```

2. To download an IAM policy that allows the AWS Load Balancer Controller to make calls to AWS APIs on your behalf, run the following command:
```sh
 curl -o iam_policy.json https://raw.githubusercontent.com/kubernete
 ```

3. To create an IAM policy with the downloaded policy, run the following create-policy AWS CLI command:
```sh
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json
```

4. To create a service account for the AWS Load Balancer Controller, run the following command:
```sh
eksctl create iamserviceaccount \
  --cluster=YOUR_CLUSTER_NAME \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --attach-policy-arn=arn:aws:iam::AWS_ACCOUNT_ID:policy/AWSLoadBalancerControllerIAMPolicy \
  --override-existing-serviceaccounts \
  --approve
```

5. To verify that the new service role is created, run one of the following commands:
```sh
eksctl get iamserviceaccount \
  --cluster=YOUR_CLUSTER_NAME \
  --name=aws-load-balancer-controller \
  --namespace=kube-system
```

# Install the AWS Load Balancer Controller with Helm
Complete the following steps:

1. To add the Amazon EKS chart to Helm, run the following command:
```sh
helm repo add eks https://aws.github.io/eks-charts
```

2. To update the repository and pull the latest chart, run the following command:
```sh
helm repo update eks 
```

3. To install the Helm chart, run the following command:
```sh
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  --set clusterName=YOUR_CLUSTER_NAME \
  --set serviceAccount.create=false \
  --set region=YOUR_REGION_CODE \
  --set vpcId=EKS_CLUSTER_VPC_ID \
  --set serviceAccount.name=aws-load-balancer-controller \
  --version 1.11.0 \
  -n kube-system
```

4. To verify that the controller installed correctly, run the following command:
```sh
kubectl get deployment -n kube-system aws-load-balancer-controller
```

# Argo CD Installation with Helm
Argo CD was installed via Helm for GitOps-based continuous delivery.

Helm Installation Commands:
```sh
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
```
Then install Argo CD in the argocd namespace:

```sh
helm install argocd argo/argo-cd --namespace argocd --create-namespace --set server.service.type=LoadBalancer
```

Get Argo-CD UI URL:
```sh
kubectl get svc -n argocd
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