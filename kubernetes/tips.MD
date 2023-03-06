- Get cluster name
```
$ kubectl config view -o jsonpath='{"Cluster name\tServer\n"}{range .clusters[*]}{.name}{"\t"}{.cluster.server}{"\n"}{end}'
```
- Enable master node to run pods
```
$ kubectl taint nodes {master node name} node-role.kubernetes.io/master-
```
