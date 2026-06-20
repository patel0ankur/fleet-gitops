# fleet-gitops — Fleet Management GitOps repo

This is the **platform GitOps repo** for the [Fleet Management](https://github.com/patel0ankur/fleet_infra) IDP.

Argo CD on the shared-services EKS cluster watches `clusters/control/` recursively via an `Application` named `fleet-bootstrap`. Anything committed there is reconciled into the cluster automatically.

## Layout

```
clusters/
  control/                  ← fleet-bootstrap watches this directory
    00-projects/            Argo AppProjects (RBAC, allowed sources)
    20-fleet-crds/          Platform API CRDs (Project, Deployment, IncidentBinding)
    30-infratemplates/      kro ResourceGraphDefinitions (golden paths)
    40-backstage/           Backstage developer portal + scaffolder templates
    60-incidents/           Incident pipeline RBAC (DevOps Agent)
    80-applicationsets/     ApplicationSets that turn projects/* into running Apps

projects/                   Per-tenant content (Backstage scaffolder writes here)
```

## Golden paths

kro ResourceGraphDefinitions in `30-infratemplates/`, each surfaced as a
self-service Backstage scaffolder template:

- **stateless-service-with-bucket** — Deployment + Service + S3 bucket + IAM
  role + Pod Identity.
- **agentic-service** — AI agent workload: Deployment + Service wired to
  Amazon Bedrock for inference (via Pod Identity, no static keys), a DynamoDB
  table for session/long-term memory, and an S3 artifact bucket. Requires the
  ACK DynamoDB controller in the cluster.

## How to add a new platform component

1. Drop a manifest into the appropriate `clusters/control/<bucket>/` directory.
2. Open a PR.
3. Merge → Argo applies within ~3 min.

Never `kubectl apply` directly to the cluster. This repo is the source of truth.

## Status

- **Phase 1** (Foundation + Bootstrap) — complete. Cluster + EKS Capabilities (ACK, kro, Argo CD) + CSI Driver/ASCP deployed.
- **Phase 2** (Platform API + InfraTemplates) — in progress.
