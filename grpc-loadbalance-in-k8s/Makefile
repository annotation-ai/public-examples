PROFILE_NAME=grpc

cluster:
	minikube start driver=docker --profile=$(PROFILE_NAME)
	eval $(minikube docker-env)  # for using host docker images

tunnel:
	minikube tunnel -p $(PROFILE_NAME)

grpc-example:
	docker build -t grpc-server:latest -f grpc-server/Dockerfile .
	minikube image load grpc-server:latest -p $(PROFILE_NAME)
	kubectl apply -f charts/grpc-server/grpc_server.yaml

traefik:
	helm repo add traefik https://helm.traefik.io/traefik
	helm dependency build charts/traefik
	helm install traefik charts/traefik

request:
	pass

