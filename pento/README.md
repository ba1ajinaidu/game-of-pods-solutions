# Challenge
We have 2 nodes(master and node01), both nodes doesn't work properly. We have to debug the problems and fix them. And then deploy the file-server on the cluster.

## Architecture
![pento](https://i.imgur.com/QUWgCxt.jpg)


First we have to debug the nodes and fix them.<br/>
`kubectl get nodes` throws a error `Unable to connect to the server: x509: certificate signed by unknown authority`.<br/>
So there's some issue with the certificate in the kube-api server. On checking the logs of kube-api server docker container we see a error 
``` bash
error: unable to load client CA file: unable to load client CA file: open /etc/kubernetes/pki/ca-authority.crt: no such file or directory
```

On checking the `kube-apiserver.yaml` file in /etc/kubernetes/manifest we see client-ca entry is named as `ca-authority.cert`, rename it to `ca.cert` this solves the issue with certificate.
Still the api-server doesn't work. On running `netstat -tulpn | grep LISTEN` lists all the ports which are listening to traffic, we see that apiserver is not listening on 6443, which is the default port on which apiserver listens on.
Edit the `.kube/config` file to change the port to 6443. Now the apiserver works fine.

One more issue in master node is coredns, coredns pods are created in kube-system namespace. Listing the pods in kube-system namespace(`kubectl get pods -n kube-system`) shows the coredns status as  ImagePullBackoff, edit the deployment(` kubectl edit deployment coredns -n kube-system`) to change the container image from kubedns to coredns.
Now the master node is fixed.


`kubectl get nodes` shows that node01 as "Ready,SchedulingDisabled". This happens when a node is cordoned, so running `kubectl uncordon node01` fixes the node01 node.
Now both the nodes are ready we can deploy the file-server with the given specifications.

## Components created
1. Persistent volume
2. persistent volume claim
3. pod
4. service
