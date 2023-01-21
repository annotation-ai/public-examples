This example is for 


## Prerequisites

- Install [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- Install [Helm](https://helm.sh/docs/intro/install/)
- Install kubectl([Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)/ [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/) / [MacOS](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/))
- Install [go](https://go.dev/dl/)

## Setup

```
make cluster
```

After minikube cluster is build, check the node and pod.

```
kubectl get node
```

```
NAME   STATUS   ROLES           AGE   VERSION
grpc   Ready    control-plane   13s   v1.24.1
```

```
kubectl get pod
```

```
No resources found in default namespace.
```

## Deploy traefik

```
make traefik
```

Check traefik load balancer is running.

```
NAME                       READY   STATUS    RESTARTS   AGE
traefik-78bbd5b657-ksdxg   1/1     Running   0          22s
```


## Deploy grpc server 

```
make grpc-server
```

Check `go-grpc-greeter-server`

```
kubectl get pod
```

```
NAME                                      READY   STATUS    RESTARTS   AGE
go-grpc-greeter-server-65c4c6f557-6cvxc   1/1     Running   0          13m
go-grpc-greeter-server-65c4c6f557-n27f6   1/1     Running   0          8m57s
traefik-7b59949b6c-7kvj5                  1/1     Running   0          3h38m
```

Check two types of load balancers.

```
kubectl get svc
```

```
NAME                        TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)           AGE
go-grpc-greeter-server      ClusterIP      10.97.255.22     <none>        50051/TCP         4h9m
kubernetes                  ClusterIP      10.96.0.1        <none>        443/TCP           4h51m
l4-go-grpc-greeter-server   LoadBalancer   10.102.165.112   127.0.0.1     50052:30539/TCP   3m42s
traefik                     LoadBalancer   10.102.18.230    127.0.0.1     50051:32488/TCP   3h36m
```

`go-grpc-greeter-server` is L7 load balancer and `l4-go-grpc-greeter-server` is L4 load balancer.


## Request

Before request, make tunnel on minikube to creates a route to services deployed with type LoadBalancer.

```
make tunnel
```

Run request on an another terminal. 

```
make request-to-l4
```

```
2023/01/15 21:27:17 Greeting: Hello world
2023/01/15 21:27:17 Greeting: Hello world
2023/01/15 21:27:17 Greeting: Hello world
2023/01/15 21:27:17 Greeting: Hello world
2023/01/15 21:27:17 Greeting: Hello world
2023/01/15 21:27:17 Greeting: Hello world
```

Check the requests are beging distributed.

```
kubectl logs go-grpc-greeter-server-65c4c6f557-6cvxc
```

```
2023/01/15 12:25:39 Received: world
2023/01/15 12:25:39 Received: world
2023/01/15 12:25:39 Received: world
2023/01/15 12:25:39 Received: world
2023/01/15 12:25:39 Received: world
2023/01/15 12:25:40 Received: world
```

```
kubectl logs go-grpc-greeter-server-65c4c6f557-n27f6
```

```
-> None
```


```
make request-to-l7
```

```
2023/01/15 21:27:17 Greeting: Hello world
2023/01/15 21:27:17 Greeting: Hello world
2023/01/15 21:27:17 Greeting: Hello world
2023/01/15 21:27:17 Greeting: Hello world
2023/01/15 21:27:17 Greeting: Hello world
2023/01/15 21:27:17 Greeting: Hello world
```

Check the requests are beging distributed.

```
kubectl logs go-grpc-greeter-server-65c4c6f557-6cvxc
```

```
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:06 Received: world
```

```
kubectl logs go-grpc-greeter-server-65c4c6f557-n27f6
```

```
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:05 Received: world
2023/01/15 12:30:05 Received: world
```

The requests is distributed well.


## References

- [grpc-go helloworld](https://github.com/grpc/grpc-go/tree/master/examples/helloworld)
- [grpc server](https://kubernetes.github.io/ingress-nginx/examples/grpc/)
