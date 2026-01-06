# Kustomize PoC Results (nh-kustomize-openproject)

## Status
- Structure complete (base renders + overlays for memcached, openproject, postgres)
- ArgoCD paths align with requested mapping (Test/Production branches)
- Vault POD_INJECTION configured via overlays (annotations for all workloads)
- Deployment not executed in this environment; sync/health evidence pending cluster run

## Repository structure
```
openproject-project/
  memcached/
    base/helm-rendered/memcached.yaml
    overlays/{test,prod}/kustomization.yaml
  openproject/
    base/helm-rendered/openproject.yaml
    overlays/{test,prod}/kustomization.yaml
  postgres/
    base/helm-rendered/postgres.yaml
    base/helm-rendered/postgres-backup-{pvc,cronjob}.yaml
    overlays/{test,prod}/kustomization.yaml
```

## ArgoCD app mapping (Kustomize)
| App Name | Path | Revision | Namespace |
| --- | --- | --- | --- |
| lhw-openproject-memcached-test | openproject-project/memcached/overlays/test | Test | lhw-openproject-test |
| lhw-openproject-memcached-prod | openproject-project/memcached/overlays/prod | Production | lhw-openproject-prod |
| lhw-openproject-openproject-test | openproject-project/openproject/overlays/test | Test | lhw-openproject-test |
| lhw-openproject-openproject-prod | openproject-project/openproject/overlays/prod | Production | lhw-openproject-prod |
| lhw-openproject-postgres-test | openproject-project/postgres/overlays/test | Test | lhw-openproject-test |
| lhw-openproject-postgres-prod | openproject-project/postgres/overlays/prod | Production | lhw-openproject-prod |

## Vault POD_INJECTION
- Overlays add annotations: `vault.hashicorp.com/agent-inject=true`, role `poc-role`, secret `secret/data/poc/db`, and template to write `/vault/secrets/db`.
- Requires Vault Agent Injector + K8s auth role `poc-role` bound to app service accounts.

## Next steps to verify
1. Apply ArgoCD Applications pointing to this repo/paths and branches Test/Production.
2. Sync and confirm pods show 2/2 containers (app + vault-agent).
3. `kubectl exec` to confirm `/vault/secrets/db` rendered (without echoing secrets).
