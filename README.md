## Example usage of redis operator
This repo is example of how to use redis on kubernetes with both architectures redis sentinel for High Availabilty (HA) with failover and redis cluster for High Scalabity.

### [Redis cluster (ops-tree)](https://example.com)

![Architecture](https://github.com/OT-CONTAINER-KIT/redis-operator/raw/main/static/redis-operator-architecture.png)
#### Install ops-tree
```
helm repo add ot-helm https://ot-container-kit.github.io/helm-charts

helm repo update
```
```
helm install redis-operator ot-helm/redis-operator -n redis-operator --create-namespace
```

### Example Volumes
```
kubectl apply -f redis/redis-storage.yaml
```
This create 6 persistent volumes with 1Gi each.

#### Create redis cluster
```
kubectl apply -f redis/redis-cluster.yaml -n redis-operator
```
This will create redis cluster and pvc bound to earlier pv.

#### Checking
```
kubectl get pods -n redis-operator
```
You should see 3 follower and 3 leader.
```
kubectl get svc -n redis-operator
```
Use **redis-cluster-master** for consumation.
```
kubectl get pvc -n redis-operator
```
Check bounded pvc for redis-cluster.

#### Example usage with redis-cli
##### 1. Spin up temp pod for redis-cli
```
kubectl run -it --rm redis-cli -n redis-operator --image=redis:alpine -- sh
```
##### 2. Inside use redis-cli command
```
redis-cli -c -h redis-cluster-master 
```
##### 3. Set and Get values
```
Set Hello "World!"
```
```
Get Hello
# "World!"
```

### Example usage with redis nodejs
```
import { createCluster } from 'redis';
import 'dotenv/config';

const cluster = createCluster({
    rootNodes: [
        { url: process.env.REDIS_URL || 'redis:redis-cluster-master.redis-operator.svc.cluster.local:6379' }
    ],
    defaults: {
        password: process.env.REDIS_PASSWORD,
    }
});

cluster.on('error', (err) => console.error('Redis Cluster Error:', err));

export const connectRedis = async () => {
    if (!cluster.isOpen) {
        await cluster.connect();
        console.log('Connected to Redis Cluster');
    }
};

export default cluster;
```