# MetalLB Setup in Kubernetes

MetalLB hooks into your Kubernetes cluster, and provides a network load-balancer implementation. In short, it allows you to create Kubernetes services of type `LoadBalancer` in clusters that don’t run on a cloud provider, and thus don’t have native load balancers.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Namespace and Security Settings](#namespace-and-security-settings)
- [Configuring kube-proxy](#configuring-kube-proxy)
- [Installing MetalLB](#installing-metallb)
- [Configuring IP Address Pool](#configuring-ip-address-pool)
- [Apply Configuration](#apply-configuration)
- [Verification](#verification)

## Prerequisites

- A Kubernetes cluster
- kubectl configured to communicate with your cluster
- Helm (optional for installation)

## Namespace and Security Settings

First, create the `metallb-system` namespace and set the necessary security settings.

```bash
kubectl create ns metallb-system
kubectl label namespace metallb-system pod-security.kubernetes.io/enforce=privileged
kubectl label namespace metallb-system pod-security.kubernetes.io/audit=privileged
kubectl label namespace metallb-system pod-security.kubernetes.io/warn=privileged
```

## Configuring kube-proxy

Next, you need to configure `kube-proxy` to work with MetalLB:

1. Edit the `kube-proxy` ConfigMap:

    ```bash
    kubectl edit configmap -n kube-system kube-proxy
    ```

2. Set `mode` to "ipvs" and `strictARP` to true:

    ```yaml
    apiVersion: kubeproxy.config.k8s.io/v1alpha1
    kind: KubeProxyConfiguration
    mode: "ipvs"
    ipvs:
      strictARP: true
    ```

3. Save and exit.

Alternatively, you can automate this configuration change:

- To preview changes:

  ```bash
  kubectl get configmap kube-proxy -n kube-system -o yaml | \
  sed -e "s/strictARP: false/strictARP: true/" | \
  kubectl diff -f - -n kube-system
  ```

- To apply the changes:

  ```bash
  kubectl get configmap kube-proxy -n kube-system -o yaml | \
  sed -e "s/strictARP: false/strictARP: true/" | \
  kubectl apply -f - -n kube-system
  ```

## Installing MetalLB

You can install MetalLB using Helm:

```bash
helm repo add metallb https://metallb.github.io/metallb
helm install metallb metallb/metallb
```

## Configuring IP Address Pool

Create a file named [layer-two-config.yaml](https://github.com/HarishankarYellapragada/hslabs/blob/main/metallb/layer-two-config.yaml) and copy the content for the file. 

Note: Just change the IP range as you like

## Apply Configuration

Apply the IP address pool configuration to your cluster:

```bash
kubectl apply -f ipaddresspool_metallb.yaml -n metallb-system
```

## Verification

After applying the configuration, you should verify that MetalLB is running and the IP address pool has been successfully configured:

```bash
kubectl get pods -n metallb-system
kubectl get ipaddresspool -n metallb-system
```

You should see the MetalLB pods running and the IP address pool you configured.
