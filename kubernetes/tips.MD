- Get cluster name
```
$ kubectl config view -o jsonpath='{"Cluster name\tServer\n"}{range .clusters[*]}{.name}{"\t"}{.cluster.server}{"\n"}{end}'
```
- Enable master node to run pods
```
$ kubectl describe node master | grep Taints
Taints:             node-role.kubernetes.io/control-plane:NoSchedule
$ kubectl taint nodes master node-role.kubernetes.io/control-plane-
```

- Get current configuration
```
kubectl get --raw "/api/v1/nodes/master/proxy/configz" | jq
```
