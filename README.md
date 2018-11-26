# HELM initialization

## Prerequisites

- Helm client installation: <https://docs.helm.sh/using_helm/#installing-helm>

## Tiller initialisation

Create "tiller" service account

```bash
kubectl apply -f ServiceAccount.yaml
```

Create cluster role binding

```bash
kubectl apply -f ClusterRoleBinding.yaml
```

Initialize Tiller

```bash
helm init --service-account tiller --override 'spec.template.spec.containers[0].command'='{/tiller,--storage=secret}'
```

## Tiller unistallation

Remove the Tiller component from the K8s cluster

```bash
helm reset --force
```

Delete cluster role binding

```bash
kubectl delete -f ClusterRoleBinding.yaml
```

Delete "tiller" service account

```bash
kubectl delete -f ServiceAccount.yaml
```

## Bonus

Install autocompletion for terraform:

```bash
echo "source <(helm completion bash)" >> ~/.bashrc
```