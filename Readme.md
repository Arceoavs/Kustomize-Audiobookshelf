# Audiobookshelf Kubernetes Deployment

This repository contains Kubernetes resources for deploying a Audiobookshelf service. More about Audiobookshelf can be found at https://github.com/advplyr/audiobookshelf.

## Prerequisites and Assumptions

The configuration was tested on a `k3s` cluster running on AMD64 architecture. It should work on any Kubernetes cluster that meets the following requirements:

- Domain name pointing to the cluster
- The NGINX Ingress Controller installed on the cluster (https://kubernetes.github.io/ingress-nginx/deploy/)
- This configuration uses the local-path provisioner to create a PersistentVolumeClaim. If you do not have the local-path provisioner installed, you can install it using the following command:

```bash
kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml
```

or you can use a different provisioner by editing the `pvc.yaml` file in the `base` directory.

## Contents

The repository contains the following resources:

- `base` directory: This directory contains the base resources for the service, including a Deployment, a Service, a Namespace, and a PersistentVolumeClaim. The ingress resource is not included in the base directory because it is environment-specific. The base directory also contains a `kustomization.yaml` file that defines the resources to be deployed.
- `overlays` directory: This directory contains overlays for the service. A template for a `production` overlay is included in the repository. You can create additional overlays for other environments by copying the `production` directory and editing the `kustomization.yaml` file in the new directory.

### Note about the Ingress resource

Uploading large files to the Audiobookshelf service can cause the NGINX Ingress Controller to time out. To avoid this, you can increase the timeout value for the Ingress Controller by adding the following annotations to the Ingress resource:

```yaml
nginx.ingress.kubernetes.io/proxy-body-size: "0"
nginx.ingress.kubernetes.io/proxy-read-timeout: "600"
nginx.ingress.kubernetes.io/proxy-send-timeout: "600"
```

These values are set in the `ingress.yaml` file in the `production` overlay directory and can be adjusted as needed.
More information about this issue can be found in the Ingress Controller documentation: https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#custom-max-body-size

## Usage

To deploy the service, follow these steps:

1. Install Kustomize: https://kubectl.docs.kubernetes.io/installation/kustomize/
2. Clone this repository

   ```bash
   git clone <repo-url>
   ```

3. Navigate to the overlays directory for your desired environment

   ```bash
    cd overlays/<environment>
   ```

4. Customize the resources in the overlay directory as needed by editing the corresponding `kustomization.yaml` file.

5. Deploy the resources using Kustomize

   ```bash
   kustomize build . | kubectl apply -f -
    #OR
    kubectl apply -k .
   ```

6. Verify that the resources were deployed successfully

   ```bash
   kubectl get all -n audiobookshelf
   ```

## Contributing

Contributions to the repository are welcome.
