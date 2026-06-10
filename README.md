# fleet-gitops — Fleet Management GitOps repo

This is the **platform GitOps repo** for the [Fleet Management](https://github.com/patel0ankur/fleet_infra) IDP.

Argo CD on the shared-services EKS cluster watches `clusters/control/` recursively via an `Application` named `fleet-bootstrap`. Anything committed there is reconciled into the cluster automatically.

## Layout

```
clusters/
  control/                  ← fleet-bootstrap watches this directory
    00-projects/            Argo AppProjects (RBAC, allowed sources)
    10-platform-addons/     Cluster-wide addons (ingress, observability)
    20-fleet-crds/          Phase 2: Platform API CRDs
    30-infratemplates/      Phase 2: kro ResourceGraphDefinitions (golden paths)
    40-experience/          Phase 3: Backstage
    50-ci/                  Phase 4: Argo Workflows
    60-observability/       Phase 5: DevOps Agent wiring
    70-finops/              Phase 6: cost reporting

projects/                   Per-tenant content (Backstage scaffolder writes here)
```

## How to add a new platform component

1. Drop a manifest into the appropriate `clusters/control/<bucket>/` directory.
2. Open a PR.
3. Merge → Argo applies within ~3 min.

Never `kubectl apply` directly to the cluster. This repo is the source of truth.

## Status

- **Phase 1** (Foundation + Bootstrap) — complete. Cluster + EKS Capabilities (ACK, kro, Argo CD) + CSI Driver/ASCP deployed.
- **Phase 2** (Platform API + InfraTemplates) — in progress.
