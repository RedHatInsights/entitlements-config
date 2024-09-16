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

1. requiring one or more SKUs exist on the account making the request (and whether the SKU is a trial SKU or not).
```yaml
- name: name_of_entitlement
  skus:
    MCT3691:
      is_trial: true|false
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

When your PR is merged, you will need to update the sha reference of the `entitlements-bundle-config-<env>` deployment in app interface to the latest commit after merge.
Once that is done, entitlements will bounce in the corresponding environment(s) and the config changes should be reflected.
Contact the Access & Management team (@crc-entitlements-team) for any assistance.
