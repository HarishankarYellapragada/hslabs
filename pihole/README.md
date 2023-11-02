# Pi-hole Kubernetes Setup

This repository contains the necessary Kubernetes manifests and Helm configurations to deploy Pi-hole in your cluster. 

## Prerequisites
Ensure you have the following prerequisites installed and configured:
- A running Kubernetes cluster
- `kubectl` CLI tool configured
- Helm v3

## Installation Steps

### 1. Create a Namespace for Pi-hole
```bash
kubectl create namespace pihole
```

### 2. Add Pi-hole Helm Repository
```bash
helm repo add mojo2600 https://mojo2600.github.io/pihole-kubernetes/
helm repo update
```

### 3. Create Admin Password Secret
Create a secret in the Kubernetes cluster to store Pi-hole's admin password:
```bash
kubectl create secret generic pihole-secret --from-literal='password=<ADMIN_PASSWORD>' --namespace pihole
```
*Replace `<ADMIN_PASSWORD>` with your chosen password.*

### 4. Configure Pi-hole
Download the default `values.yaml` file from the Helm chart:
```bash
helm show values mojo2600/pihole > values.yaml
```

Make your changes to the downloaded `values.yaml` file or use a pre-existing configuration file named `my-pihole-values.yaml`. Ensure that you configure the `ServiceWeb`, `ServiceDns`, and specify the use of MetalLB for LoadBalancing. Additionally, set up the necessary Persistent Volumes (PV) and Persistent Volume Claims (PVC).

### 5. Install Pi-hole
Deploy Pi-hole in your Kubernetes cluster using the Helm chart:
```bash
helm install pihole mojo2600/pihole --namespace pihole --values my-pihole-values.yaml
```

## Accessing Pi-hole
After installation, you can access the Pi-hole web interface via the external IP provided by MetalLB. Use the admin password you set earlier to log in.

## Conclusion
You now have Pi-hole running in your Kubernetes cluster, utilizing MetalLB for LoadBalancing and with persistent storage configured. Enjoy your network-wide ad-blocking!
