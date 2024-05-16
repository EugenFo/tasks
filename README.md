# Task

Required tools:

- [k3d](https://k3d.io)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [helm](https://helm.sh/)

## Preparation

### Install kubernetes/k3d cluster

`k3d cluster create task -p "80:80@loadbalancer"`

### Create namespaces

```
kubectl create ns monitoring
kubectl create ns apache
```

### Adjust /etc/hosts file

Add the following into your `/etc/hosts` file:

```
127.0.0.1 example-site1.task
127.0.0.1 example-site2.task
127.0.0.1 grafana.task
```

## Installing kube-prometheus-stack with Grafana:

- `helm repo add prometheus-community https://prometheus-community.github.io/helm-charts`
- `helm install -n monitoring -f kube-prometheus-stack/kube-prometheus-stack-values.yaml prometheus prometheus-community/kube-prometheus-stack`

## Installing apache2/httpd:

ConfigMap with the vhosts has to be created before installing apache via Helm. It also takes a little bit of time until apache pod is started.

- `helm repo add bitnami https://charts.bitnami.com/bitnami`
- `kubectl -n apache create -f apache/apache-vhosts-cm.yaml`
- `helm install -n apache -f apache/apache_values.yaml apache bitnami/apache`

## Provisioning Apache Dashboard in Grafana:

- `kubectl -n monitoring create -f kube-prometheus-stack/apache-grafana-dashboard-cm.yaml`

Go to http://grafana.task and enter the default grafana credentials

```
login: admin
password: prom-operator
```

and now you should be able to see the Apache Dashboard

## Delete kubernetes/k3d cluster

`k3d cluster delete task`
