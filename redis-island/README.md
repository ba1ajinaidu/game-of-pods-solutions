## Challenge
The Kingdom of Redis Islands needs reinforcement! Build a highly available Redis Cluster based on the below architecture.

## Architecture
![redis-island](https://i.imgur.com/haemvGm.jpg)

## Solution
A simple redis cluster requires atleast 3 master nodes and 3 worker nodes, so the main aim is to setup a simple redis-cluster using StatefulSet kubernetes object and create a service endpoint to access the redis cluster.
</br>
* First we ssh into node01 and create /redis01, /redis02 ... directories as host directories to create persistent volume.
* Now we'll create persistent volume using `pv.yaml`.
* Then create the StatefulSet using `redis-statefulset.yaml`
* Atlast create the service.
* Finally execute the given command, 
``` bash
kubectl exec -it redis-cluster-0 -- redis-cli --cluster create --cluster-replicas 1 $(kubectl get pods -l app=redis-cluster -o jsonpath='{range.items[*]}{.status.podIP}:6379 ')
```
 
