# K8s-Microservices-Deployment

This repository contains Kubernetes configuration files for deploying a microservices-based application. The application consists of multiple services including user management, product management, order processing, and more.

## Project Structure

```
.
├── configserver/           # Configuration Server Service
├── gateway/                # API Gateway Service
├── mysql/                  # MySQL Database Configuration
├── orderservice/           # Order Management Service
├── productservice/         # Product Management Service
├── serviceregistry/        # Service Registry (Eureka)
└── userservice/            # User Management Service
```

## Services Overview

### Config Server
- `deployment.yaml`: Defines the deployment configuration
- `service.yaml`: Exposes the config server service
- `hpa.yaml`: Horizontal Pod Autoscaling configuration

### API Gateway
- `deployment.yml`: Gateway deployment configuration
- `service.yaml`: Exposes the gateway service
- `hpa.yaml`: Autoscaling configuration for the gateway

### MySQL Database
- `configMap.yaml`: Database configuration settings
- `deployment.yaml`: MySQL deployment configuration
- `secret.yaml`: Sensitive data storage (credentials)
- `pv.yaml`: Persistent Volume configuration
- `pvc.yaml`: Persistent Volume Claim configuration
- `hpa.yaml`: Autoscaling settings

### Microservices
Each service (order, product, user) contains:
- `deployment.yaml`: Service deployment configuration
- `service.yaml`: Service exposure configuration
- `hpa.yaml`: Autoscaling settings

## Prerequisites

- Kubernetes cluster (local or cloud-based)
- kubectl CLI tool
- Helm (optional, for package management)
- Metrics Server (required for HPA functionality)

## Initial Setup

1. Install Metrics Server (required for HPA to work):
```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

2. Verify Metrics Server installation:
```bash
kubectl get deployment metrics-server -n kube-system
```

## Deployment Instructions

1. First, deploy the MySQL database:
```bash
kubectl apply -f mysql/
```

2. Deploy the Config Server:
```bash
kubectl apply -f configserver/
```

3. Deploy the Service Registry:
```bash
kubectl apply -f serviceregistry/
```

4. Deploy the remaining microservices:
```bash
kubectl apply -f userservice/
kubectl apply -f productservice/
kubectl apply -f orderservice/
```

5. Finally, deploy the API Gateway:
```bash
kubectl apply -f gateway/
```

## Configuration

### Database Configuration
- Database configurations are stored in `mysql/configMap.yaml`
- Sensitive data is stored in `mysql/secret.yaml`
- Persistent storage is configured via `pv.yaml` and `pvc.yaml`

### Metrics Server Configuration
The Metrics Server is required for collecting resource metrics from Kubelets and exposing them through the Kubernetes metrics API. This enables:
- Horizontal Pod Autoscaling
- Resource usage monitoring
- Performance monitoring

### Autoscaling
All services are configured with Horizontal Pod Autoscaling (HPA) for automatic scaling based on CPU/Memory usage. To use HPA:
1. Ensure Metrics Server is running
2. Check HPA status: `kubectl get hpa`
3. Monitor resource metrics: `kubectl top pods`

## Service Dependencies

1. Metrics Server must be running for HPA functionality
2. Config Server must be running before other services
3. Service Registry should be available for service discovery
4. MySQL database must be running before the microservices
5. Microservices can be deployed in any order after the above
6. API Gateway should be deployed last

## Monitoring and Maintenance

- Monitor the pods: `kubectl get pods`
- Check services: `kubectl get services`
- View logs: `kubectl logs <pod-name>`
- Scale manually: `kubectl scale deployment/<deployment-name> --replicas=<number>`
- Check resource usage: `kubectl top pods`
- Monitor HPA: `kubectl get hpa --watch`

## Troubleshooting

Common issues and solutions:

1. If pods are not starting, check events:
```bash
kubectl get events
```

2. For service connectivity issues:
```bash
kubectl describe service <service-name>
```

3. For pod issues:
```bash
kubectl describe pod <pod-name>
```

4. For Metrics Server issues:
```bash
# Check Metrics Server status
kubectl get pods -n kube-system | grep metrics-server
# Check Metrics Server logs
kubectl logs -n kube-system deployment/metrics-server
# Verify metrics availability
kubectl top nodes
kubectl top pods
```

5. For HPA issues:
```bash
# Check HPA status
kubectl describe hpa <hpa-name>
# Verify HPA metrics
kubectl get hpa
```

## License

See the LICENSE file in the repository root.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request

## Support

For issues and support, please create an issue in the repository.
