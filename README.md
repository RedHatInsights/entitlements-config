# SKU bundles and config for entitlements

## About

This is a repository for housing decoupled cloud entitlements service bundles
for determining which entitlements are available, and how they are defined.

Insights Entitlement service repo: https://github.com/RedHatInsights/entitlements-api-go

## Contributing

### Add an entitlement

Entitlements can be added to the `/configs/bundles.yml` file, namespaced by
service/feature/offering.

### Format of entitlements

Entitlements may be defined by:

1. requiring one or more SKUs exist on the account making the request
```yaml
- name: name_of_entitlement
  skus:
    - MCT3691
```

2. requiring the account number exists on the request
```yaml
- name: name_of_entitlement
  use_valid_acc_num: true
```

3. allowing entitlement for all by default
```yaml
- name: name_of_entitlement
  use_valid_acc_num: false
```

### Deployment

When your PR is merged, an automated job will pick up the changes. This runs once
per day. If you need the changes available faster, please reach out to the platform
infrastructure team to coordinate changes being promoted through the environments.
