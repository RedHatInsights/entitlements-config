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

1. Requiring one or more SKUs exist on the account making the request. This can be done in 1 of 2 ways: 

    i. define a list of `skus` (this method does not support trial versions of features)
```yaml
- name: example_entitlement
  skus:
    - MCT3691
``` 
&nbsp;
    ii. define 2 lists of skus, one for eval/trial skus and one for paid skus

```yaml
- name: example_entitlement
  eval_skus:
    - MCT3691
  paid_skus:
    - RH00031
```
More details on how this option works below. 

2. Requiring the account number exists on the request
```yaml
- name: example_entitlement
  use_valid_acc_num: true
```

3. Allowing entitlement for all by default
```yaml
- name: example_entitlement
  use_valid_acc_num: false
```

### Eval & Paid Skus
If you define your feature with `eval_skus` and `paid_skus`, this will enable `is_trial` in the entitlements service. This flag denotes if the feature is designated as a trial on the accout or not. 

Example: given the config
```yaml
- name: my-bundle
  eval_skus: 
    - RH0001
  paid_skus:
    - RH0002
```
we can see following values of `is_trial` when querying `GET /services` in the entitlements service:
| Account subscriptions | `is_entitled` | `is_trial` |
|-------|-------|-------|
| RH0001, RH0002 | true  | true  |
| RH0001         | true  | true  |
| RH0002         | true  | false |
| (empty)        | false | false |

### Deployment

When your PR is merged, you will need to update the sha reference of the `entitlements-bundle-config-<env>` deployment in app interface to the latest commit after merge.
Once that is done, entitlements will bounce in the corresponding environment(s) and the config changes should be reflected.
Contact the Access & Management team (@crc-entitlements-team) for any assistance.
