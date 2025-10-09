# GP Kustomize Standardization Documentation

## Overview
This document identifies and documents all input, output, static, and default variables used in Kubernetes configuration for GP Kustomize standardization.

---

## Variable Categories

### 1. Input Variables
Input variables are values that are provided externally to customize the Kubernetes deployment.

| Variable Name | Type | Description | Example Value | Required |
|---------------|------|-------------|---------------|----------|
| `namespace` | string | Target namespace for deployment | `production` | Yes |
| `replicas` | integer | Number of pod replicas | `3` | No |
| `image.tag` | string | Container image tag | `v1.2.3` | Yes |
| `image.repository` | string | Container image repository | `myregistry/myapp` | Yes |
| `resources.requests.cpu` | string | CPU resource requests | `100m` | No |
| `resources.requests.memory` | string | Memory resource requests | `128Mi` | No |
| `resources.limits.cpu` | string | CPU resource limits | `500m` | No |
| `resources.limits.memory` | string | Memory resource limits | `512Mi` | No |
| `ingress.host` | string | Ingress hostname | `app.example.com` | No |
| `ingress.enabled` | boolean | Enable/disable ingress | `true` | No |
| `env.DATABASE_URL` | string | Database connection string | `postgresql://...` | Yes |
| `env.API_KEY` | string | External API key | `abc123xyz` | No |

### 2. Output Variables
Output variables are values generated or computed during the Kustomize process.

| Variable Name | Type | Description | Source | Usage |
|---------------|------|-------------|--------|-------|
| `metadata.labels.app` | string | Application label | Generated from base config | Pod/Service selection |
| `metadata.labels.version` | string | Version label | Derived from image.tag | Deployment tracking |
| `spec.selector.matchLabels` | object | Label selectors | Computed from metadata.labels | Service/Deployment linking |
| `status.readyReplicas` | integer | Number of ready replicas | Kubernetes runtime | Monitoring |
| `spec.clusterIP` | string | Service cluster IP | Kubernetes auto-assigned | Internal networking |
| `status.loadBalancer.ingress` | array | Load balancer endpoints | Kubernetes/Cloud provider | External access |

### 3. Static Variables
Static variables are fixed values that remain constant across environments.

| Variable Name | Type | Value | Description | Location |
|---------------|------|-------|-------------|----------|
| `apiVersion` | string | `apps/v1` | Kubernetes API version for Deployment | deployment.yaml |
| `kind` | string | `Deployment` | Kubernetes resource type | deployment.yaml |
| `spec.strategy.type` | string | `RollingUpdate` | Deployment strategy | deployment.yaml |
| `spec.template.spec.restartPolicy` | string | `Always` | Pod restart policy | deployment.yaml |
| `metadata.labels.managed-by` | string | `kustomize` | Management tool identifier | All resources |
| `spec.ports[0].protocol` | string | `TCP` | Network protocol | service.yaml |
| `spec.type` | string | `ClusterIP` | Service type | service.yaml |

### 4. Default Variables
Default variables have predefined values that can be overridden.

| Variable Name | Default Value | Type | Description | Override Method |
|---------------|---------------|------|-------------|-----------------|
| `replicas` | `1` | integer | Default replica count | kustomization.yaml patches |
| `image.pullPolicy` | `IfNotPresent` | string | Image pull policy | deployment.yaml overlay |
| `resources.requests.cpu` | `100m` | string | Default CPU request | resource patches |
| `resources.requests.memory` | `128Mi` | string | Default memory request | resource patches |
| `service.port` | `80` | integer | Default service port | service.yaml overlay |
| `ingress.enabled` | `false` | boolean | Ingress disabled by default | ingress overlay |
| `env.LOG_LEVEL` | `INFO` | string | Default logging level | configmap/env patches |
| `securityContext.runAsNonRoot` | `true` | boolean | Security context default | deployment patches |
| `livenessProbe.initialDelaySeconds` | `30` | integer | Health check delay | deployment patches |
| `readinessProbe.initialDelaySeconds` | `5` | integer | Readiness check delay | deployment patches |

---

## Configuration Structure

### Base Configuration
```
base/
├── deployment.yaml
├── service.yaml
├── configmap.yaml
├── kustomization.yaml
└── namespace.yaml
```

### Overlay Structure
```
overlays/
├── development/
│   ├── kustomization.yaml
│   ├── deployment-patch.yaml
│   └── configmap-patch.yaml
├── staging/
│   ├── kustomization.yaml
│   ├── deployment-patch.yaml
│   └── ingress.yaml
└── production/
    ├── kustomization.yaml
    ├── deployment-patch.yaml
    ├── hpa.yaml
    └── ingress.yaml
```

---

## Variable Sources and Transformation

### Input Sources
1. **Environment Variables**: Injected during CI/CD pipeline
2. **ConfigMaps**: Stored configuration data
3. **Secrets**: Sensitive configuration data
4. **Kustomization Patches**: Override and modification files
5. **External Files**: JSON/YAML files referenced in kustomization

### Transformation Methods
1. **Strategic Merge Patches**: Merge overlays with base configurations
2. **JSON 6902 Patches**: Precise field modifications
3. **Replacements**: String/value substitutions
4. **Generators**: Dynamic resource generation
5. **Transformers**: Custom field transformations

---

## Environment-Specific Configurations

### Development Environment
| Variable | Value | Purpose |
|----------|-------|---------|
| `replicas` | `1` | Single instance for dev |
| `resources.limits.memory` | `256Mi` | Lower resource limits |
| `env.LOG_LEVEL` | `DEBUG` | Verbose logging |
| `ingress.enabled` | `false` | No external access |

### Staging Environment
| Variable | Value | Purpose |
|----------|-------|---------|
| `replicas` | `2` | Minimal redundancy |
| `resources.limits.memory` | `512Mi` | Production-like resources |
| `env.LOG_LEVEL` | `INFO` | Standard logging |
| `ingress.enabled` | `true` | External testing access |

### Production Environment
| Variable | Value | Purpose |
|----------|-------|---------|
| `replicas` | `3` | High availability |
| `resources.limits.memory` | `1Gi` | Full resource allocation |
| `env.LOG_LEVEL` | `WARN` | Minimal logging |
| `ingress.enabled` | `true` | Public access |

---

## Validation and Compliance

### Required Validations
- [ ] All input variables have defined types and constraints
- [ ] Default values are specified for optional variables
- [ ] Output variables are properly documented
- [ ] Static variables are consistent across environments
- [ ] Sensitive variables are stored in Secrets

### Compliance Checklist
- [ ] Variable naming follows kebab-case convention
- [ ] All variables are documented with descriptions
- [ ] Resource limits and requests are defined
- [ ] Security contexts are properly configured
- [ ] Health checks are implemented with appropriate defaults

---

## References
- [Kustomize Official Documentation](https://kustomize.io/)
- [Kubernetes Configuration Best Practices](https://kubernetes.io/docs/concepts/configuration/)
- [GP Kubernetes Standards Document](internal-link)

---

*Last Updated: October 9, 2025*  
*Document Version: 1.0*  
*Maintained by: Infrastructure Team*
