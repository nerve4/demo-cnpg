# Demo-cnpg

## Summary

PostgreSQL up-and-running with [CloudNativePG](https://cloudnative-pg.io/) in k8s.


## Install CNPG

```
helm repo add cnpg https://cloudnative-pg.github.io/charts

helm repo update

helm upgrade --install cnpg cnpg/cloudnative-pg \
    --namespace cnpg-system --create-namespace --wait
```
Output:
```
NAME: cnpg
LAST DEPLOYED: Tue May 23 08:46:44 2023
NAMESPACE: cnpg-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CloudNativePG operator should be installed in namespace "cnpg-system".
You can now create a PostgreSQL cluster with 3 nodes in the current namespace as follows:

cat <<EOF | kubectl apply -f -
# Example of PostgreSQL cluster
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata:
  name: cluster-example
spec:
  instances: 3
  storage:
    size: 1Gi
EOF

kubectl get cluster
```


## Install Prometheus

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update

helm upgrade --install prometheus-community \
    prometheus-community/kube-prometheus-stack \
    --namespace observability --create-namespace \
    --values https://raw.githubusercontent.com/cloudnative-pg/cloudnative-pg/main/docs/src/samples/monitoring/kube-stack-config.yaml \
    --wait
```
Check the status with:
```
kubectl --namespace observability get pods -l "release=prometheus-community"
```


## Install Grafana Dashboard

```
kubectl --namespace observability apply \
    --filename https://raw.githubusercontent.com/cloudnative-pg/cloudnative-pg/main/docs/src/samples/monitoring/grafana-configmap.yaml
```


## Install the Cluster
```
kubectl create ns demo
kubectl apply -f pg-cluster.yaml
```
Check the results:
```
kubectl get po -n demo
kubectl get svc -n demo
kubectl get secrets -n demo
```