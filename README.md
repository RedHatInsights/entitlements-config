# SKU bundles and config for entitlements

## **IMPORTANT**
Sku based features are now going to be entirely managed by the feature service team. Any update to sku based features here will no longer be reflected in the entitlements service. 
For any required updates to sku based features, please reach out to `it-subprod-request@redhat.com` to make the updates. Feel free to ping [#forum-mgmt-fabric-accessmanagement](https://redhat.enterprise.slack.com/archives/C0233N2MBU6) for help if needed.

We will eventually be ripping out sku based features from this repo, as part of this ticket: [RHCLOUD-49553](https://redhat.atlassian.net/browse/RHCLOUD-49553)
For now, they still exist while we make this transition.

Non sku based features will still be managed thru this repo once sku based features are removed. Details are subject to change while this effort is ongoing. The rest of the README is going to be left alone for now, but will be accurately updated as part of the ticket linked above. 

## About

This is a repository for housing decoupled cloud entitlements service bundles
for determining which entitlements are available, and how they are defined.

Insights Entitlement service repo: https://github.com/RedHatInsights/entitlements-api-go

## Contributing

### Add an entitlement

Entitlements are defined in environment-specific `bundles.yml` files:

- `configs/stage/bundles.yml` — commercial staging (update this first)
- `configs/prod/bundles.yml` — commercial production
- `configs/fedramp-stage/bundles.yml` — FedRAMP staging
- `configs/fedramp-prod/bundles.yml` — FedRAMP production

Each bundle is namespaced by service/feature/offering. Update stage before prod. FedRAMP files use flat `skus` lists only (no `eval_skus`/`paid_skus` split).

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

2. Requiring a valid org ID on the request
```yaml
- name: example_entitlement
  use_valid_org_id: true
```

3. Allowing entitlement for all by default (use sparingly — grants universal access)
```yaml
- name: example_entitlement
  use_valid_org_id: false
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

## Further Reading

- [`docs/security-guidelines.md`](docs/security-guidelines.md) — SKU conventions, bundle mechanisms, YAML formatting rules, and PR review checklist
