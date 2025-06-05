# pgAdmin 4 Helm Chart

This chart bootstraps a pgAdmin 4 deployment on a Kubernetes cluster using the Helm package manager.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- PV provisioner support in the underlying infrastructure (if persistence is enabled)

## Installing the Chart

To install the chart with the release name `my-pgadmin`:

```bash
helm install my-pgadmin ./pgadmin
```

The command deploys pgAdmin on the Kubernetes cluster in the default configuration. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `my-pgadmin` deployment:

```bash
helm delete my-pgadmin
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Known Issues

### pkg_resources Deprecation Warning

You may see the following warning in the pgAdmin container logs:

```
/venv/lib/python3.12/site-packages/passlib/pwd.py:16: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
```

This is a known warning from the passlib library used by pgAdmin and does not affect functionality. It will be resolved in future pgAdmin releases when the underlying libraries are updated.

## Parameters

### Global parameters

| Name               | Description                                     | Value |
| ------------------ | ----------------------------------------------- | ----- |
| `nameOverride`     | String to partially override pgadmin.fullname  | `""`  |
| `fullnameOverride` | String to fully override pgadmin.fullname      | `""`  |

### pgAdmin Configuration

| Name                                    | Description                                           | Value                  |
| --------------------------------------- | ----------------------------------------------------- | ---------------------- |
| `pgadmin.defaultEmail`                  | Default admin email (required)                        | `admin@example.com`    |
| `pgadmin.defaultPassword`               | Default admin password (required)                     | `SuperSecret`          |
| `pgadmin.enableTLS`                     | Enable TLS (container listens on port 443)           | `false`                |
| `pgadmin.listenAddress`                 | Listen address (leave empty for defaults)             | `nil`                  |
| `pgadmin.listenPort`                    | Custom listen port (leave empty for defaults)         | `nil`                  |
| `pgadmin.disablePostfix`                | Disable Postfix mail server                           | `true`                 |
| `pgadmin.serverJsonFile`                | Server configuration file path                        | `/pgadmin4/servers.json` |
| `pgadmin.replaceServersOnStartup`       | Replace servers on startup                            | `false`                |
| `pgadmin.preferencesJsonFile`           | Preferences configuration file path                   | `/pgadmin4/preferences.json` |
| `pgadmin.config`                        | pgAdmin configuration environment variables           | `{}`                   |
| `pgadmin.gunicorn.accessLogFile`        | Gunicorn access log file                              | `"-"`                  |
| `pgadmin.gunicorn.limitRequestLine`     | Gunicorn request line limit                           | `8190`                 |
| `pgadmin.gunicorn.threads`              | Gunicorn threads                                      | `25`                   |

### Image parameters

| Name                | Description                                    | Value            |
| ------------------- | ---------------------------------------------- | ---------------- |
| `image.repository`  | pgAdmin image repository                       | `dpage/pgadmin4` |
| `image.tag`         | pgAdmin image tag (immutable tags recommended) | `""`             |
| `image.pullPolicy`  | pgAdmin image pull policy                      | `IfNotPresent`   |
| `imagePullSecrets`  | pgAdmin image pull secrets                     | `[]`             |

### Deployment parameters

| Name                        | Description                                          | Value   |
| --------------------------- | ---------------------------------------------------- | ------- |
| `replicaCount`              | Number of pgAdmin replicas to deploy                | `1`     |
| `podAnnotations`            | Annotations for pgAdmin pods                         | `{}`    |
| `podLabels`                 | Extra labels for pgAdmin pods                        | `{}`    |
| `podSecurityContext.fsGroup`| Group ID for the container                           | `5050`  |
| `securityContext`           | Security context for the pgAdmin container          | See values.yaml |

### Service parameters

| Name                  | Description                        | Value       |
| --------------------- | ---------------------------------- | ----------- |
| `service.type`        | pgAdmin service type               | `ClusterIP` |
| `service.port`        | pgAdmin service HTTP port          | `80`        |
| `service.targetPort`  | pgAdmin container HTTP port        | `80`        |

### Ingress parameters

| Name                       | Description                                | Value                    |
| -------------------------- | ------------------------------------------ | ------------------------ |
| `ingress.enabled`          | Enable ingress record generation           | `false`                  |
| `ingress.className`        | IngressClass that will be used             | `""`                     |
| `ingress.annotations`      | Additional annotations for the Ingress     | `{}`                     |
| `ingress.hosts`            | An array with hosts and paths              | `[{"host": "pgadmin.example.local", "paths": [{"path": "/", "pathType": "ImplementationSpecific"}]}]` |
| `ingress.tls`              | TLS configuration for additional hostnames | `[]`                     |

### Persistence parameters

| Name                        | Description                                   | Value               |
| --------------------------- | --------------------------------------------- | ------------------- |
| `persistence.enabled`       | Enable persistence using Persistent Volume Claims | `true`         |
| `persistence.existingClaim` | Name of an existing PVC to use                | `""`                |
| `persistence.storageClass`  | Storage class of backing PVC                  | `""`                |
| `persistence.accessMode`    | Persistent Volume access mode                 | `ReadWriteOnce`     |
| `persistence.size`          | Persistent Volume size                        | `5Gi`               |
| `persistence.annotations`   | Additional PVC annotations                    | `{}`                |

### Resource parameters

| Name                        | Description                        | Value     |
| --------------------------- | ---------------------------------- | --------- |
| `resources.limits.cpu`      | The CPU resource limits            | `500m`    |
| `resources.limits.memory`   | The memory resource limits         | `512Mi`   |
| `resources.requests.cpu`    | The requested CPU resources        | `250m`    |
| `resources.requests.memory` | The requested memory resources     | `256Mi`   |

### Autoscaling parameters

| Name                                            | Description                                                                | Value   |
| ----------------------------------------------- | -------------------------------------------------------------------------- | ------- |
| `autoscaling.enabled`                           | Enable Horizontal POD autoscaling                                          | `false` |
| `autoscaling.minReplicas`                       | Minimum number of pgAdmin replicas                                         | `1`     |
| `autoscaling.maxReplicas`                       | Maximum number of pgAdmin replicas                                         | `3`     |
| `autoscaling.targetCPUUtilizationPercentage`    | Target CPU utilization percentage                                          | `80`    |
| `autoscaling.targetMemoryUtilizationPercentage` | Target Memory utilization percentage                                       | `""`    |

### Health check parameters

| Name                                    | Description                                           | Value           |
| --------------------------------------- | ----------------------------------------------------- | --------------- |
| `livenessProbe.httpGet.path`            | Path for the HTTP liveness probe                      | `/misc/ping`    |
| `livenessProbe.httpGet.port`            | Port for the HTTP liveness probe                      | `http`          |
| `livenessProbe.initialDelaySeconds`     | Initial delay seconds for livenessProbe               | `30`            |
| `livenessProbe.periodSeconds`           | Period seconds for livenessProbe                      | `10`            |
| `livenessProbe.timeoutSeconds`          | Timeout seconds for livenessProbe                     | `5`             |
| `livenessProbe.failureThreshold`        | Failure threshold for livenessProbe                   | `3`             |
| `readinessProbe.httpGet.path`           | Path for the HTTP readiness probe                     | `/misc/ping`    |
| `readinessProbe.httpGet.port`           | Port for the HTTP readiness probe                     | `http`          |
| `readinessProbe.initialDelaySeconds`    | Initial delay seconds for readinessProbe              | `10`            |
| `readinessProbe.periodSeconds`          | Period seconds for readinessProbe                     | `5`             |
| `readinessProbe.timeoutSeconds`         | Timeout seconds for readinessProbe                    | `3`             |
| `readinessProbe.failureThreshold`       | Failure threshold for readinessProbe                  | `3`             |

## Configuration and installation details

### Setting up admin credentials

You must provide the initial admin email and password. These can be set in the values.yaml file:

```yaml
pgadmin:
  defaultEmail: "admin@mydomain.com"
  defaultPassword: "MySecretPassword123"
```

**Important**: In production, consider using external secrets management instead of storing passwords in values.yaml.

### Persistence

By default, pgAdmin data is persisted using PVC. You can disable persistence by setting `persistence.enabled=false`.

### Ingress

To enable ingress, set `ingress.enabled=true` and configure the hostname:

```yaml
ingress:
  enabled: true
  className: "nginx"
  hosts:
    - host: pgadmin.mydomain.com
      paths:
        - path: /
          pathType: ImplementationSpecific
```

### Security

The chart configures security contexts to run pgAdmin as the pgadmin user (UID: 5050) for better security.

### Configuration Options

You can set pgAdmin configuration options using the `pgadmin.config` section:

```yaml
pgadmin:
  config:
    ENHANCED_COOKIE_PROTECTION: "True"
    LOGIN_BANNER: "Authorized users only!"
    CONSOLE_LOG_LEVEL: "10"
```

These will be automatically prefixed with `PGADMIN_CONFIG_`.

## Upgrading

To upgrade the chart:

```bash
helm upgrade my-pgadmin ./pgladmin
```

## Troubleshooting

### Common Issues

1. **Pod stuck in Pending state**: Check if PVC can be provisioned
2. **Authentication errors**: Verify defaultEmail and defaultPassword are set correctly
3. **Permission denied errors**: Ensure securityContext is properly configured

### Logs

To view pgAdmin logs:

```bash
kubectl logs -f deployment/my-pgadmin
```
