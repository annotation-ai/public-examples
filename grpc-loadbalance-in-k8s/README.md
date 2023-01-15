This example is for 


## Prerequisites

- Install [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- Install [Helm](https://helm.sh/docs/intro/install/)
- Install kubectl([Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)/ [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/) / [MacOS](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/))

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


## Deploy grpc example pod

```
make grpc-example
```

Check `go-grpc-greeter-server`

```
kubectl get pod
```

```
NAME                                      READY   STATUS    RESTARTS   AGE
go-grpc-greeter-server-674d77c58c-pzhmh   1/1     Running   0          3m38s
traefik-78bbd5b657-ksdxg                  1/1     Running   0          48m
```

## Request

Before request, make tunnel on minikube to creates a route to services deployed with type LoadBalancer.

```
make tunnel
```

Run request on an another terminal.

```
make request
```



