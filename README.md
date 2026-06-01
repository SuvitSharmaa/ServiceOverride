# ServiceOverride

Minimal Helm charts for testing **Harness** service and environment value overrides.

## Chart

| Path | Description |
|------|-------------|
| `charts/sample-app/` | Single Deployment + Service (nginx by default) |

## Local test

```bash
helm template test-release ./charts/sample-app
helm template test-release ./charts/sample-app -f ./charts/sample-app/values-harness-example.yaml
```

## Harness setup

1. **Service** → Deployment type **Kubernetes** → **Helm**
2. **Manifest source**: Git → this repository
3. **Chart path**: `charts/sample-app`
4. **Values**: use chart `values.yaml` defaults; add overrides in **Values YAML** on the service or environment

### Override-friendly keys

| Key | Default | Good for testing overrides |
|-----|---------|----------------------------|
| `replicaCount` | `1` | Scale per environment |
| `image.tag` | `1.25` | Artifact / env promotion |
| `app.env` | `dev` | Shows on pod label `app.harness.io/env` |
| `app.message` | `Hello from sample-app` | Env var `APP_MESSAGE` |

See `charts/sample-app/values-harness-example.yaml` for a sample override block.

### Example Harness Values YAML

```yaml
replicaCount: 3
app:
  env: prod
  message: "Deployed via Harness override"
image:
  tag: "<+artifact.tag>"
```

After deploy, verify overrides:

```bash
kubectl get deploy -l app.kubernetes.io/instance=<release-name> -o yaml | grep -E 'replicas|APP_ENV|APP_MESSAGE'
```
