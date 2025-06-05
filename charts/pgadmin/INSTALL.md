# pgAdmin Installation Guide

## Quick Start

1. **Install with default values:**
   ```bash
   helm install my-pgadmin ./pgadmin
   ```

2. **Install with custom values:**
   ```bash
   helm install my-pgadmin ./pgadmin -f values.production.yaml
   ```

3. **Access pgAdmin:**
   ```bash
   kubectl port-forward svc/my-pgadmin 8080:80
   ```
   Then open http://localhost:8080

## Production Installation

1. **Copy and customize the production values:**
   ```bash
   cp values.production.yaml my-values.yaml
   # Edit my-values.yaml with your specific configuration
   ```

2. **Install with production values:**
   ```bash
   helm install pgadmin ./pgadmin -f my-values.yaml
   ```

## Configuration Examples

### Enable Ingress
```yaml
ingress:
  enabled: true
  className: "nginx"
  hosts:
    - host: pgadmin.example.com
      paths:
        - path: /
          pathType: ImplementationSpecific
```

### Custom pgAdmin Settings
```yaml
pgadmin:
  config:
    ENHANCED_COOKIE_PROTECTION: "True"
    LOGIN_BANNER: "Company Internal Use Only"
    SESSION_EXPIRATION_TIME: "8"
```

### High Availability Setup
```yaml
replicaCount: 2
autoscaling:
  enabled: true
  minReplicas: 2
  maxReplicas: 5
  targetCPUUtilizationPercentage: 70
```

## Upgrading

```bash
helm upgrade pgadmin ./pgadmin -f my-values.yaml
```

## Uninstalling

```bash
helm uninstall pgadmin
```

**Note:** PVC will remain. Delete manually if needed:
```bash
kubectl delete pvc data-pgadmin-0
```
