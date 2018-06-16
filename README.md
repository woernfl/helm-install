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
helm init --service-account tiller
```

## Bonus

Install autocompletion for terraform:

```bash
echo "source <(helm completion bash)" >> ~/.bashrc
```