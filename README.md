Monitoring Application

This is a simple monitoring application taken from the following repositor: https://github.com/AnaisUrlichs/observe-argo-rollout 

The presentation for the other demo is linked [here](https://youtu.be/Z8hfs_CN1EY).

# Prerequisites

* 1 Kubernetes Cluster -- this can be any Kubernetes cluster
* Helm installed

# Installation

Create a new namespace
```
kubectl create ns demo
```

Install monitoring stack

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update 

helm install prom prometheus-community/kube-prometheus-stack -n demo --values monitoring/values.yaml
```

Install application resources
```
kubectl apply -n demo -f ./application
```

Look at the metrics of the application
```
kubectl port-forward service/app -n demo 8080:8080
```

And then open the following:
http://localhost:8080/metrics

Install pinger application to generate traffic on our application
```
kubectl apply -n demo -f ./pinger
```

Port-forward Prometheus
```
kubectl port-forward service/prom-kube-prometheus-stack-prometheus -n demo 9090:9090
```

http://localhost:9090/

Port-forward Grafana
```
kubectl port-forward service/prom-grafana -n demo 3000:8080
```

http://localhost:3000/

### Install Promtail and Loki

Promtail
```
helm upgrade --install promtail grafana/promtail -f monitoring/promtail-values.yaml -n monitoring
```

Loki 
```
helm upgrade --install loki grafana/loki-distributed -n monitoring
```