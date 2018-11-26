# HELM management

## Prerequisites

- Helm client installation: <https://docs.helm.sh/using_helm/#installing-helm>

## Tiller initialisation

We will:

- create Tiller clients and servers CA and certificates
- create Tiller Service account and Cluster Role Binding
- intialise Tiller using previously generated certificates, RBAC and specifying secret storage
- configure Helm client to connect to Tiller

Generate a Certificate Authority

```bash
openssl genrsa -out ./ca.key.pem 4096
openssl req -key ca.key.pem -new -x509 -days 7300 -sha256 -out ca.cert.pem -extensions v3_ca
```

Generating Certificates

```bash
openssl genrsa -out ./tiller.key.pem 4096
openssl genrsa -out ./helm.key.pem 4096
openssl req -key tiller.key.pem -new -sha256 -out tiller.csr.pem
openssl req -key helm.key.pem -new -sha256 -out helm.csr.pem
openssl x509 -req -CA ca.cert.pem -CAkey ca.key.pem -CAcreateserial -in tiller.csr.pem -out tiller.cert.pem -days 1095
openssl x509 -req -CA ca.cert.pem -CAkey ca.key.pem -CAcreateserial -in helm.csr.pem -out helm.cert.pem  -days 1095
```

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
helm init \
  --service-account tiller \
  --override 'spec.template.spec.containers[0].command'='{/tiller,--storage=secret}' \
  --tiller-tls \
  --tiller-tls-verify \
  --tiller-tls-cert ./tiller.cert.pem \
  --tiller-tls-key ./tiller.key.pem \
  --tls-ca-cert ca.cert.pem
```

Configure Helm client to use certs

```bash
cp ca.cert.pem $(helm home)/ca.pem
cp helm.cert.pem $(helm home)/cert.pem
cp helm.key.pem $(helm home)/key.pem
```

Test helm connection

```bash
helm ls # will through an error "Error: transport is closing"
helm ls --tls # will not trhough anything at this point
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

## Reference

- [Securing your Helm Installation](https://github.com/helm/helm/blob/master/docs/securing_installation.md)
- [Using SSL Between Helm and Tiller](https://github.com/helm/helm/blob/master/docs/tiller_ssl.md)

## Want more

You have a project? Want to discuss? Contact me at [hello@onmyown.io](hello@onmyown.io)