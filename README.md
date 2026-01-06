# nh-kustomize-openproject

Kustomize-based PoC for managing OpenProject, Postgres, and Memcached with pre-rendered Helm YAML in `base/helm-rendered` plus per-environment overlays (test/prod). Structure mirrors the Tanka repo for direct A/B comparison.

## Layout
```
openproject-project/
  memcached|openproject|postgres/
    base/helm-rendered/*.yaml   # pre-rendered Helm output
    overlays/{test,prod}/kustomization.yaml
```

## ArgoCD app mapping
| App | Path | Revision | Namespace |
| --- | --- | --- | --- |
| lhw-openproject-memcached-test | openproject-project/memcached/overlays/test | Test | lhw-openproject-test |
| lhw-openproject-memcached-prod | openproject-project/memcached/overlays/prod | Production | lhw-openproject-prod |
| lhw-openproject-openproject-test | openproject-project/openproject/overlays/test | Test | lhw-openproject-test |
| lhw-openproject-openproject-prod | openproject-project/openproject/overlays/prod | Production | lhw-openproject-prod |
| lhw-openproject-postgres-test | openproject-project/postgres/overlays/test | Test | lhw-openproject-test |
| lhw-openproject-postgres-prod | openproject-project/postgres/overlays/prod | Production | lhw-openproject-prod |

## How to build
```bash
# example
cd openproject-project/memcached/overlays/test
kustomize build
```

## Vault Agent Injector
Overlays add `vault.hashicorp.com/*` annotations so Vault Agent renders `/vault/secrets/db`. Adjust role/secret path in patches if your Vault config differs.

## Branches
- main
- Test
- Production
