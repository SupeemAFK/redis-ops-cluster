## Example usage of redis operator
This is repo is example of how to use redis on kubernetes both architectures redis sentinel for High Availabilty (HA) with failover and redis cluster for High Scalabity.

### 1. Redis cluster (ops-tree)
#### Install ops-tree
```
helm repo add ot-helm https://ot-container-kit.github.io/helm-charts

helm repo update
```
```
helm install redis-operator ot-helm/redis-operator
```
#### Create redis cluster
```
kubectl apply -f redis-cluster.yaml
```

### 2. Redis sentinel (spotahome)
#### Install spotahome
```
helm repo add redis-operator https://spotahome.github.io/redis-operator

helm repo update
```
```
helm install redis-operator redis-operator/redis-operator
```
#### Create redis sentinel for failover
```
kubectl apply -f redis-failover.yaml
```