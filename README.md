# infra-templates-data-systems

Reusable Terraform modules and Helm charts to provision and deploy Airflow, Spark on Kubernetes, and supporting data infra with multi-environment patterns and CI controls.

## Repo
 - Terraform modules for networking, cluster provisioning, IAM, and data platform primitives (S3, Kafka, managed DB).

 - Helm charts and values templates for Airflow and Spark operator deployments with production-ready defaults.

 - Example GitHub Actions workflows for gated Terraform plan/apply and Helm deployments.

## Tech stack
 - Terraform (modules + workspaces)

 - Kubernetes (EKS / GKE / AKS compatibility)

 - Helm charts for Airflow and Spark operator

 - Optional: HashiCorp Vault and SealedSecrets for secrets management

 - CI: GitHub Actions; test tooling: Terratest examples

## Repo layout
`terraform/modules/` — VPC, cluster, iam, data-platform modules

`terraform/envs/` — dev, staging, prod var files and backend configs

`helm/airflow/` — Airflow chart and example values

`helm/spark/` — Spark operator chart and job examples

`.github/workflows/` — terraform-plan, terraform-apply, helm-deploy workflows

`tests/` — terratest and validation scripts

`examples/` — example dev.tfvars, staging.tfvars, prod.tfvars

## Quickstart (dev)
Install tools:

 - `Terraform >= 1.0`

 - `Helm >= 3`

 - `kubectl` and `cloud CLI` for your provider

Configure backend (S3/GCS) and set required env vars: export TF_VAR_region=us-central1

Initialize Terraform: cd terraform && terraform init -backend-config="path=state/dev.tfstate"

Plan: terraform plan -var-file=envs/dev.tfvars

Apply: terraform apply -var-file=envs/dev.tfvars

Deploy Helm charts: `helm upgrade --install airflow helm/airflow -f helm/airflow/values-dev.yaml helm upgrade --install spark helm/spark -f helm/spark/values-dev.yaml`

## Example Terraform modules
`modules/vpc` — VPC with public/private subnets, NAT, route tables.

`modules/cluster` — EKS/GKE provisioning with node groups and autoscaling.

`modules/data-platform` — S3 buckets with lifecycle, managed Kafka topics, and analytics DB links.

`modules/iam` — IAM roles and policies scaffolded for least-privilege access.

## Helm chart highlights
Airflow chart:

Worker autoscaling based on queue depth.

Persistent volumes for logs and scheduler HA options.

RBAC and ingress examples.

Spark chart:

Spark operator CRDs and example SparkApplication manifests.

Resource request/limit templates and autoscaler knobs.

## CI/CD patterns
GitHub Actions:

terraform-plan: runs on PR; executes terraform init and terraform plan and posts plan summary.

terraform-apply: manual approval in protected environments; applies terraform apply to target workspace.

helm-deploy: runs on merges to main; deploys charts to target cluster using kubeconfig from secrets.

## Tests and validation
Terraform linting: terraform fmt and terraform validate enforced in CI.

Terratest examples under tests/ to spin up ephemeral infra for validation.

Kubernetes manifest linting with kubeval and kubescore in CI.

## Security and cost controls
Example least-privilege IAM policies included; replace with org-specific policies.

S3 lifecycle rules and bucket encryption enforced by module defaults.

Resource quotas and autoscaling defaults set to avoid unmanaged cost spikes.

## Examples and templates
`envs/dev.tfvars` — development variable example (small instance sizes).

`helm/airflow/values-dev.yaml` — dev-friendly resource sizing and debug mode.

`.github/workflows/terraform-plan.yml` — example PR plan workflow.

## How to customize
Edit envs/*.tfvars to reflect naming, CIDR ranges, and provider specifics.

Override helm values via values-<env>.yaml and use Helm release naming conventions.

Integrate Vault or sealed secrets for production secret handling; sample patterns included.

## Notes
Use isolated service accounts for CI to limit blast radius.

Store Terraform state in a remote backend with locking (DynamoDB, GCS lock).

Regularly run terraform plan in non-prod to detect drift.
